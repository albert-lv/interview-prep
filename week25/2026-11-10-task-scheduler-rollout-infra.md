# Day 159 · 2026-11-10 · Week 25 Day 1 🚀 开营

> **本周主题**：Agentic RL 训练基础设施实战（rollout 基础设施与异步解耦 → 权重同步与训练-推理一致性 → 奖励服务与 rollout worker 容错 → 训练安全与成本工程 → 收官复盘，与 agent-arena/veRL 集成项目线汇合）
>
> **今日算法题**：任务调度器（LC 621 · 堆 = 冷却期调度 = 训练任务排队 = rollout 队列的原子结构）
>
> **面试技巧**：rollout 基础设施与异步解耦——同步的 straggler 之困、异步化光谱（one-step off-policy → request-level async）、训练-推理解耦的权衡账本
>
> 🔥 **今日时事锚点 = Anthropic 定价文件发出又撤回（11/10 今天，路演启动日当天的悬疑剧：8.667 亿股 @ $70-80、最高募资 693 亿美元，随后紧急撤回称"定价细节有误"；估值预期从 2 万亿下修至 1.1-1.5 万亿；260 亿美元私募配售锁定谷歌/英伟达/微软/台积电等七家）** × **TideRL（readiness-aware 调度，2026）× ProRL Agent（Rollout-as-a-Service，arXiv 2603）× Libra（跨阶段弹性资源规划，arXiv 2606）× GLM-5.2 SAO（单 rollout 异步优化）四篇 agentic RL infra 论文扎堆** × **veRL one-step off-policy 报告 2.35-2.67x 吞吐提升**

---

## 一、今日算法题：任务调度器（LC 621）

### 题目

给定一个用字符数组 `tasks` 表示的 CPU 任务列表，字母代表任务种类。任务可以任意顺序执行，但**两个相同种类的任务之间必须间隔至少 `n` 个时间单位**（冷却期）。返回完成所有任务所需的最少时间。

```
输入：tasks = ["A","A","A","B","B","B"], n = 2
输出：8
执行序列：A → B → 待机 → A → B → 待机 → A → B（共 8 个时间单位）
```

### 工程读法（为什么 Week 25 开营选这道题）

Week 25 讲 agentic RL 训练基础设施，第一天的问题是：**rollout 到底是什么？** 答案：rollout = 一个任务队列，里面排着成千上万个 episode，每个 episode 长度不一、完成时间不一、依赖的资源（LLM backend、sandbox、工具）不一。

**而带冷却约束的任务排程，正是调度系统的原子结构：**

- **冷却期 n** = 同一个 LLM backend 切换权重版本后的"预热期"，或同一 sandbox 复用前的重置时间——ProRL Agent（arXiv 2603）的 min-heap LLM backend pool 支持动态注册和 checkpoint swapping，"换 backend"这个动作本身就有冷却成本
- **待机时间** = GPU 空转——TideRL（2026）指出的 agentic RL 核心矛盾：episode 长度重尾分布，同步训练下整个 batch 等最慢的那条轨迹，**GPU 利用率由尾部决定而不是均值**
- **贪心策略"每窗口尽量塞满 n+1 个不同任务"** = 调度器的第一性原理：**永远不要让昂贵资源闲着**——这和 one-step off-policy（rollout 用上一 step 权重、训练用当前 step，重叠执行）是同一个哲学：用"轻微的过期"换"零空转"
- 今天（11/10）Anthropic 路演启动，**8950 亿美元算力承诺本质上是一道巨型调度题**：GPU 就那么多，训练/rollout/推理/研发任务全在排队，每个任务的"冷却期"（调度约束）被写进合同 SLA——**调度约束一旦进 SLA，就不是算法能省的**

### 思路一：贪心模拟 + 最大堆（面试主推，过程可讲）

**核心不变量**：每个长度为 `n+1` 的冷却窗口里，尽量安排 `n+1` 个不同任务——能塞满就塞满，塞不满的坑才是待机。

