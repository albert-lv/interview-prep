# Day 113 — 设计阻塞队列（Bounded Blocking Queue）+ veRL：Rollout 基础设施与 Agentic RL 工程 🏭

> 📅 2026-09-25 · Week 17 Day 6 · 连续更新第 113 天
>
> 主题：强化学习与 RL 训练工程 🧠 · 收官倒计时：算法已经备齐，现在把它跑成系统

Week 17 的进度条走到了倒数第二天。回看这一周的路：Day 108 打了 MDP / Bellman 的地基，Day 109 上了策略梯度，Day 110 啃了 PPO 这个 RLHF 大杀器，Day 111 用 DPO 偷了 RL 的目标，Day 112 终于摸到本周主角 GRPO——删掉 Critic，组内均值当基线，DeepSeek-R1 用它把开源推理训练推上了头条。

但算法再漂亮，跑不起来就是 PPT。**GRPO 的算力大头根本不在训练，在采样**：每个 prompt 要自回归生成 G 条完整回答，动辄 4k~16k tokens，生成是显存带宽瓶颈，训练是计算瓶颈，两种性格迥异的 workload 挤在同一批 GPU 上。于是有了今天的主角——**veRL（HybridFlow）**，字节开源的 RL 训练框架，R1 复现潮里的事实标准：单控制器编排 + colocate 显存编排 + vLLM/SGLang 分离式 rollout，把"训练-生成"这对欢喜冤家塞进流水线。

今天的算法题「设计阻塞队列」看着朴素，却是整条 rollout 管道的祖师爷：**推理引擎是生产者，训练器是消费者，中间的队列容量就是系统的背压阀门**。能把阻塞队列的条件变量写对，才谈得上去调 veRL 的 partial rollout。

---

## 1) 今日算法题

### 设计阻塞队列（Design Bounded Blocking Queue）

**题意**：实现一个**有界阻塞队列** `BoundedBlockingQueue`，支持多线程并发调用：

- `BoundedBlockingQueue(int capacity)`：初始化队列，容量上限 `capacity`；
- `void enqueue(int element)`：把元素加入队尾；**队列满时阻塞**，直到有空位；
- `int dequeue()`：移除并返回队首元素；**队列空时阻塞**，直到有新元素；
- `int size()`：返回当前元素个数（近似值即可）。

```
输入: capacity = 3
并发行为:
  线程A: enqueue(1), enqueue(2), enqueue(3), enqueue(4) ← 阻塞！
  线程B: dequeue() → 1     ← 线程A 被唤醒，4 入队成功
约束: 1 <= capacity <= 3000；enqueue/dequeue 最多 10⁴ 次调用；不允许使用语言自带的阻塞队列
```

**关键约束**："有界" + "阻塞" + "并发安全" 三件事缺一不可——满了硬塞会丢背压语义，空了就返回会丢数据，不加锁会撕裂内部状态。

### 思路：一把锁 + 两个条件变量（经典中的经典）

核心模型：**互斥锁保护内部状态，两个条件变量分别表达"非空"和"非满"两种等待**。

```
        ┌─────────────────────────────┐
enqueue →│ 满? → notFull.Wait()       │→ 写入尾部 → notEmpty.Signal()
        │ 否 → 直接写                 │
        ├─────────────────────────────┤
dequeue →│ 空? → notEmpty.Wait()      │→ 读头部 → notFull.Signal()
        │ 否 → 直接读                 │
        └─────────────────────────────┘
```

三个必须说对的细节：

1. **`Wait` 必须用 `while` 包，不能用 `if`**——防虚假唤醒（spurious wakeup）。操作系统不保证唤醒你的那一刻条件还成立（别的线程可能抢先消费了刚放进去的元素）。这是面试第一扣分点。
2. **`Signal` 还是 `Broadcast`**：本题一对一配对（放一个元素最多唤醒一个消费者，腾一个空位最多唤醒一个生产者），`Signal` 足够且更快；只有当"一次状态变化可能让多个等待者同时通过"时才需要 `Broadcast`（经典反例：读写锁的读锁释放，一堆读者都能进）。
3. **环形缓冲区**：用 `head` 指针 + `count` 计数实现循环复用数组，`O(1)` 入队出队，无需搬移元素——和 vLLM 的 PagedAttention 把 KV cache 切成 page 复用是一个思想：**固定大小的池子，环形复用，避免动态分配**。

