# Day 110 — 按权重随机选择 + PPO 算法详解 🎬

> 📅 2026-09-22 · Week 17 Day 3 · 连续更新第 110 天
>
> 主题：强化学习与 RL 训练工程 🧠 · 从"裸奔的策略梯度"到工业界默认答案

Day 108 学了值方法（Q-Learning），Day 109 学了策略梯度（REINFORCE / Actor-Critic）。但裸策略梯度有三个致命伤：**样本用一次就丢（on-policy）、步长大了崩溃小了龟速、策略更新不受约束可能一步暴雷**。

今天的答案就是 **PPO（Proximal Policy Optimization）**——OpenAI 2017 年提出，至今仍是 LLM RLHF 的默认算法（InstructGPT / GPT-4 的 post-training 用的就是它）。

有意思的是，PPO 的本质是"**在旧策略的分布上小心地挪一小步**"——而今天的算法题「按权重随机选择」，恰好就是"从策略分布中采样动作"的工程实现。训练策略 = 调权重，采样动作 = 按权重抽样，题和考点再次互为镜像。

---

## 1) 今日算法题

### 按权重随机选择（Random Pick with Weight）

**题意**：给定一个正整数数组 `w`（`w[i]` 表示下标 `i` 的权重），多次调用 `pickIndex()`，要求**按权重比例**返回一个下标。

```
输入: w = [1, 3, 2, 4]
输出: 分布统计应接近：0 占 1/10，1 占 3/10，2 占 2/10，3 占 4/10
约束: pickIndex() 会被调用 10⁴ 次以上，单次调用必须 O(log n)
```

**关键约束**：不能每次调用都 O(n) 遍历累加（调用太频繁），需要预处理换查询。

### 思路：前缀和 + 二分查找

核心洞察：**按权重采样 = 在累积分布函数（CDF）上撒一个均匀随机点，找到它落在哪个区间**。

```
w       = [1,   3,   2,   4  ]
前缀和   = [1,   4,   6,   10 ]   ← CDF
总权重 W = 10

撒点: rand ∈ [0, 10)，比如 rand = 5.2
       ↓
找第一个 前缀和 > rand 的位置 → 6 > 5.2 → 下标 2 ✅
概率校验：落在 [4,6) 区间长度 = 2 = w[2]，长度/总长 = 2/10 ✅
```

撒一个均匀点，落在第 `i` 段区间的概率正好等于区间长度占比 `w[i]/W` —— **离散分布采样的通用模板**。

工程细节：
- 前缀和数组是**单调递增**的（权重为正），所以二分用 `upper_bound`（第一个大于 target 的位置）；
- Go 没有内置 upper_bound，手写 `sort.Search`；Python 用 `bisect_right`；
- `rand.Intn(total)` 取 `[0, total)` 整数，等价于在 CDF 轴上撒点。

**和昨天的蓄水池对照一下**（面试很爱问这组对比）：

| | 蓄水池抽样（Day 109） | 按权重随机选择（今天） |
|---|---|---|
| 目标 | **等概率**（每个 1/n） | **加权概率**（w_i / Σw） |
| 场景 | 不知道总长度、流式一遍过 | 权重已知、高频查询 |
| 预处理 | 无（O(1) 空间神来之笔） | 前缀和 O(n)，换查询 O(log n) |
| 本质 | 概率只依赖"第几个" | 概率映射为"区间长度占比" |

### 代码

**Go：**

```go
type Solution struct {
    prefix []int // 前缀和，即 CDF
    total  int
    rng    *rand.Rand
}

func Constructor(w []int) Solution {
    prefix := make([]int, len(w))
    acc := 0
    for i, v := range w {
        acc += v
        prefix[i] = acc
    }
    return Solution{
        prefix: prefix,
        total:  acc,
        rng:    rand.New(rand.NewSource(time.Now().UnixNano())),
    }
}

func (s *Solution) PickIndex() int {
    target := s.rng.Intn(s.total) // 在 [0, total) 撒均匀点
    // 手写 upper_bound：第一个 prefix[i] > target 的下标
    lo, hi := 0, len(s.prefix)-1
    for lo < hi {
        mid := (lo + hi) / 2
        if s.prefix[mid] <= target {
            lo = mid + 1
        } else {
            hi = mid
        }
    }
    return lo
}
```