```
1. 统计每个任务剩余次数，建最大堆
2. 每轮循环（一个窗口）：
   - 弹出剩余次数最多的前 n+1 个不同任务，各执行一次（次数 -1）
   - 还没执行完的任务放入"冷冻区"，本轮不再碰
   - 窗口结束，把冷冻区里还有剩余的任务放回堆里
3. 堆空但冷冻区还有任务 → 剩下的时间全是待机
```

```go
type IntHeap []int

func (h IntHeap) Len() int            { return len(h) }
func (h IntHeap) Less(i, j int) bool  { return h[i] > h[j] } // 最大堆
func (h IntHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *IntHeap) Push(x interface{}) { *h = append(*h, x.(int)) }
func (h *IntHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

func leastInterval(tasks []byte, n int) int {
    counts := make([]int, 26)
    for _, t := range tasks {
        counts[t-'A']++
    }
    h := &IntHeap{}
    heap.Init(h)
    for _, c := range counts {
        if c > 0 {
            heap.Push(h, c)
        }
    }
    time := 0
    for h.Len() > 0 {
        var cooling []int // 冷冻区：本轮执行过但还没做完的
        for i := 0; i <= n; i++ { // 一个窗口最多 n+1 个不同任务
            if h.Len() > 0 {
                cnt := heap.Pop(h).(int) - 1
                if cnt > 0 {
                    cooling = append(cooling, cnt)
                }
            }
            time++
            if h.Len() == 0 && len(cooling) == 0 {
                break // 全部完成，后续待机不用算
            }
        }
        for _, c := range cooling {
            heap.Push(h, c)
        }
    }
    return time
}
```

**为什么贪"剩余次数最多"是对的**（面试官必追问）：每执行一次次数最多的任务，它下次可执行的最早时间就被推得最远——**最容易变成瓶颈的任务要最优先处理**。反过来，如果先执行次数少的，次数多的任务到最后会发现"坑不够分"，被迫塞满大量待机。

### 思路二：数学公式（O(1) 额外空间，加分项）

出现次数最多的任务（设次数 `maxCount`）决定了框架：**它每两次执行之间必须隔 n 个坑**，所以：

```
frames = maxCount - 1          // 完整冷却窗口数
frame_length = n + 1           // 每个窗口长度
tail = maxCountTasks           // 并列最多的任务数（收尾各占一个坑）
answer = frames × frame_length + tail
```

如果任务种类足够多、每个窗口都填得满，`answer` 会被 `len(tasks)` 反超（此时冷却期形同虚设），取 `max` 即可。

```go
func leastIntervalFast(tasks []byte, n int) int {
    counts := make([]int, 26)
    for _, t := range tasks {
        counts[t-'A']++
    }
    maxCount, maxTasks := 0, 0
    for _, c := range counts {
        if c > maxCount {
            maxCount, maxTasks = c, 1
        } else if c == maxCount {
            maxTasks++
        }
    }
    ans := (maxCount-1)*(n+1) + maxTasks
    if ans < len(tasks) {
        ans = len(tasks)
    }
    return ans
}
```

### 复杂度

- 堆解法：时间 O(N · log K)（K = 任务种类数，常数 26 → 近似 O(N)），空间 O(K)
- 公式法：时间 O(N)，空间 O(K)（仅计数）

### 坑三连 & 进阶六连

**坑**：
1. `n = 0` 时公式法直接退化为 `len(tasks)`——但堆解法不用特判，天然正确
2. 堆解法里 `time++` 放在循环体内部，**窗口没塞满时待机也要计时**；最后 `break` 条件（堆空且冷冻区空）防止多算尾部待机
3. 冷冻区的任务**本轮不能放回堆**——放回去下一轮可能又选到它，违反冷却约束

