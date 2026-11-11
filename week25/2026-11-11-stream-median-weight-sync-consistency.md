# Day 160 · 2026-11-11 · Week 25 Day 2 ⚖️ 一致性日

> **本周主题**：Agentic RL 训练基础设施实战（rollout 基础设施与异步解耦 ✓ → **权重同步与训练-推理一致性** → 奖励服务与 rollout worker 容错 → 训练安全与成本工程 → 收官复盘）
>
> **今日算法题**：数据流中位数回归（LC 295 · 双堆 = 权重版本流的一致性水位线 · Day 157 做过的题，今天换一副眼镜看）
>
> **面试技巧**：权重同步与训练-推理一致性——不一致的四层来源、对齐三流派、FP32 LM head 的血泪教训、top-p logprob 定义陷阱、vLLM Native Weight Transfer API、设计题「百万 rollout worker 集群的权重分发」
>
> 🔥 **今日时事锚点 = Anthropic 更新招股书、路演正式启动（11/10 启动路演、11/11 全球刷屏：定价区间 $70-78、估值 1-1.4 万亿美元、904 亿美元总盘超 SpaceX 862 亿纪录、IPO 已足额认购、11/18 定价 11/19 纳斯达克挂牌）——昨天撤回的定价文件今天正式"同步"了** × **Claude Fable 5.2 / Mythos 5.2 发布（11/9-10：路演当天发新权重版本，输出提速 30%、API 降价 20%）** × **vLLM 官方博客 11/6《Zero Mismatch, Simple Async RL: REINFORCE Zero, DeepSeek-V4.1 and Kimi K3.1》——bitwise 对齐 + unclipped IS，与今天主题严丝合缝** × **VeRL-Omni v0.3.0 发布（11/6：async RL 扩展）**

---

## 一、今日算法题：数据流中位数回归（LC 295）

### 题目

设计一个数据结构，支持两种操作：

```
void addNum(int num)  —— 从数据流中添加一个整数
double findMedian()   —— 返回目前所有元素的中位数
```

要求：数据流长度未知（可能无限），`findMedian` 必须 **O(1)**。

### 为什么今天回归这道题

Day 157（三天前）我们用双堆做过这题，当时的工程读法是「稳态监控的分位数检测器」——币安案的签名速率监控。今天回归，换一副眼镜：

**数据流 = 权重版本心跳流。** 一个 agentic RL 集群里，trainer 每个 step 产出一个新权重版本（v1, v2, v3, ...），成千上万个 rollout worker 异步同步权重，各自心跳上报"我当前跑到第几版"。于是系统里流淌着一条**版本号数据流**。

**中位数 = 一致性水位线（consistency watermark）。** `findMedian()` 实时回答一个调度器每 step 都要问的问题：**"P50 的 worker 已经同步到哪一版了？"** 这个水位线决定：

- 训练能不能安全推进下一步（多数 worker 已在新版本上，新 rollout 才是 on-policy 的）
- 哪些 worker 掉队太远需要告警（Day 159 的 straggler，今天的版本维度版）
- one-step off-policy 的"一步"到底是多少——**水位线差几个版本，policy lag 就是几**

**双堆 = 已同步堆 vs 落后堆的动态平衡。** 大顶堆放较小一半（落后的 worker）、小顶堆放较大一半（领先的 worker），两个堆顶的中间就是整个集群的一致性水位线。版本心跳到来 O(log n) 入堆，调度器 O(1) 读水位线——**不花一毛钱的时刻表，换来每 step 一次免费的一致性体检**。

而今天的更大主题是：这条版本流不仅要"水位线正确"，还要**每一位的数值都对得上**——往下看。

### 思路：双堆平衡（核心不变量背诵版）

```
大顶堆 maxHeap 存较小的一半数字
小顶堆 minHeap 存较大的一半数字
核心不变量（二选一，全代码必须死守）：
  偶数时：size(maxHeap) == size(minHeap)
  奇数时：size(maxHeap) == size(minHeap) + 1
中位数：
  偶数：(maxHeap.top + minHeap.top) / 2
  奇数：maxHeap.top
插入纪律（写法 A，面试主推）：
  1. 一律先插 maxHeap
  2. 检查堆顶秩序：若 maxHeap.top > minHeap.top，交换两堆顶
  3. 检查尺寸纪律：若 size(maxHeap) > size(minHeap)+1，弹 maxHeap 顶给 minHeap
```