**进阶姿势（面试官加分项）**：高并发场景可以拆成**双锁**（Java `LinkedBlockingQueue` 同款）：`putLock` 管入队 + `notFull`，`takeLock` 管出队 + `notEmpty`，生产者和消费者不再互掐，吞吐接近翻倍；代价是 `size()` 需要原子计数器，逻辑复杂度上一个台阶。面试说得出这个 trade-off 就够了，手写还是写单锁版。

> 💡 **连接 veRL**：GRPO 的 rollout pipeline 就是一条巨型阻塞队列——vLLM worker 生产完成的 rollout，trainer 消费样本做更新。`capacity` 这个参数就是**背压（backpressure）**：队列满了，rollout 引擎就该降速，而不是把样本堆爆显存。Day 112 说的 partial rollout，本质就是在队列满时"先到的样本先训练"的调度策略。

### 代码

**Go（channel 版——工程首选，3 行讲完）：**

```go
type BoundedBlockingQueue struct {
    ch chan int
}

func Constructor(capacity int) BoundedBlockingQueue {
    return BoundedBlockingQueue{ch: make(chan int, capacity)}
}

func (q *BoundedBlockingQueue) Enqueue(element int) { q.ch <- element }
func (q *BoundedBlockingQueue) Dequeue() int        { return <-q.ch }
func (q *BoundedBlockingQueue) Size() int           { return len(q.ch) }
```

Go 的有缓冲 channel 天生就是阻塞队列：满了写阻塞，空着读阻塞，背压白送。生产代码写到这里就完事——**但如果面试官说"不许用 channel"**（就是想考并发原语），上 mutex + cond 版：

**Go（mutex + cond 版——面试标准答法）：**

```go
import "sync"

type BoundedBlockingQueue struct {
    mu       sync.Mutex
    notFull  *sync.Cond
    notEmpty *sync.Cond
    buf      []int
    head     int // 队首下标
    cnt      int // 当前元素数
}

func Constructor(capacity int) BoundedBlockingQueue {
    q := &BoundedBlockingQueue{buf: make([]int, capacity)}
    q.notFull = sync.NewCond(&q.mu)
    q.notEmpty = sync.NewCond(&q.mu)
    return *q
}

func (q *BoundedBlockingQueue) Enqueue(element int) {
    q.mu.Lock()
    defer q.mu.Unlock()
    for q.cnt == len(q.buf) { // ⚠️ while 防虚假唤醒，别用 if
        q.notFull.Wait()
    }
    q.buf[(q.head+q.cnt)%len(q.buf)] = element // 环形写尾部
    q.cnt++
    q.notEmpty.Signal() // 放一个元素，唤醒一个消费者
}

func (q *BoundedBlockingQueue) Dequeue() int {
    q.mu.Lock()
    defer q.mu.Unlock()
    for q.cnt == 0 {
        q.notEmpty.Wait()
    }
    v := q.buf[q.head]
    q.head = (q.head + 1) % len(q.buf) // 环形推进队首
    q.cnt--
    q.notFull.Signal() // 腾一个空位，唤醒一个生产者
    return v
}

func (q *BoundedBlockingQueue) Size() int {
    q.mu.Lock()
    defer q.mu.Unlock()
    return q.cnt
}
```

**Python（threading.Condition 版）：**

```python
import threading

class BoundedBlockingQueue:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.buf = [0] * capacity
        self.head = 0
        self.cnt = 0
        self.cond = threading.Condition()  # 一把锁配两个条件，靠谓词区分

    def enqueue(self, element: int) -> None:
        with self.cond:
            while self.cnt == self.cap:   # while 防虚假唤醒
                self.cond.wait()
            self.buf[(self.head + self.cnt) % self.cap] = element
            self.cnt += 1
            self.cond.notify()            # 唤醒一个等待的消费者

    def dequeue(self) -> int:
        with self.cond:
            while self.cnt == 0:
                self.cond.wait()
            v = self.buf[self.head]
            self.head = (self.head + 1) % self.cap
            self.cnt -= 1
            self.cond.notify()            # 唤醒一个等待的生产者
            return v

    def size(self) -> int:
        with self.cond:
            return self.cnt
```