**Python（bisect 一行版）：**

```python
import random, bisect

class Solution:
    def __init__(self, w):
        self.prefix = []
        acc = 0
        for v in w:
            acc += v
            self.prefix.append(acc)
        self.total = acc

    def pickIndex(self):
        target = random.randint(0, self.total - 1)
        return bisect.bisect_right(self.prefix, target)
```

### 复杂度

| 维度 | 复杂度 | 说明 |
|---|---|---|
| 构造 | `O(n)` | 前缀和预处理 |
| 每次 pick | `O(log n)` | 二分查找区间 |
| 空间 | `O(n)` | 前缀和数组 |

### 面试官连环问

> 💬 **问：权重会动态变化（比如实时更新 w[i]）怎么办？**
>
> 🎯 答：前缀和静态好用，动态就退化 —— 更新一个 w[i] 要改后面所有前缀和，O(n)。正解是**树状数组（Fenwick Tree）或线段树**：单点更新 + 前缀和查询都是 `O(log n)`，撒点后仍按前缀和二分。这就是"动态加权随机"的标准工程方案。

> 💬 **问：10 亿条数据，内存装不下前缀和怎么办？**
>
> 🎯 答：两条路线。① **Efraimidis-Spirakis**：给每条数据算 key = u^(1/w)（u 是均匀随机数），取 top-k 或全局最大者，流式一遍过、无需存 CDF；② 分桶 + 两层采样：先按块权重选块，再在块内采样，把前缀和换成"块级前缀和"，两级各自都能装下。

> 💬 **问：Go 的 `rand.Intn` 均匀性够吗？做 RL 训练采样动作会不会有问题？**
>
> 🎯 答：工程上够用（Go 1.20+ 的全局随机源改用 ChaCha8，质量更高）；但严肃场景（如并行 rollout 需要可复现的种子）要自己持有 `rand.Rand` 实例并显式播种 —— 今天的代码就是这么写的。做 RL 时**每个 worker 用不同种子**，否则所有 rollout 采出同一条轨迹，梯度估计就废了。

---

## 2) 面试技巧：PPO 算法详解

### 2.1 先复盘：裸策略梯度的三大致命伤

Day 109 的 REINFORCE / Actor-Critic 是"理论漂亮、工程拉胯"：

| 痛点 | 根因 | 后果 |
|---|---|---|
| **样本效率极低** | on-policy：数据用当前 π_θ 采的，θ 一变数据全废 | 每条轨迹只贡献一次梯度就要扔 |
| **步长玄学** | 损失函数对参数化方式敏感，同一 KL 距离下损失曲面可能天差地别 | 步长大了策略崩坏（reward 跳水），小了训练以月计 |
| **更新无约束** | 梯度上升对"往哪走"有指导，对"走多远"完全没数 | 可能一步跨进灾难区域，熵崩塌、性能不可逆 |

PPO 就是同时给这三个痛点开刀的手术方案。

### 2.2 第一刀：重要性采样（Importance Sampling）——让旧数据还能用

想复用旧策略 `π_θold` 采的数据，数学工具是重要性采样：

```
E_{x~p}[f(x)] = E_{x~q}[ (p(x)/q(x)) · f(x) ]
```

套到策略梯度上，把采样分布从新策略换成旧策略：

```
∇J(θ) = E_{(s,a)~π_old} [ r_t(θ) · ∇log π_θ(a_t|s_t) · A_t ]
其中 r_t(θ) = π_θ(a_t|s_t) / π_old(a_t|s_t)   ← 概率比（probability ratio）
```