**为什么这样是对的？** 步骤 2 保证 maxHeap 里所有元素 ≤ minHeap 里所有元素（全局有序性由两堆夹逼出来）；步骤 3 保证尺寸不变量。两条纪律合起来 = 中位数永远躺在两个堆的交界处。

```go
type MedianFinder struct {
    maxH IntHeap // 大顶堆：较小的一半
    minH IntHeap // 小顶堆：较大的一半
}

func Constructor() MedianFinder {
    return MedianFinder{maxH: IntHeap{}, minH: IntHeap{}}
}

func (mf *MedianFinder) AddNum(num int) {
    heap.Push(&mf.maxH, num)          // 一律先进大顶堆
    if mf.maxH[0] > mf.minH.Top() {   // 堆顶秩序矫正
        a := heap.Pop(&mf.maxH).(int)
        b := heap.Pop(&mf.minH).(int)
        heap.Push(&mf.maxH, b)
        heap.Push(&mf.minH, a)
    }
    if mf.maxH.Len() > mf.minH.Len()+1 { // 尺寸纪律
        heap.Push(&mf.minH, heap.Pop(&mf.maxH).(int))
    }
}

func (mf *MedianFinder) FindMedian() float64 {
    if mf.maxH.Len() > mf.minH.Len() {
        return float64(mf.maxH[0])
    }
    return float64(mf.maxH[0]+mf.minH[0]) / 2
}

// IntHeap 通用实现：Less 里一个 > 号变大顶堆，一个 < 号变小顶堆
type IntHeap []int

func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] > h[j] } // 大顶堆版本
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h IntHeap) Top() int {
    if len(h) == 0 { return -1 << 30 } // 空堆兜底
    return h[0]
}
func (h *IntHeap) Push(x any) { *h = append(*h, x.(int)) }
func (h *IntHeap) Pop() any {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}
```

### 复杂度

| 操作 | 时间 | 说明 |
|---|---|---|
| `addNum` | **O(log n)** | 堆插入 |
| `findMedian` | **O(1)** | 读堆顶，零遍历 |
| 空间 | **O(n)** | 所有元素都在堆里 |

### 坑三连（面试现场自查）

1. **空堆访问堆顶 panic** —— `mf.minH.Top()` 前必须判空（或像上面给 Top 加兜底）。真实面试写 Go，这是第一翻车点。
2. **"先插哪个堆"的记忆口诀** —— 一律先进 maxHeap 再矫正，比"按大小分流再平衡"的写法少一个分支；两个写法都对，但混着写必错。
3. **int 溢出** —— `(maxH[0] + minH[0]) / 2` 两个 int 相加再转 float，元素量级大时先转 float 再加：`float64(a)/2 + float64(b)/2`。

### 进阶阶梯六连（面试官的追问弹药库）

**① 滑动窗口中位数（LC 480）—— 带"延迟删除"的双堆。** 版本水位线只看最近 N 个心跳才有意义（一个 worker 十分钟前掉线和现在掉线是两回事）。固定窗口 k：元素出窗时不能立刻从堆里删（堆不支持按值删），改为**哈希表记待删计数 + 堆顶过期懒弹出**——检查堆顶是否 stale，stale 就弹掉重看。窗口移动均摊 O(log k)。**工程读法：带 TTL 的一致性水位线，掉队 worker 的心跳过期后不再拉低水位。**

**② Top-K / P99 版本水位线。** 中位数只是 P50。调度器真正想盯的是 **P90 版本**——最后 10% 的掉队 worker 决定要不要等。精确解 = 双堆推广为 K 个有序桶；工程解 = **t-digest / GK sketch**：O(1) 内存换 ε 误差。**背过题答双堆，做过系统答 sketch。**

**③ 加权中位数。** worker 不一样大：128 卡的 mega-worker 和单卡 spot 实例，心跳不该一人一票。权重版不变量变成「大顶堆总权重 == 小顶堆总权重」，入堆按权重分流。**工程读法：按容量加权的版本水位线——大 worker 权重高，它掉队才真的拖水位。**