> ⚠️ **边界陷阱**：① `Wait` 外包 `while`；② 环形数组的下标是 `(head + cnt) % cap` 写、`head` 读，别搞反；③ `notify` 必须**在释放锁之前**发（持有锁时发，避免唤醒的线程立刻又睡回去的惊群浪费）；④ `Size()` 也要拿锁读——不加锁读到的 `cnt` 是撕裂值，编译器还可能有寄存器缓存。

### 复杂度

| 维度 | 复杂度 | 说明 |
|---|---|---|
| enqueue | `O(1)` | 环形数组，无搬移 |
| dequeue | `O(1)` | 同上 |
| 空间 | `O(capacity)` | 预分配固定缓冲 |
| 阻塞语义 | 队满/队空时线程挂起 | 由 OS 调度，不占 CPU |

### 面试官连环问

> 💬 **问：为什么 `wait` 外面必须是 `while` 不能是 `if`？**
>
> 🎯 答：三个原因叠加：① **虚假唤醒**——JVM/OS 规范允许 `wait` 在没有 `notify` 的情况下返回；② **条件可能被其他线程抢先破坏**——消费者 A 被唤醒，但消费者 B 先拿到锁把唯一元素取走了，A 醒来一看队列又空了，`if` 就直接崩在下标越界上；③ 多消费者 `notifyAll` 场景下必然发生②。`while` 让线程醒后**重新检查谓词**，不满足接着睡，永远安全。

> 💬 **问：什么时候必须 `notifyAll`（Broadcast）而不是 `notify`（Signal）？**
>
> 🎯 答：判定口诀：**一次状态变化能否让多个等待者同时通过？** 本题不行——放 1 个元素只能供 1 个消费者消费，`Signal` 一对一最省。反例：读写锁里读锁释放（后面排队的一堆读者都能进）、单缓冲区多消费者竞争（为了避免误唤醒单消费者消费了别人等的资源造成饥饿）。另外 `Signal` 有个隐藏陷阱：如果等待队列里既有等"非空"又有等"非满"的线程，用单个条件变量 + `notify` 可能唤错人（该醒生产者却醒了消费者），本题用两个条件变量天然规避。

> 💬 **问：单锁和双锁（putLock + takeLock）怎么选？**
>
> 🎯 答：读多写少的高并发队列用双锁——入队和出队互不阻塞，吞吐接近翻倍，`LinkedBlockingQueue` 就是双锁实现，代价是 `size()` 要维护原子计数器、代码复杂度高。低并发或教学场景单锁完全够，还更好验证正确性。工程判断标准：先 profile 锁竞争占比，`enqueue/dequeue` 临界区只有几条指令，锁竞争不激烈时双锁收益微乎其微。

> 💬 **问：这个队列怎么用到 rollout pipeline 里？`size()` 返回近似值要紧吗？**
>
> 🎯 答：三个映射：① **背压**：队列容量上限 = rollout 引擎的未完成样本上限，满了就让 vLLM 降并发，防显存爆；② **流水线**：partial rollout 下"先完成的样本先入队"，trainer 消费端永不空转；③ **近似 size 可以接受**：监控指标用，错一个两个无妨——但 `enqueue/dequeue` 自身的计数必须精确，否则背压失效。经典事故：用无锁队列的近似 size 做流控，流量突刺时实际堆积远超预期。

---

## 2) 面试技巧：veRL（HybridFlow）与 Rollout 基础设施

### 2.1 为什么 rollout 是 RL 训练的第一瓶颈

先把账算清楚，后面所有架构设计都是这笔账的推论：

- **采样量**：GRPO 每 step 要为 batch 内每个 prompt 生成 G=8~64 条完整回答，每条 4k~16k tokens。训练侧只是几次 forward/backward，生成侧是**数倍 token 量的自回归解码**。
- **硬件特性**：训练是 **compute-bound**（Tensor Core 吃满，MFU 50%+），生成是 **memory-bandwidth-bound**（每解码一步都要把全部权重从 HBM 过一遍，算力闲着）。同一批 GPU，两种 workload，互相嫌弃。
- **工程后果**：R1-Zero 级别的训练里，rollout 占总 step 时间 **70~90%** 是常态。所以 RL 训练的吞吐优化，九成功夫在 rollout 侧。