**进阶**：
1. **冷却期动态化**（不同任务不同冷却）→ 公式法失效，堆解法改优先队列（按"最早可执行时间"排序）；工程对应 = Libra 的异构 TP 路由：长轨迹分到大 TP 实例、短轨迹留在小 TP 实例，**冷却约束变成资源约束**
2. **任务有优先级/依赖** → 拓扑排序（Day 151 LC 207 呼应）；评测流水线编排就是 DAG 调度
3. **分布式调度** → 单机堆升级为分布式队列 + 就绪度感知；TideRL 的核心思想：**trainer 不傻等，而是按 rollout 的就绪度动态组 batch**
4. **允许中断/部分完成** → APRIL（Active Partial Rollouts）：超出 batch 需求的多余 rollout 请求照常跑，buffer 够了就终止在途生成、回收部分轨迹下轮续跑——**reported 44% 吞吐提升 + 最高 8% 最终精度提升**（因为 buffer 偏向已完成的 episode，信息密度更高）
5. **双机并行版** → 两台机器同时调度（经典 follow-up）：把任务分成两组，分别求最少时间，取 max；进阶思路 = 负载均衡 + 同任务间隔约束的联合优化
6. **带截止时间的调度** → 从"最少总时间"变成"每个任务有 deadline"的可行性判定，是 EDF（最早截止优先）的领域——工程对应 = 训练任务的优先级抢占：spot 实例上的 rollout worker 随时可能被回收，调度器必须会"算账"

### 一句话速答

> **任务调度器：贪心模拟 = 最大堆每窗口取 n+1 个不同任务，塞不满才待机；数学法 = (maxCount-1)×(n+1)+并列数，和 len(tasks) 取 max。它是带冷却约束的资源排程的原子结构——LLM backend 的权重切换冷却、GPU 的任务排队、sandbox 的复用重置，全是这道题。**

---

## 二、面试技巧：rollout 基础设施与异步解耦

### 0. 今天的热点：Anthropic 定价文件"发出又撤回"（11/10，就是今天）

美东 11/9 晚，Anthropic 向 22 家承销银行发送了一份招股文件副本：**8.667 亿股 A 类股 @ $70-80、最高募资 693 亿美元**；随后又发一封邮件：**"定价细节不准确"**，文件撤回、稍后更新。史上最大 AI IPO（估值讨论区间从 2 万亿一路下修到 1.1-1.5 万亿）在路演启动当天上演了这出悬疑剧。同一份被撤回的文件还披露：**谷歌、英伟达、AMD、博通、美光、微软、台积电已同意按 IPO 价参与 260 亿美元私募配售**——芯片商、云厂商、代工厂，半个产业链都把钱押在了这家公司身上。Q3 营收近 170 亿美元，算力承诺近 9000 亿美元。

**面试怎么用这个案例（不是吃瓜，是话术弹药）：**

这件事最妙的类比是**分布式系统的发布纪律**。一个未经最终确认的数据包被广播给了所有消费者——这就是经典的"两阶段提交少了一阶段"：prepare 了，commit 之前先发给了下游，发现不对只能发补偿消息（第二封撤回邮件）。

聊到 rollout 基础设施的容错、checkpoint、权重一致性时，可以这样切入：

> "Rollout worker 挂掉重启后必须拉最新 checkpoint，而不是用可能不一致的旧状态——这和 Anthropic 撤回错误定价文件是同一个逻辑：**先保证状态可回滚，再谈吞吐**。9000 亿美元的算力承诺是义务不是选项——训练任务的『冷却期』（调度约束）一旦被写进 SLA，就不是算法能省的。"

### 1. RL 训练循环的五个角色（地基，30 秒讲清）

一个 RL 训练系统最多涉及五个模型角色，硬件需求完全不同：

| 角色 | 用途 | 显存（7B, bf16） |
|---|---|---|
| Actor | 被训练的策略（前向+反向） | ~84 GB（权重+优化器+梯度） |
| Rollout | 快速自回归生成（vLLM/SGLang） | ~14 GB + ~40 GB KV cache |
| Reference | 冻结副本，算 KL 散度 | ~14 GB |
| Critic | 价值函数（仅 PPO） | ~84 GB |
| Reward | 打分 | 不定 |

**迭代循环**：采样 prompt → 生成 completion（**瓶颈在这**）→ 奖励打分 → 算 advantage → 策略梯度更新 → **同步权重给 rollout 引擎** → 下一轮。

PPO 需要全部五个角色（~236 GB → 至少 3-4 张 H100），**GRPO 的设计动机之一就是砍掉 Critic**。工程惯例：actor 和 rollout 共卡（共享权重、训练/生成交替）、actor 和 reference 共卡。