`r_t(θ)` 就是"新旧策略对同一个动作的看法差多少"：θ = θ_old 时 r = 1。

⚠️ **面试必问的坑**：重要性采样**理论上无偏、实际上方差爆炸**。当新旧策略差太远，r 值忽而巨大忽而为零，估计质量雪崩。所以必须**配合"别走太远"的约束** —— 这就引出第二刀。

### 2.3 第二刀：信赖域思想 —— 从 TRPO 到 PPO

**信赖域（Trust Region）**的核心思想朴素：每次更新只允许策略在旧策略附近一个小区域内移动，在这个区域内线性近似才可信。

**TRPO（2015）**把它写成约束优化：

```
max_θ  E[ r_t(θ) · A_t ]
s.t.   E[ KL(π_old || π_θ) ] ≤ δ
```

TRPO 理论上漂亮，但用到了 Fisher 信息矩阵 + 共轭梯度，**二阶方法、实现噩梦**（天然 TensorFlow 1.x 的痛）。

**PPO（2017）的天才之处：把约束从"优化问题的条件"挪进"目标函数的形状里"**，用一阶方法拿到 95% 的效果：

```
L^CLIP(θ) = E_t [ min( r_t(θ) · A_t,  clip(r_t(θ), 1-ε, 1+ε) · A_t ) ]
```

默认 ε = 0.2。**逐符号读这个公式**（面试要求能默写 + 能画图）：

- `r·A`：重要性采样加权的老熟人；
- `clip(r, 1-ε, 1+ε)`：把概率比**强行夹在 [0.8, 1.2]**；
- `min(…, …)`：**取更悲观的那个**（pessimistic bound）—— 不让优化器靠 r 失控的方向刷高分。

**两种情况的直觉**（A > 0，即该动作比平均好）：
- `r > 1+ε`（动作概率已经加太多了）→ clip 生效 → 梯度为 0 → **停止奖励**；
- `r < 1+ε` → 正常按 r·A 梯度上升；
- A < 0 时对称：动作太烂就想压低它的概率，但压到 `1-ε` 以下就被 clip 拦住 → **停止惩罚**。

一句话：**PPO 的 clip 是"软信赖域"—— 不精确限制 KL，但把目标函数的斜率在 0.8/1.2 之外削平，优化器自然走不远。** TRPO 是"围墙"，PPO 是"在围墙该在的地方挖了壕沟"，一阶 SGD 就能跨过去求解。

> 📌 PPO 论文其实给了两个变体：clip 版 + **自适应 KL 惩罚版**（惩罚系数 β 随实测 KL 动态调整）。实践中 clip 版完胜，但答题时提一嘴 β 版会显得读过原论文。

### 2.4 PPO 完整流程（面试要求"一张纸写完"）

```
重复 N 轮迭代:
  1. 用当前策略 π_θold 采集一批 rollout（T 步或 M 个 episode）
  2. 计算每个时刻的 TD 误差 δ_t = r_t + γV(s_{t+1}) − V(s_t)
  3. 用 GAE 合成优势: Â_t = Σ_{l≥0} (γλ)^l · δ_{t+l}
  4. 对这批数据跑 K 个 epoch 的小批量 SGD（这才是"数据复用"的关键！）:
       θ ← θ + α · ∇ L^CLIP(θ)          （Actor 更新）
       φ ← φ − β · MSE(V_φ, R_t)          （Critic 回归真实回报）
       + 可选：熵正则  − c·H(π_θ)          （防坍缩）
  5. θ_old ← θ（下一轮重新采样，旧数据作废）
```

和 REINFORCE 对照，"复用"体现在**第 4 步**：一批数据训练 K 个 epoch（典型 K=3~10），而不是用完即弃 —— 配合 clip 保证复用时策略不会偏太离谱。这是样本效率数十倍提升的来源。

### 2.5 GAE：优势估计的偏差-方差旋钮