**④ 分布式流中位数。** 一百万 worker 不可能都喂给一个堆。正解 = **每个集群本地维护 sketch（t-digest），中心节点合并 sketch 求全局分位数**——sketch 可合并，精确堆不可合并。**这句"可合并性决定分布式可行性"是系统设计加分句。**

**⑤ 两个有序数组的中位数（LC 4）—— 二分答案。** 如果版本流天然有序（worker 按 ID 排好序，各自上报版本），全局中位数 = 在两条有序序列上二分划分点 i/j，O(log min(m,n))。**工程读法：静态快照场景免堆。**

**⑥ 权重版本流的"真"语义：每个元素是个向量时钟。** 真实系统里 worker 上报的不是一个 int，而是 `(step, shard_id)` —— 模型切了片，worker A 可能权重主体是 v100 但新 expert 层还是 v99。**"版本"本身从全序变成了偏序**——这时双堆的 key 需要一个标量化函数（如 min 分量），更严格的系统直接上向量时钟 / Lamport 时钟（Day 155 呼应）。**进阶不是炫技：一致性水位线的定义跟着一致性模型走。**

---

## 二、面试技巧：权重同步与训练-推理一致性

### 2.0 今日热点读法：Anthropic 的"权重版本"一天三更新

今天的一致性主题，被 Anthropic 用一场 IPO 演成了行为艺术：

- **11/10 早上**：定价文件发给 22 家承销银行（$70-80、693 亿）——**未确认状态就广播给下游**（Day 159 的两阶段提交缺一阶段）
- **11/10 当天**：紧急撤回，"定价细节不准确"
- **11/10 晚-11/11**：更新招股书正式路演——**定价区间 $70-78，估值 1-1.4 万亿美元**（较此前 1.5-2 万亿预期下修约 1/3），募资最高 643.5 亿 + 260 亿私募配售 = **904 亿美元总盘，超越 SpaceX 862 亿成史上最大**，11/18 定价、11/19 挂牌，代码 ANT，**认购订单已足额覆盖全部可售股份**
- 同一时刻，**Claude Fable 5.2 / Mythos 5.2 发布**：输出提速 30%、API 降价 20%（$8/$40 per MTok）——**路演当天发新权重版本**

工程读法三连：

1. **版本号不是虚荣，是合约。** 招股书的版本、权重的版本、API 价目表的版本——任何一个对外广播，下游（投资人 / rollout worker / 客户）就会拿它做决策。Anthropic 昨天播了 v1.0，今天全网同步 v1.1——**撤回并重新广播的代价是信誉，权重播错的代价是 reward 崩溃，机制一模一样。**
2. **降价 20% 发生在"同步权重"的同一天。** Fable 5.2 的 API 定价比 Fable 5.1 低 20%——同一个"权重版本"，在订阅池、按量价目表、缓存价三个口径下的"燃烧成本"全部改变（Day 150 燃烧顺序口诀：相对价格结构变才触发重算）。**权重同步从来不只是技术动作，它同一天改写了所有人的账本。**
3. **足额认购 = 一致性水位线过线。** 路演一天的认购就覆盖全部可售股份——就像 P90 worker 都过了水位线， trainer 可以放心 commit 下一步（11/18 定价）。

### 2.1 为什么一致性是 agentic RL 的生死线

RL 的核心机制是 importance ratio：

```
ρ_t = π_θ(a_t|s_t) / π_θ_old(a_t|s_t)
```

- 分子：训练引擎在当前权重下重算的 logprob（ℓ_t^T）
- 分母：rollout 引擎采样时记录的 logprob（ℓ_t^R）

**理想状态：同一权重版本、同一 token，ρ_t = 1，训练信号干净。**

现实是三股漂移叠加（11/8 arXiv 2611.11450 的口径）：

1. **权重版本漂移（policy lag）**：Day 159 的异步化——rollout 用的是旧版本，这是"已知且可用 IS 修正"的漂移。
2. **数值漂移（numerical mismatch）**：**同一个权重版本**，训练引擎和推理引擎算出来的 logprob 就是不一样——kernel 不同、reduction order 不同、精度不同。vLLM 10/14 博客的实测：DeepSeek-V4-Flash 上高性能 baseline 的 train-inference KL 从 2e-3 **涨到 29.9，reward 直接回落**。这是"最阴险"的漂移：**你以为你在 on-policy 训练，其实你在优化另一个策略。**
3. **语义漂移（定义不一致）**：两个引擎对"logprob"这个词的定义不同——见 2.3 的 top-p 陷阱。