一句话：**RL 训练系统 = 训练框架 × 推理引擎 × 一条把两者缝起来的流水线**。veRL 干的就是"缝起来"这件事。

### 2.2 veRL / HybridFlow 总体架构

- **出处**：Sheng et al. (字节), *HybridFlow: A Flexible and Efficient RLHF Framework*（arXiv:2409.19256，2024.09），开源实现即 **veRL**。2025 年 R1 复现潮（Open-R1、TinyZero 生态）的主力框架。
- **核心设计哲学——单控制器（single-controller）编程抽象**：整个训练循环就是一个跑在 CPU 上的普通 Python driver 脚本，用 **Ray** 拉起 GPU worker（Actor），像写单机程序一样写分布式调度。对比 multi-controller 方案（OpenRLHF 早期、NeMo-Aligner）：每个 worker 内嵌自己的控制流，靠集合通信协同——灵活但心智负担爆炸，调一个调度策略要改一堆 worker 代码。
- **关键组件一张表**：

| 组件 | 职责 | 后端 |
|---|---|---|
| `ResourcePool` | Ray 资源池：哪些节点哪些 GPU 分给哪个角色 | Ray |
| `RolloutWorker` | 自回归采样生成 | **vLLM / SGLang / HF generate** |
| `ActorRolloutRefWorker` | 训练 + rollout + ref 三合一（colocate 模式载体） | FSDP / Megatron |
| `CriticWorker` | 价值模型（PPO 才需要；**GRPO 删掉**） | FSDP / Megatron |
| `RewardWorker` | 奖励：RM 前向 或 规则校验 | FSDP / 沙箱 |
| `WorkerGroup` | 同角色多 worker 的集合通信封装 | NCCL |

- **3D-HybridEngine**：并行维度自由组合——数据并行（FSDP/Megatron DP）× 模型并行（TP/PP）× **生成/训练角色并行**。同一个模型，rollout 时按 vLLM 的 TP 切法部署，训练时按 FSDP 切，权重同步时做重分布（resharding）。这是"Hybrid"的另一层含义：不是混合精度，是**混合并行拓扑**。

### 2.3 一条 GRPO step 的数据流（面试要能默写）

```
① driver 取一批 prompts（batch B 个 prompt）
② RolloutWorker: vLLM 生成，每组 G 条
   （continuous batching 混排全部 B×G 条；prefix caching 白嫖同 prompt 前缀）
③ RewardWorker: 规则校验 / RM 打分 → r_1..r_G
④ driver: 组内归一化算 advantage A_i（Day 112 的公式）
⑤ ActorWorker: 旧策略 logps（rollout 时已算）+ 新策略 logps
   → PPO clip loss + KL 惩罚 → 反向更新
⑥ 权重同步: NCCL broadcast 全量参数 → vLLM load_weights
   （colocate 模式：先 vLLM sleep 释放 KV cache → 训练 → wake + 重新预分配）
⑦ 回到 ①
```

两个细节追问预备：⑥里的 **ref logps** 怎么办？β>0 时 KL 项需要 ref 模型前向——veRL 的做法是 ref 和 actor colocate 共享同一份权重副本（actor 更新前先算好 ref logps），省一整份模型显存。④⑤之间数据在 driver 和 worker 间走 **NCCL/Ray object store**，B×G 条序列的 token ids + logps，MB 到 GB 级，不是瓶颈。

### 2.4 权重同步与显存编排：colocate 的艺术

GPU 不够时，训练和生成必须**共用同一批卡**，这是 veRL colocate 模式的出发点，也是它最精巧的部分：

- **显存三角冲突**：训练要 activations + 梯度 + 优化器状态（Adam 占 2 倍参数），vLLM 要 KV cache + 激活，加起来远超单卡 80GB。解法：vLLM **sleep mode**——训练阶段把 KV cache 整块释放（权重保留或换出到 CPU），训练完 `wake_up` + 重新预分配 KV block。
- **权重同步两板斧**：① **NCCL broadcast**：训练侧 FSDP all-gather 出全量参数后，经独立通信组广播给每个 rollout rank，走 NVLink/IB。7B（BF16 ≈ 14GB）在 NVLink 400GB/s 下约 35ms，毛刺级别；② vLLM `load_weights` 按 TP rank 分片接收，各取所需。
- **频率**：on-policy 要求**每个 step 同步一次**。同步开销 ≈ 参数量 / 带宽——7B 无感，70B+ 跨机（IB 200GB/s ≈ 350ms）开始肉疼，这是异步/延迟权重同步研究方向的动机。
- **分离式（disaggregated）**：rollout 用纯推理集群（甚至不同型号 GPU，比如 H100 训练 + L40S 生成），权重同步走 IB/对象存储。优点：两边各自吃满最优 batch size、互不打扰；缺点：多一套机器预算、跨机同步复杂。