### 2. 同步的 straggler 之困（为什么必须异步）

Agentic RL 的定义性基础设施难题：**episode 长度服从重尾分布**。

- 一个 coding agent 的 rollout 可能调用 8-32 次工具、跨多个 sandbox、持续几分钟到几十分钟
- 同步模式下，**整个 batch 等最慢的轨迹**——迭代时间由 10% 的尾部决定，40% 的 GPU 在空转
- 这就是"冷却期调度"的分布式版本：短任务（快 episode）执行完不能走，必须等长任务（慢 episode）填满窗口——**GPU 版本的待机时间**

**记住这个账**：`T_iter = max(T_rollout, T_train)`——异步化之前，训练吞吐被 rollout 的 P99 锁死。

### 3. 异步化光谱（三级递进，各自代价背下来）

**Level 0：全同步**。全部生成 → 全部打分 → 训练 → 同步权重 → 下一轮。确定性、好调试，但 head-of-line blocking 严重。只适合 episode 长度均匀的场景（数学 RLVR）。

**Level 1：one-step off-policy（当前工程性价比之王）**。当前训练与下一批生成重叠，**rollout 用上一 step 的权重**。veRL 报告在 128 卡训练 Qwen2.5-7B 上 **2.35x-2.67x 提升**——一步的权重过期对训练动态影响极小（clipped objective 天然容忍小分布偏移）。

**Level 2：request-level async**。每条对话独立推进、无 batch 屏障——快 episode 完成即释放资源、新 episode 随资源空出即启动。这是让 agentic RL 可行的关键：**没有它，一条需要 5 分钟工具调用的对话会阻塞整个 batch**。实现参考：veRL `AgentLoopBase`（每条对话一个状态机）、Slime 原生 asyncio + `/abort_request` 端点。

**面试话术**："异步化不是免费午餐，是用一致性换利用率。Level 1 的代价是权重过期一步，Level 2 的代价是 policy lag 加剧——GLM-5.2 的 SAO 干脆放弃跟踪 π_old，直接用 rollout policy 的 log prob 做双边重要性采样 + 激进裁剪，把过期 token 的梯度完全 mask 掉。**约束太多的时候，改算法让约束消失，比堆工程更优雅。**"

### 4. 训练-推理解耦：架构账本

**分离派**（rollout 与训练物理分集群，异步执行）：
- 好处：资源配比独立调优、GPU 利用率拉满、异构硬件各干各的
- 代价：权重广播成为新瓶颈——7B 策略在 400 Gbps RDMA 下广播 <2 秒，100 Gbps 以太网要 8-12 秒，成为每次更新墙钟时间的显著占比
- 案例：RLinf（清华，256 卡 H100 验证）——rollout worker 无状态，被抢占后从 trainer 拉最新 checkpoint 重启，**只损失一个 update cycle 的 rollout 吞吐，训练进度不丢** → rollout 可以跑 spot 实例，trainer 跑 on-demand，成本直接砍半

**共卡派**（verl HybridEngine：同 GPU 上 FSDP ↔ vLLM 布局原地切换）：
- 好处：零广播成本、权重天然一致
- 代价：训练与生成串行——GPU 不能一边训练一边跑 vLLM
- Salesforce SFR-RL 的折中：全集群两阶段交替（rollout 阶段全员 rollout、训练阶段全员训练）+ 跨 batch 流水线，宣称 near-100% 利用率 + 同资源训练 5x 更大模型

**面试话术**："分离还是共卡，本质是权重广播成本 vs GPU 空转成本的比较。7B 模型 + 400G RDMA 选分离，70B+ MoE + 长轨迹共卡更稳。Libra（arXiv 2606）给出了第三个答案：GPU 角色弹性切换——训练↔rollout 切换成本约 15 秒（10.8s vLLM 激活 + 3.6s 快照 + 4.4s RDMA 重载），但只占总 step 时间 <3.5%，换来的是随 workload 漂移动态再平衡资源配比。"

### 5. 模拟面试速答五连