**面试一句话**：SFT 里 forward 差 0.001 是噪声；RL 里 logprob 差 0.001 被 exponentiate 后变成 ratio 的系统性偏移，clip 会 clip 错 token，梯度会被污染——**MiniMax M1 的教训是 LM head 精度问题让 RL reward 彻底停滞，相关性从 ~0.9 修回 ~0.99 才恢复训练。一个层的精度，能停掉整个训练。**

### 2.2 训练-推理不一致的四层来源（排查清单）

| 层 | 来源 | 典型症状 | 修法 |
|---|---|---|---|
| **L1 数值** | kernel 不同（FlashInfer vs FA3 vs Triton）、reduction order 不同（batch size 相关）、精度不同（bf16 vs fp8 LM head） | `train_rollout_logprob_abs_diff` 从 0.011 缓慢爬升 | 对齐 kernel / deterministic mode / FP32 LM head |
| **L2 语义** | top-p/top-k 重归一化后 logprob 定义不同、采样参数实现不同 | step-0 就偏大一个常量（如 +0.053） | 统一"比较的对象"定义（见 2.3） |
| **L3 tokenization** | BOS/EOS 插入、chat template、padding 策略两边不同 | token 序列错位一位，logprob 全对不上 | TITO gateway / Data Coordinator 统一入口 |
| **L4 版本** | 权重同步滞后、部分 shard 没同步完 | diff 随 step 单调增长 | 版本号广播 + 水位线（今天算法题） |

**排查心法：step-0 就大 = L2/L3（定义问题，和训练无关）；step-0 小但随 step 增长 = L1/L4（漂移问题）。** 这个二分法是面试时拉开差距的分寸感。

### 2.3 对齐三流派 + 2026 年军备赛新证据

**流派一：同引擎派（Slime / Miles —— "根本不让不一致发生"）**

- 同一个 SGLang server 既跑 rollout 生成又跑 logprob 打分（OpenAI-compatible API）——**架构上消灭 L1 数值漂移**
- Truly On-Policy 模式：FA3（prefill/decode bitwise 相等）+ DeepGEMM（固定 reduction order 的确定性 GEMM）+ torch.compile + 数值细节对齐 → **logprob bitwise identical，训练-推理 KL 严格为 0**
- GLM-5.3（8 月发布）口径：logprob 平均差异压到 **1e-7 量级，较此前配置降低 99.99% 以上**
- 代价：侵入式修改训练后端、MoE/多模态架构覆盖仍有限（Slime 自己承认"只在 vanilla dense 上从没观察到因 mismatch 崩溃"）

**流派二：解耦但 token 一致派（MiniMax Forge —— "分开跑，但账本对得上"）**

- 训练和推理完全解耦（Gateway Server + Data Pool 异步收集轨迹），但**推理引擎在 agent 执行期间持久化全部日志，Data Coordinator 抽取精确 token 序列喂给训练**——L3 tokenization 从源头锁死
- **FP32 LM head fix**：M1 从 dense 迁 MoE 时 RL reward 完全停滞，层间排查发现 LM head 高幅值激活放大了两边精度差，相关性 ~0.9x → FP32 修回 ~0.99x——**这个 fix 现在是他们 MoE 模型的标准配置**
- 配套算法：CISPO（clip IS 权重本身而不是 clip objective，保住大 ratio token 的梯度）+ MIS 多重重要性采样处理 agent 长轨迹的尾部 ratio

**流派三：显式分离派（OpenRLHF —— "接受小不一致，换工程简单"）**

- vLLM 生成 + HuggingFace forward 打分，承认有 gap，靠 clip 兜住
- 适合：预算有限、模型规模不大、快速验证的场景
- 面试话术："选它不是错误，是**把一致性风险显性化并纳入 clip 的设计假设**——但 agentic 长轨迹 + 多步工具调用会把 ratio 方差放大，小不一致被 MIS 指数放大，流派三在规模化 agentic RL 里是第一个撞墙的"

**2026 军备赛新证据（背三个数字就够）：**