**选型口诀**：GPU 紧张 / 中小模型 → colocate（sleep/wake 编排）；吞吐优先 / 大模型 → 分离式。判断指标：**rollout : train 时间比，优化到 ≈ 1:1 才算健康**。

### 2.5 吞吐优化四板斧

1. **Continuous batching**：vLLM/SGLang 运行时动态组 batch——一条生成完立刻塞下一条，GPU 不为等最长的那条空转。GRPO 同组的 G 条天然是好邻居。
2. **Prefix caching**（GRPO 的隐性红利）：同 prompt 采 G 条，system prompt + 题干的 KV 只 prefill 一次，后 G−1 条白嫖前缀。实测省 30~60% prefill 算力——PPO 的 batch 内 prompt 各异，没这福利。这是"GRPO 比 PPO 工程上更香"的隐藏论据。
3. **Chunked prefill + sequence packing**：长 prompt 切块调度，防一条长 prefill 阻塞整批 decode；短序列拼接填满 KV page，碎片率下降。
4. **Partial rollout 流水线**：不等整组 G 条全生成完，先回的先进训练队列。吞吐换一致性（见 2.6 连环问），工程折中是"同 prompt 的 G 条凑齐才放行"。

### 2.6 Agentic RL：多轮 rollout 的工程地狱

**场景**：search-agent（R1-Searcher 类）、代码 agent（Terminal-Bench 类）、tool-use 训练。一条轨迹 = prompt → 模型输出 → 解析 tool call → 环境执行 → 观测拼回 context → 再生成……循环直到终局或步数上限。

**veRL 的 agentic rollout 支持**：loop 级控制交给 driver 编排 + worker 侧环境沙箱（verifier 进程池、代码执行容器），每轮观测追加进上下文，多轮之间 KV 前缀续用（prefix caching 跨轮命中）。

**三大新痛点**：

| 痛点 | 症状 | 对策 |
|---|---|---|
| **长度爆炸** | 上下文随轮数线性涨，KV cache 爆 | 截断策略（丢早期轮次/压缩摘要）、`max_steps` 硬上限、长度惩罚进 reward |
| **环境异构** | 搜索 API 延迟、沙箱冷启动，rollout 时间方差爆炸 | async rollout + 动态 batching + 超时熔断 |
| **信用分配** | 终局 reward 稀疏到几乎没信号 | per-step shaping（工具调用成功给小奖励）、PRM、turn-level advantage（Day 112 预告的 agentic 变体落地） |

还有一个 GPU 利用率陷阱：环境等待时 GPU 不该闲着——把"等搜索 API 返回"和"解码别的样本"**异步重叠**（Ray future + vLLM 的多请求调度），是 agentic RL 框架的核心竞争力。

### 2.7 框架选型对比表

| 维度 | **veRL (HybridFlow)** | OpenRLHF | TRL GRPOTrainer | NeMo-Aligner |
|---|---|---|---|---|
| 控制面 | **单控（Ray driver）** | 多控（Ray） | 单控（单进程） | 多控 |
| 训练后端 | FSDP · Megatron | DeepSpeed ZeRO | HF Accelerate | Megatron |
| 推理后端 | **vLLM · SGLang** | vLLM | HF generate / vLLM | TensorRT-LLM |
| 最佳场景 | **研究新算法、RLVR 主流** | 大规模 RLHF | 教学、小模型快速验证 | NVIDIA 全家桶超大规模 |
| 上手成本 | 中 | 中 | **低** | 高 |

面试选型话术：**快速跑通选 TRL，研究/复现 R1 选 veRL，Megatron 生态重度用户选 NeMo，OpenRLHF 卡在中间**。

### 2.8 可复现性与故障恢复