**Q1：agentic RL rollout 和 RLHF rollout 的基础设施差异？**
> 三个量级的差异：①吞吐——单 completion 变成 8-32 次工具调用的多轮轨迹，rollout 吞吐需求高一个数量级；②环境依赖——奖励来自可验证信号（代码过没过测试集），验证是 I/O 密集且延迟不定的，reward 计算不能阻塞 GPU，必须异步跑在 rollout 集群旁边；③长尾——episode 长度重尾分布，同步模式下 GPU 利用率由尾部决定，所以 request-level async 不是优化是刚需。

**Q2：rollout worker 挂了怎么办？**
> 三板斧：①心跳检测（crash 秒级发现）→ ②无状态重启——worker 本身不存任何训练状态，拉最新 checkpoint 复活，损失一个 update cycle 吞吐而已（RLinf 的 spot 实践）→ ③心跳/健康检查要盯 progress 不只看 liveness（Day 155 呼应：进程活着 ≠ rollout 在推进，模型可能陷入无效循环空转烧 token）。

**Q3：one-step off-policy 会不会让训练不稳？**
> 一步权重过期 ≈ 小分布偏移，clipped objective 天然容忍，实测 2.35-2.67x 吞吐提升且精度不掉。过期超过一步（Level 2）就要上重要性采样修正——SAO 的做法是直接双边裁剪把极端偏移 token 的梯度 mask 掉，用"近似但激进"换"精确但昂贵"。

**Q4：怎么给 rollout 集群省钱？**
> 先画账本树（Day 150）找大头：rollout 是 GPU 大户。三板斧：①rollout worker 无状态 → 跑 spot 实例（RLinf 实测 48-72 小时 32 卡 H100 训练成本从 $5,357 降到 $3,571）；②权重量化广播（bf16 → fp8 传输、GPU-direct RDMA）；③部分 rollout 复用（APRIL：终止在途生成回收部分轨迹，44% 吞吐提升，且 buffer 偏向已完成 episode 反而提升最终精度）。

**Q5：为什么 rollout 任务队列用堆？（把算法题焊回来）**
> 调度决策的核心是"下一个最该跑谁"，这就是一个优先级问题——剩余量最大（防瓶颈）、就绪度最高（TideRL）、权重版本最匹配（减少切换冷却）都是堆的 key。LC 621 的冷却期约束在工程里的对应物：同一 backend 的权重切换成本、sandbox 复用重置时间——**调度器的价值 = 单位时间内填满的坑最多，空闲坑最少**。

### 6. 今日金句

> **「同步训练的 GPU 利用率由最慢的 episode 决定，就像任务调度器的答案由出现次数最多的任务决定——都是尾部定价。Anthropic 今天用 693 亿美元的定价撤回告诉我们：状态没确认之前就广播，迟早要发补偿消息；而 veRL 用 2.35x 告诉我们：权重过期一步不可怕，GPU 空转才可怕。rollout 基础设施的全部要义，就是带着冷却约束把坑填满。」**

### 7. 自检清单

- [ ] 手写 LC 621 堆解法（含冷冻区逻辑）+ 公式法
- [ ] 说出异步化三级递进（sync → one-step off-policy → request-level async）及各自代价
- [ ] 记住一个数字：veRL one-step off-policy 2.35x-2.67x
- [ ] 记住一个数字：APRIL partial rollout 44% 吞吐提升
- [ ] 说出五个模型角色及 GRPO 砍掉 Critic 的动机
- [ ] 说出分离 vs 共卡的核心权衡（广播成本 vs 空转成本）
- [ ] 能复述 Libra 弹性切换的成本账（<3.5% step time）
- [ ] 能复述 Anthropic 定价撤回 = 发布纪律问题（两阶段提交类比）
- [ ] 能回答"spot 实例为什么敢跑 rollout worker"（无状态 + checkpoint 拉取）
- [ ] 能背出 SAO 的核心思想（放弃 π_old 跟踪，双边裁剪）

---

## 三、热点深读

### Anthropic IPO：路演启动日的定价撤回悬疑剧（11/10 今天）