- **vLLM 10/14**：训练 forward 直接调 vLLM 推理 kernel，两边 bitwise identical——DeepSeek-V4-Flash 100 步每步 diff = 0，**代价只有 8.7% 吞吐损失**（保住 91.3%）；Nemotron-3.5 上 baseline 的 SWE-bench Pro 从 0.56 掉到 0.22，一致性版本守住 0.56
- **vime × RL-Kernel × AMD（9/24）**：ROCm 上 Megatron + vLLM 对齐，**200 个 GRPO step 零 mismatch**——"vime 对齐时间线（哪个 token batch 属于哪个权重版本），RL-Kernel 对齐数值执行（哪些值参与计算、怎么分片合并、在哪取整）"
- **vLLM 11/6《Zero Mismatch, Simple Async RL》**：bitwise 对齐 kernel + **unclipped importance sampling** 修正 policy lag——覆盖 REINFORCE Zero、DeepSeek-V4.1、Kimi K3.1、AMD MI355X。**这句话值得背：数值一致性（同版本 diff=0）和策略滞后（跨版本）是两个独立问题——把前者归零，后者才能被 IS 干净地度量。**

### 2.4 top-p logprob 定义陷阱（Miles 面试场景，10/23）

真实事故：RL 团队在 temperature=1、top-p=0.9 下采样，训练端对全词表打分，发现换了引擎后 `train_rollout_logprob_abs_diff` 从 0.015 跳到 +0.053。三个错误归因：

- ❌ "新引擎 kernel 差，换回去" —— raw 模式下新引擎和 fp32 参考差 0.005，跟旧引擎一样好
- ❌ "开确定性推理" —— 只让引擎重复自己的输出，不改 logprob 的定义
- ❌ "差值是常量，训练端减掉" —— 位移是 `-log(kept_mass)`，74-79% 的 token 的 kept mass < 0.99，**逐 token 不同向、不同量，减常量 = 每个 token 留一个同方向的 bias**

**真相**：vLLM 的 `processed_logprobs` 是 **top-p 截断重归一化之后**的值（比全词表 logprob 高 -log(kept_mass)），SGLang 默认返回重归一化之前的值。**两个系统比较一个数之前，必须先同意这个数的定义。**

**正解二选一**（比较的对象必须一致）：
1. 引擎设 raw 模式 → 两边都用全词表 logprob
2. Miles replay 模式开 `--rollout-top-p --rollout-top-k` → 两边都用截断后集合

**金句**：*"Two systems that compare a number must agree on its definition before they compare its value."* 顺带：这个坑还是**免费的一致性探针**——step-0 gap 突然变大，第一怀疑的不是 kernel，是 flag。

### 2.5 权重广播工程优化（设计题弹药）

**vLLM Native Weight Transfer API（2026-05 官方标准化，四阶段必背）：**

```
init_weight_transfer_engine   # 建通信 channel，训练 rank0 + 全部推理 worker 进同一 NCCL group（只调一次）
start_weight_update           # 每 step 训练完，通知推理侧准备收权重
update_weights                # 实际传输，可分块多次调用（大模型必须分块）
finish_weight_update          # 收尾后处理（如量化校准、布局转换）
```

后端可插拔：**NCCL**（跨 GPU 广播）/ **CUDA IPC**（同卡共享内存句柄）。配套 pause mode 解决 DPEP（disaggregated prefill/decode）部署的死锁。

**传输优化五板斧（按性价比排序）：**

1. **量化传输**：bf16 → fp8，带宽直接减半，QATFactory 的做法更彻底——训练侧量化后按**推理引擎原生布局打包**（scale metadata + 低 bit 值），rollout 侧零校准直接加载。**"发送 inference-ready 的权重"** 比 "发送权重再各端转换" 省两遍转换误差。
2. **RDMA / GPU-direct**：7B bf16 ≈ 14GB，400G RDMA < 2s；100G 以太网 8-12s（Day 159 账本：广播成本能不能接受，先看网卡）。
3. **分块 + 流水**：按 layer 分块传，传第 k 层时第 k-1 层已经在推理侧就位——和流水线并行同一个思想。
4. **树形/环形广播**：worker 数 ≫ 1000 时，单点 broadcast 变 bottleneck，改树形扇出（NCCL 内部自动做）。
5. **增量同步**：只传 diff（LoRA / 稀疏更新）——agentic RL 里权重每步全量变，diff 不适用；但**只传被更新的 shard**（MoE 里没被路由到的 expert 可以慢一拍）是真实的工程红利。