- **seed 地狱**：torch / numpy / python / vLLM 四层随机源都要管。更麻烦的是 vLLM continuous batching 带来**运行时非确定性**——batch 内请求顺序影响浮点归约顺序，两次"相同配置"跑不出 bitwise 相同结果。严格复现要固定请求调度顺序（牺牲吞吐）或开确定性模式。
- **checkpoint**：actor 权重 + optimizer state + RNG state 全量存；rollout 引擎无需单独存（权重可从 actor 重建，KV cache 本来就是易失的）。
- **故障恢复**：Ray worker 崩溃自动重启（`max_restarts`）；按 step 幂等落盘 rollout 结果，恢复时跳过已完成 step，不重训不重采。

### 2.9 高频连环问速答

> 💬 **问：GRPO 训练 step 的时间怎么拆？优化顺序是什么？**
>
> 🎯 答：四段：**rollout 生成 / reward / 训练 / 权重同步**。先 profile 再动刀（torch profiler、nsys、vLLM 内置 metrics、veRL 的 step timer）。优化顺序：① 压 rollout——四板斧（continuous batching、prefix caching、chunked prefill、partial rollout），通常 70~90% 的时间在这；② 压训练——sequence packing、梯度累积调优、flash-attention、FSDP 配置；③ 同步一般不是瓶颈（7B 级 35ms），但 colocate 的 sleep/wake 显存重建会抖，要监控。

> 💬 **问：veRL 为什么选单控制器而不是 multi-controller？**
>
> 🎯 答：RL 训练循环的控制流本质是**串行状态机**（采样→打分→更新→同步），单控把调度逻辑写在一个 driver 脚本里，像单机程序一样直观，改调度策略只改一处；multi-controller 要把控制流拆进每个 worker，worker 间靠通信协议协同，灵活但调试地狱。代价是 driver 是逻辑单点——但控制面只发指令，数据面全走 NCCL，所以**不是性能瓶颈**。

> 💬 **问：colocate 和分离式部署怎么选？**
>
> 🎯 答：看 GPU 余量和模型大小。中小模型（≤13B）或卡紧张 → colocate：vLLM sleep/wake 编排显存，省一套机器；大模型或吞吐优先 → 分离式：训练卡和推理卡各自吃满最优 batch size，互不拖后腿，代价是权重同步跨机 + 双份基础设施。判断指标是 rollout:train 时间比，目标 ≈1:1；偏离太远就说明该换部署模式了。

> 💬 **问：权重同步每个 step 都要做吗？开销多大？**
>
> 🎯 答：on-policy 要求每 step 同步（否则重要性采样比漂移，clip 大量触发，有效梯度变少）。开销 = 参数量 / 带宽：7B BF16 ≈ 14GB，NVLink 400GB/s 约 35ms；70B 跨机 IB 200GB/s 约 350ms，相对秒级 step 占比仍小。真正的坑不在带宽，在 colocate 模式 sleep/wake 后 KV block 重新预分配的显存碎片和延迟抖动。

> 💬 **问：partial rollout 会不会破坏 GRPO 的组基线？**
>
> 🎯 答：理论上是隐患：先训后到的样本，其策略版本与当前策略有差（off-policyness 上升），同组样本若分开训练，组内均值基线的"同分布"假设被削弱。工程折中三层：① 严格模式——同 prompt 的 G 条凑齐才入训练队列，保组内一致性，牺牲一点吞吐；② 分组模式——mini-batch 对齐 + importance ratio 监控；③ 激进模式——真 partial + clip 兜底（clip 本身就是 off-policy 保险）。生产常用①或②。

> 💬 **问：agentic 场景 rollout 怎么不卡死？**
>
> 🎯 答：五件套：① 环境调用全异步（等搜索 API 时 GPU 接着解码别的样本）；② 超时熔断（单轮/单轨迹双上限）；③ `max_steps` + 上下文长度双硬限；④ 失败轨迹给惩罚 reward 而不是丢弃——**丢弃会 bias 数据分布**（难任务被系统性过滤，模型越训越挑软柿子）；⑤ 沙箱资源池隔离，防一个毒轨迹拖垮整个 worker。

> 💬 **问：prefix caching 为什么对 GRPO 特别香？**
>
> 🎯 答：组内 G 条样本共享同一个 prompt 前缀，prefill 从 O(G×L) 降到 O(L + G×增量)，省 30~60% prefill 算力，agentic 多轮场景还能跨轮命中。PPO 的随机 batch 内 prompt 各异，几乎吃不到。所以"GRPO 比 PPO 采样贵 G 倍"的账，prefix caching 能找补回一大块——这是面试里很加分的工程洞察。