Day 109 埋的伏笔今天收。 Actor-Critic 用 TD 误差 `δ_t` 近似优势 `A(s,a) = Q − V`，但单步 δ 方差小偏差大、MC 回报无偏方差大。**GAE（Generalized Advantage Estimation）用一个 λ 滑动整条光谱**：

```
Â_t^GAE(γ, λ) = Σ_{l=0}^{T-t-1} (γλ)^l · δ_{t+l}
```

| λ | 退化 | 偏差 | 方差 |
|---|---|---|---|
| λ = 0 | 单步 TD：`δ_t` | 大（Critic 的偏差全吃） | 最小 |
| λ = 1 | MC：回报减 baseline | 无偏 | 爆炸 |
| λ ∈ (0,1)（常用 0.95） | 指数衰减加权的 δ 加权和 | 折中 | 折中 |

记忆口诀：**γ 管"看多远"，λ 管"信 bootstrap 多少"**。面试被问"PPO 的超参"时，能报出 `γ≈0.99, λ≈0.95, ε=0.2, K=4~10, minibatch≈64~256` 这一组经典值，就是资深候选人的气味。

### 2.6 PPO × 大模型：RLHF 的默认发动机

这是面试官眼睛发亮的地方。把 PPO 映射到 LLM post-training：

| PPO 组件 | RLHF 里的对应物 |
|---|---|
| 策略 π_θ | 正在被微调的语言模型（输出 token 分布） |
| rollout 采集 | 对一批 prompt 生成完整回答 |
| 奖励 r | **RM（Reward Model）打分** + KL penalty（防跑偏） |
| Critic V_φ | 另一个 value model（通常和 policy 同 backbone） |
| A_t = GAE(δ_t) | 每个 token 位置的优势估计（LLM 里常 γ=1、λ 调小） |
| clip | 一样的 clip，防止 policy 一步刷崩 RM |
| 熵正则 | 防模型把输出压成复读机 |

**LLM 场景的三个特化**（高频考点）：

1. **奖励极度稀疏**：只有回答结尾的 RM 标量 → token 级信用分配难 → 实际中 λ 取很小、让 advantage 多靠 GAE 平滑传播；
2. **额外的 KL 惩罚**：InstructGPT 在奖励里直接加 `−β·KL(π_θ || π_ref)`（π_ref 是 SFT 模型），双保险防止 reward hacking 和语言退化；
3. **reward hacking**：policy 会找 RM 的漏洞刷高分（比如超长输出、奉承语气）→ 需要迭代重训 RM、在线过滤、KL 约束三件套。

### 2.7 PPO vs 邻居算法（一张表收束 Week 17 前半程）

| | REINFORCE | Actor-Critic | **PPO** | DQN |
|---|---|---|---|---|
| 学习对象 | 策略 | 策略 + 值 | 策略 + 值 | 值 |
| 数据利用 | 一次即弃 | 一次即弃 | **多 epoch 复用** | 经验回放复用 |
| 更新约束 | 无 | 无 | **clip ≈ 软信赖域** | 无（靠 target net 硬扛） |
| 方差控制 | baseline | TD | GAE + clip | 经验回放 |
| 连续性动作 | ✅ | ✅ | ✅ | ❌（max_a 枚举不了） |
| 实现难度 | 低 | 中 | 中 | 中 |
| 工业界地位 | 教学 | 基石 | **LLM RLHF 默认** | 游戏/离散控制 |

### 2.8 高频连环问速答

> 💬 **问：PPO 为什么能复用旧数据？不会引入偏差吗？**
>
> 🎯 重要性采样把期望换到旧分布下，理论上无偏；代价是方差随新旧策略偏离而增大。clip 把 r 钳在 [1−ε, 1+ε]，等于宣布"超出这个范围的数据贡献不可信"，用微小的有偏性换方差可控 —— 是又一次偏差-方差权衡，和 baseline、GAE 一脉相承。