**AReaL issue #1560 的活教材**：Megatron→vLLM 权重同步曾错误地要求训练/推理 pipeline-parallel size 一致（PP=4 训练配 PP=2 推理被拒）——**并行布局是各自的最优解，同步层不该强迫它们一致**。面试时说这个 bug，等于展示你读过真实仓库的 issue。

### 2.6 设计题：百万 rollout worker 集群的权重分发

> **面试官**：训练集群每 10 秒产出一个新权重版本，全球 100 万台 rollout worker 要及时拿到新版本，怎么做？

**四步答法：**

**① 需求澄清（先问三个数）**
- 权重多大？（7B bf16 ≈ 14GB → 百万台 × 14GB = 全网洪水；fp8/INT4 打包后 3.5-7GB 仍然巨大）
- 时效要求？（是"下一个 rollout 必须用新版"还是"一分钟内收敛就行"？后者宽松一个量级）
- worker 网络形态？（数据中心内 RDMA vs 广域网 vs 边缘混合——决定传输协议）

**② 架构总览：分层分发（CDN 的同构）**

```
Trainer → 区域中心节点（全量权威源）→ 机房种子节点 → 机架种子 → worker（P2P swarming）
```

- **第一层：权威源 → 区域中心**。全量一次，RDMA 分块流水（2.5 节五板斧）。
- **第二层：区域内树形扇出**。每个父节点带 10-30 个子节点，深度 3-4 层覆盖百万节点。
- **第三层：P2P swarming（BitTorrent 协议）**。掉队 worker 从已完成邻居拉取分块，**热点分块天然多源**——和权重版本水位线配合：水位线以上的 worker 顺手当种子。
- **版本协议**：权重文件名/元数据带版本号 + 内容哈希（Merkle root），worker 校验哈希再切换——**"先校验后切换"就是两阶段提交的 commit 阶段**，Anthropic 昨天少的就是这一步。

**③ 一致性保障（呼应今天算法题）**
- 每 worker 心跳上报 `(worker_id, version, latency)`，区域控制器维护**版本分布 sketch（t-digest）**，P50/P90 水位线 O(1) 可查
- 推进决策：P90 worker ≥ v_k 才允许 trainer 标记 v_k 为 "fully propagated"；水位线之下的 worker 标记 straggler，P2P 优先级提升
- **不追求强一致**（百万节点强一致 = 分布式锁地狱），追求**可度量、可告警、最终一致**——"一致性水位线"就是这个设计的心脏

**④ 容错（Day 24 全家桶回归）**
- 种子节点挂了 → 同级种子互备 + worker 自动换父（SWIM suspicion 语义，Day 155）
- 传输中断 → 分块断点续传 + 哈希校验
- 坏权重下发 → Merkle 校验在 worker 侧兜底 + 灰度发布（先发 1% worker，水位线观察 5 分钟再全量）——**这就是 canary/分阶段发布，GPT-6.2 Astra 影子灰度同款（Day 151）**

### 2.7 模拟面试速答五连

**Q1：为什么 RL 比 SFT 更怕训练-推理不一致？**
SFT 的 loss landscape 平滑，forward 差一点梯度方向大致还对；RL 的 importance ratio 是指数化的——logprob 差 0.05 被 exp 放大成 5% 的 ratio 偏移，clip 会 clip 错 token，优势估计被污染。MiniMax M1 实测：一个 LM head 的精度问题就能让 reward 彻底停涨。**SFT 差的是噪声，RL 差的是系统误差。**

**Q2：`train_rollout_logprob_abs_diff` 从 0.011 缓慢爬到 0.036，怎么排查？**
按 step-0 二分法：step-0 就在 0.011（健康基线）说明定义没问题，爬升 = 漂移。然后区分版本漂移 vs 数值漂移：检查权重同步水位线（P90 worker 的版本号）——如果水位线滞后增长，是同步问题；水位线正常但 diff 还涨，是 kernel 状态污染（vLLM CUDA graph replay / KV cache 布局在 update_weights 后没刷新，AReaL issue #1560 同款）。**读法：这个指标是训练系统的"体温计"，每天第一件事看它。**