> 💬 **问：怎么监控 RL 训练健康度？看哪些指标？**
>
> 🎯 答：六组：① **reward 曲线**——不涨先查 reward 实现 bug 再怀疑模型；② **组内 std**——趋零说明题目太易/太难，梯度归零，该换数据配比；③ **KL 轨迹**——爆 = β 太小在漂移，不动 = β 太大没在学；④ **响应长度分布**——突增可能是推理变长（好）也可能是 degeneration（坏），结合 reward 看；⑤ **advantage 分布**——全正/全负说明基线失效；⑥ **系统侧**——MFU、KV cache 利用率、step 四段时间占比、显存碎片。

> 💬 **问：vLLM 和 SGLang 选哪个做 rollout 引擎？**
>
> 🎯 答：都能用，benchmark 说话。经验法则：vLLM 生态成熟、文档全、社区大，默认选它；SGLang 在结构化输出（tool call 的 JSON schema 约束解码）、RadixAttention 前缀复用、多模态上更激进，agentic 场景值得一试。veRL 两个都支持，切换只是一行配置——这也是框架抽象的价值。

> 💬 **问：手写一个最小 GRPO 训练循环？**
>
> 🎯 答：driver 伪代码 20 行：`for step in range(N): prompts = sample(B) → futures = rollout_workers.generate(prompts, G) → rewards = reward_workers.score(futures) → adv = group_norm(rewards) → actor.update(loss(adv, clip, KL)) → broadcast(actor.weights, rollout_workers)`。然后指出：这段代码里**最慢的是第 2 行**，所以真正的工程全在 rollout 引擎和调度上——能说出这句话，面试官就知道你懂系统。

### 2.10 Day 113 自检清单

- [ ] 徒手画出 veRL 架构图：driver + Ray + 四类 worker + 通信关系
- [ ] 默写 GRPO step 数据流七步，指出第⑥步权重同步的实现方式（NCCL broadcast + per-shard load）
- [ ] 说清 colocate 模式的 sleep/wake 显存编排，以及它和权重同步的先后关系
- [ ] 背出吞吐四板斧：continuous batching / prefix caching / chunked prefill / partial rollout
- [ ] 解释 prefix caching 为什么对 GRPO 是隐性红利（组内共享前缀，prefill O(G×L)→O(L)）
- [ ] 答出 partial rollout 与组基线一致性的矛盾及三种工程折中
- [ ] 说出 agentic rollout 三大痛点及各自对策（长度爆炸/环境异构/信用分配）
- [ ] 背出四框架选型一句话（TRL 上手 / veRL 研究 / NeMo 大规模 / OpenRLHF 居中）
- [ ] 列出训练监控六组指标，并解释组内 std 趋零的含义
- [ ] 默写 20 行最小 GRPO 训练循环，并指出瓶颈行

---

## 📚 今日参考

- Sheng et al. (2024), *HybridFlow: A Flexible and Efficient RLHF Framework*（arXiv:2409.19256）—— veRL 论文，单控制器 + 3D-HybridEngine
- veRL 官方文档与 GitHub（volcengine/verl）—— `ResourcePool` / `ActorRolloutRefWorker` / colocate 模式 / agentic rollout 示例
- DeepSeek-AI (2025), *DeepSeek-R1*（arXiv:2501.12948）—— GRPO + 可验证奖励的 rollout 需求来源
- Kwon et al. (2023), *Efficient Memory Management for LLM Serving with PagedAttention*（vLLM，SOSP'23）—— KV cache 分页管理的源头
- Zheng et al. (2024), *SGLang: Efficient Execution of Structured Language Model Programs*（RadixAttention）—— 前缀复用
- LeetCode 1186. Design Bounded Blocking Queue；Java `LinkedBlockingQueue`（双锁实现参考）；Go `sync.Cond` 文档

---

> 🧠 **今天的一句话**：算法工程师问"怎么收敛"，系统工程师问"step 时间花在哪"。GRPO 的公式一页纸，把它跑起来的流水线要回答十个工程问题——而能问出这些问题的人，才真的准备好训练 R1 了。