> 💬 **问：min 换成 max 行不行？**
>
> 🎯 不行。min 取悲观界，防止优化器利用 r 的极端值刷目标函数（比如 r=10 时 10·A 虚高，但真实策略可能已经崩了）。换 max 就是鼓励往极端走，信赖域形同虚设。

> 💬 **问：clip 之后 r 超界的样本梯度是零，那部分数据不就浪费了？**
>
> 🎯 是的，这正是设计意图：超出信赖域的样本不再提供梯度信号，优化器被"软性拦住"。配合早停（KL 超阈值就提前跳出 epoch）可以进一步防止失控。

> 💬 **问：PPO 和 TRPO 到底什么关系？**
>
> 🎯 TRPO 用硬 KL 约束 + 二阶优化精确求解信赖域；PPO 用 clip 把约束近似进一阶目标函数。Schulman 自己的消融实验：PPO 效果≈TRPO，实现复杂度断崖下降。学术谱系上 PPO 是 TRPO 的"廉价可行化"。

> 💬 **问：PPO 的 Critic 学什么目标？和 Actor 会互相干扰吗？**
>
> 🎯 Critic 回归折扣回报 `R_t`（MC 或 GAE 折中），用 MSE/Huber loss。共享 backbone 时确实会互相干扰（representation 纠缠），工程上常用 value clipping、 separate value head、或干脆各自独立网络。

> 💬 **问：LLM 训练里 PPO 为什么有时候不如新出的算法？**
>
> 🎯 PPO 样本效率仍低（每条 rollout 只服务一轮更新）、Critic 在 token 级估值很难训准。于是有了 DPO（Day 111，绕开 RL 直接用偏好对做监督）和 GRPO（Day 112，删掉 Critic 改组内相对优势）。但 PPO 仍是"通用性最强、理论上最经得起推敲"的那个基准线。

### 2.9 Day 110 自检清单

- [ ] 默写 `L^CLIP = E[min(r·A, clip(r,1−ε,1+ε)·A)]`，并画出 A>0 时的目标函数形状（两段斜线 + 平台）
- [ ] 说出重要性采样为什么无偏、为什么方差会炸
- [ ] 手推 clip 在 r>1+ε 且 A>0 时梯度为零
- [ ] 说清 PPO 与 TRPO 的关系：硬约束二阶 vs 软约束一阶
- [ ] 一张纸默写 PPO 完整流程（采样 → GAE → K epoch 更新 → 同步 θ_old）
- [ ] 说出 GAE 中 λ=0 / λ=1 各退化为什么，γ 和 λ 的分工
- [ ] 报出经典超参组合：ε=0.2, γ≈0.99, λ≈0.95, K=4~10
- [ ] 说明 RLHF 中 KL 惩罚的三重作用：防 reward hacking、防语言退化、防 reward drift
- [ ] 说出"按权重随机选择"与"策略采样"的映射，及树状数组动态化方案

---

## 📚 今日参考

- Schulman et al. (2017), *Proximal Policy Optimization Algorithms*（arXiv:1707.06347）—— PPO 原论文，17 页人人能读
- Schulman et al. (2015), *Trust Region Policy Optimization*（TRPO）
- Schulman et al. (2016), *High-Dimensional Continuous Control Using GAE*
- Ouyang et al. (2022), *Training Language Models to Follow Instructions with Human Feedback*（InstructGPT，PPO in RLHF 的教科书案例）
- LeetCode 528. Random Pick with Weight
- LeetCode 398. Random Pick Index（昨天的等概率版，对比着看）
- Spinning Up in Deep RL（OpenAI 教程的 PPO 章节，推导最友好）

---

> 🧠 **今天的一句话**：REINFORCE 是裸奔的策略梯度，TRPO 给它修了城墙但门口设了收费站（二阶优化），PPO 则把墙画在目标函数上 —— 优化器看着像平路，走出 0.2 步自然崴脚停下。信任，但 clip。