**Q3：同引擎派（Slime）vs 解耦派（MiniMax Forge）怎么选？**
看三件事：① 模型架构——dense 小模型同引擎便宜，MoE/多模态同引擎改造成本指数上升；② 团队规模——同引擎要改训练后端，没专人维护别碰；③ 迭代阶段——早期验证用流派三（OpenRLHF）跑通，规模化前必须解决一致性，Agentic 长轨迹下流派三第一个撞墙。**一句话：同引擎买一致性，解耦买扩展性，显式分离买迭代速度——钱和时间总得花一个。**

**Q4：权重同步的带宽不够怎么办？**
三板斧加一刀：量化传输（bf16→fp8 带宽减半，QAT 直接发 inference-ready 低 bit 包）→ 分层 CDN 式分发（区域中心 + P2P swarming）→ 增量/按需同步（MoE 只热更被路由 expert）。最后一刀：**检查你的迭代频率是不是太奢侈——one-step off-policy 下 rollout 用旧版本是特性不是 bug，每 step 全量同步才是问题本身**（Day 159 的 2.35x 就是这么来的）。

**Q5：面试官让你设计百万 worker 的权重分发系统？**
先问权重大小/时效/网络三要素（不澄清就设计 = 耍流氓）→ 三层分发（权威源→区域树→P2P swarming，CDN 同构）→ 版本号 + Merkle 哈希"先校验后切换"（两阶段提交）→ 版本水位线（今天算法题：t-digest 维护 P50/P90，O(1) 读）→ 灰度发布 1% 观察 5 分钟 → straggler 用 P2P 优先级补。**全程一句话主线：不追求百万节点强一致，追求可度量、可告警、最终一致。**

### 2.8 今日金句

> **RL 训练的 importance ratio 是两个系统对同一个 token 的"证词"——权重版本不一致是"时间差"，数值不一致是"口径差"，token 不一致是"对象差"。Anthropic 昨天把错误的定价广播给了 22 家银行，今天更新招股书重新同步——撤回邮件就是它的重要性采样修正；而它路演当天发布 Fable 5.2、API 降价 20%，提醒我们权重版本从来不只是技术对象，它同一天改写了所有人的账本。所以训练-推理一致性的全部要义是三句话：比较之前先同意定义（top-p 陷阱），广播之前先确认状态（两阶段提交），优化之前先对齐口径（bitwise 或至少同 sketch）。让 logprob 差归零，policy lag 才能被 IS 干净地度量；让水位线可见，百万 worker 才敢放心 commit。**

---

## ✅ Day 160 自检清单

- [ ] 能不看书默写双堆不变量（`size(maxH) == size(minH)` 或 `+1`）和插入纪律？
- [ ] 能讲清"step-0 大 = 定义问题；step-0 小但爬升 = 漂移问题"的二分排查法？
- [ ] 能背出对齐三流派（同引擎/解耦但 token 一致/显式分离）各一个代表系统和一个代价？
- [ ] 能复述 MiniMax M1 的 FP32 LM head 教训（哪个层、什么症状、修完什么效果）？
- [ ] 能解释 top-p 陷阱里"减常量为什么错"（kept mass 逐 token 不同）？
- [ ] 能默写 vLLM weight transfer 四阶段 API？
- [ ] 能列出权重广播五板斧并说出各自的适用条件？
- [ ] 能完整答出百万 worker 权重分发设计题（三澄清 + 三层分发 + 水位线 + 灰度）？
- [ ] 能背出今天三个军备赛数字（vLLM 100 步 diff=0 @91.3% 吞吐 / ROCm 200 步零 mismatch / GLM-5.3 1e-7 降 99.99%）？
- [ ] 能给外行讲懂"Anthropic 撤回定价文件 = 重要性采样修正"这个类比？

---

## 📮 明日预告

**Day 161（Week 25 Day 3）**：奖励服务与 rollout worker 容错——奖励模型的服务化部署（reward server 的 SLA、延迟与吞吐账本）× rollout worker 的故障域划分（心跳失联 / 卡死 / 半残）× 部分 rollout 复用（APRIL 44% 的 buffer 设计）× 失败 episode 的归因与重试策略。

候选题：**LFU/LRU 缓存（LC 460/146 —— O(1) get/put = reward server 的热度分层 = APRIL buffer 的淘汰策略）** 或设计题「十万 QPS 奖励服务的负载均衡与降级」。