**时间线**：11/9 晚向投行发送招股文件副本（8.667 亿股 A 类股 @ $70-80，公司出售 8.433 亿 + 老股东出售 2330 万，最高募资 693 亿美元）→ 同日晚跟进邮件"定价细节不准确" → 11/10 最快当天启动路演 → 目标 11/16 当周定价、11/26 感恩节前挂牌纳斯达克（代码 ANT）→ 11/13 CEO Dario Amodei 与摩根大通 CEO Jamie Dimon 对谈直播，拟将发行股份 15-20% 分配给散户。

**撤回文件里的关键信息**：260 亿美元私募配售已锁定（谷歌、英伟达、AMD、博通、美光、微软、台积电按 IPO 价参与 N 类普通股）——**AI 产业链的资本纽带已经交织到"半个供应链互相持股"的程度**。

**市场背景**：估值预期从 2 万亿 → 1.5 万亿（上限）→ 1.1-1.3 万亿（买方讨论区间）；CoreWeave 等 AI 基建公司推迟/撤回上市，AI 股遭剧烈抛售；Q3 营收近 170 亿美元但**季度增长率已明显放缓**；8950 亿美元算力承诺是义务不是选项。

**工程读法**：这是"发布纪律"的万亿级反面教材——**没有最终确认的状态就广播给下游 = 必然需要补偿消息**。对应到 RL 基础设施：权重同步必须是"先落 checkpoint、再广播版本号、消费者按版本号拉取"，而不是"直接推权重"。

### Agentic RL 调度层论文扎堆（本周密集出现）

- **TideRL**（2026）：readiness-aware scheduling——trainer 不傻等 straggler，按 rollout 就绪度动态组 batch。**"trainer 有活可干，只是没有它等的那个活"**——把调度问题从资源分配改成就绪度排序
- **ProRL Agent**（arXiv 2603）：Rollout-as-a-Service——sandbox 容器化（init/run/eval 三生命周期）、INIT→RUN→EVAL 三段异步流水线、**min-heap 维护 LLM backend pool**（动态注册 + checkpoint swapping）、trainer 通过 HTTP 对接 veRL/NeMo RL。**又是堆**——backend 调度的 key 就是"哪个 backend 最该接下一个任务"
- **Libra**（arXiv 2606）：跨阶段资源规划——训练↔rollout 弹性切换（切换成本 <3.5% step time）、异构 TP 路由（长轨迹升大 TP、短轨迹留小 TP）、两阶段分层负载均衡（先同物理机 IP 匹配省网络延迟，再 round-robin 保全局均衡）
- **GLM-5.2 SAO**：Single-Rollout Asynchronous Optimization——放弃 π_old 跟踪（用 rollout policy log prob 直接算比率）、双边裁剪 mask 极端 token、单 rollout 消除 GRPO 组等待、value model 2:1 高频更新补偿方差 + Frozen Attention（只微调 FFN）

### veRL 生态动态（项目线背景板）

- one-step off-policy 报告 2.35-2.67x 吞吐提升（128 卡 Qwen2.5-7B）
- v0.7 起 server mode 成为 agentic 场景默认；`AgentLoopBase` 是 request-level async 的参考实现
- 趋势判断：server mode 成默认（verl/Slime/TorchForge 三家一致）、环境接口标准化（OpenEnv 推 Gym 式 step/reset/state）、训练-服务融合（RL 训练用的推理引擎就是生产 serving 引擎）

---

## 四、明日预告（Day 160 · Week 25 Day 2）

**主题**：权重同步与训练-推理一致性——TITO gateway 是怎么保证 token 不错位的（GLM-5 实战）× 训练/推理数值一致性三流派（Slime 同引擎 / MiniMax 解耦但 token 一致 / OpenRLHF 显式分离）× 权重广播的工程优化（RDMA/GPU-direct/量化传输）

**候选题**：LC 295 数据流中位数回归（双堆 = 权重版本流的一致性水位线）或设计题「百万 rollout worker 集群的权重分发」

---

*Week 25「Agentic RL 训练基础设施实战」开营。Rollout 是队列，GPU 是坑，冷却期是约束——调度器的全部工作，就是让最慢的轨迹不决定整体的命运。*
