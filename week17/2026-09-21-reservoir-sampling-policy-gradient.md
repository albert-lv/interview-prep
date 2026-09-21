# Day 109 — 随机数索引（蓄水池抽样）+ 策略梯度 REINFORCE 与 Actor-Critic 🎯

> 📅 2026-09-21 · Week 17 Day 2 · 连续更新第 109 天
>
> 主题：强化学习与 RL 训练工程 🧠 · 从"无偏采样"到策略梯度

昨天用股票 III 打通了"值方法"（Bellman 方程 → Q-Learning → DQN）。今天换个完全不同的视角：**不学值函数，直接对策略本身做梯度上升**。这就是策略梯度（Policy Gradient）路线。

有意思的是，策略梯度的命脉是一个统计学问题：**如何用有限样本无偏地估计期望**。今天的算法题「蓄水池抽样」恰好就是采样无偏性的经典面试题 —— 题和考点又互为镜像了。

---

## 1) 今日算法题

### 随机数索引（Random Pick Index）

**题意**：给定一个可能**很长**（内存装不下 / 只能流式读取一次）的整数数组 `nums`，多次调用 `pick(target)`，要求**等概率**返回一个值等于 `target` 的下标。

```
输入: nums = [1,2,3,3,3], pick(3)
输出: 2, 2, 3, 4 ... （每次以 1/3 概率返回 {2,3,4} 中的某一个）
```

**约束**：不能预先把所有 target 下标存起来（流式遍历，只能看一遍）。

### 思路：蓄水池抽样（Reservoir Sampling）

核心是**等概率 + 单遍流式**两个约束。维护一个"当前答案" `ans`，遍历到第 `count` 个匹配元素时，**以 `1/count` 的概率用它替换 `ans`**。

遍历完第 `n` 个匹配后，每个匹配位置被选中的概率都是 `1/n`。归纳证明：

- 第 `i` 个匹配元素**在当时**被选中的概率 = `1/i`；
- 之后它还要**一路幸存**：第 `i+1` 个匹配以 `i/(i+1)` 概率不替换它，第 `i+2` 个以 `(i+2-1)/(i+2)` 不替换……直到第 `n` 个：

```
P(第 i 个元素最终存活) = 1/i × i/(i+1) × (i+1)/(i+2) × … × (n-1)/n = 1/n ✅
```

妙处：概率 `1/count` 只依赖"当前是第几个"，**不需要知道流的总长度** —— 这就是它能处理"装不下内存"的原因。

**推广（高频 follow-up）**：
- 要 `k` 个而不是 1 个 → 蓄水池容量为 `k`：先装满前 `k` 个，之后第 `i` 个元素以 `k/i` 概率**替换池中随机一个**，幸存概率同样是 `1/i × Π_{j=i+1}^{n} (1 - k/j · 1/k)` = `k/n`。典型应用：**从 10 亿条日志里随机抽 1000 条**。
- **加权抽样** → `w_i / Σw` 概率替换，Efraimidis-Spirakis 算法（key = u^(1/w) 取 top-k）。
- `rand7()` 生成 `rand10()` → **拒绝采样**：`7*7=49` 大数域采样，拒绝掉 40~48，把 0~39 映射到 1~10，期望调用次数 `49/40` 次。

### 代码

**Go：**

```go
type Solution struct {
    nums []int
    rng  *rand.Rand
}

func Constructor(nums []int) Solution {
    return Solution{nums, rand.New(rand.NewSource(time.Now().UnixNano()))}
}

func (s *Solution) Pick(target int) int {
    count, ans := 0, -1
    for i, v := range s.nums {
        if v != target {
            continue
        }
        count++ // 第 count 个匹配
        if s.rng.Intn(count) == 0 { // 以 1/count 概率替换
            ans = i
        }
    }
    return ans
}
```

**Python（极简版）：**

```python
import random

class Solution:
    def __init__(self, nums):
        self.nums = nums

    def pick(self, target):
        count, ans = 0, -1
        for i, v in enumerate(self.nums):
            if v == target:
                count += 1
                if random.randint(1, count) == 1:  # 1/count 概率替换
                    ans = i
        return ans
```

### 复杂度

| 维度 | 复杂度 | 说明 |
|---|---|---|
| 时间 | `O(n)` | 单遍扫描，每个元素 O(1) 决策 |
| 空间 | `O(1)` | 只存一个答案，这就是蓄水池的意义 |
| 采样次数期望 | `O(n)` | pick 一次要扫一遍，多次调用可用"先扫描一遍存位置"的时空权衡 |

### 面试官连环问

> 💬 **问：为什么不能先 `rand.Intn(len(matches))`？**
>
> 🎯 答：因为流式场景下 `matches` 要遍历完才知道长度，而蓄水池要求**一遍过**。如果数组可以反复读，那种做法当然更好 —— 这说明你理解了两种约束下的权衡。

> 💬 **问：分布式场景怎么做？**（10 台机器各存一段，各跑蓄水池抽 k 个，怎么合并？）
>
> 🎯 答：每台本地抽 `k` 个并记录各自的 `count_i`；汇总时按 `count_i` 加权做**第二轮蓄水池抽样**（第 i 组的元素以 `count_i / Σcount` 的概率进入最终池），数学上等价于全局蓄水池。

> 💬 **问：数据流无限长怎么办？**
>
> 🎯 答：如果只要"采样"，蓄水池天然支持无限流（概率只依赖已见数据量）。如果还要**精确的计数 / 分位数**，就得用 Count-Min Sketch、HyperLogLog 这类Sketch 近似结构 —— 这是另一组"用有界误差换有界内存"的权衡。

---

## 2) 面试技巧：策略梯度 REINFORCE 与 Actor-Critic

### 2.1 为什么需要策略梯度？值方法的三大天花板

值方法（Q-Learning / DQN）要 `max_a Q(s,a)`，遇到三个过不去的坎：

| 值方法的坎 | 策略梯度的解法 |
|---|---|
| **动作空间连续**（机械臂角度、方向盘转角）：`max_a` 无法枚举 | 策略网络直接输出**动作分布**（高斯：均值+方差），采样即可 |
| **随机策略有时更优**：石头剪刀布最优解是随机出招，确定性策略会被针对 | 参数化 `π(a|s;θ)` 天然支持随机性 |
| **部分可观测（POMDP）**：同样的观测需要不同动作 | 随机策略是信息不足时的理性选择 |

一句话：**值方法是"先估值再贪心"，策略梯度是"直接对策略做梯度上升"。**

### 2.2 策略梯度定理：一句话 + 一个 trick

**目标函数**：

```
J(θ) = E_{τ ~ π_θ} [ R(τ) ]          （τ 是一条轨迹，R(τ) 是总回报）
```

**策略梯度定理**：

```
∇J(θ) = E_{τ ~ π_θ} [ Σ_t ∇log π(a_t | s_t; θ) · G_t ]
```

其中 `G_t = Σ_{k≥t} γ^{k-t} r_k` 是从 t 时刻起的折扣回报。

**核心 trick —— log-derivative（对数导数）**：

```
∇p(x) = p(x) · ∇log p(x)
```

于是"对期望求梯度"被变形成"在**原分布下**对 log 概率加权求期望"，**采样分布不用变**（对比重要性采样要换分布，这里零成本）。配上score function 恒等式 `E_{a~π}[∇log π(a|s)] = 0`（概率归一，梯度为零），整个推导无懈可击。

**直觉**：`∇log π(a|s)` 是"让这个动作更可能发生"的方向；乘以 `G_t`（这个动作好不好）后，**好动作概率调大、坏动作概率调小** —— 这就是 REINFORCE 的全部思想。

### 2.3 REINFORCE：蒙特卡洛版策略梯度

```
循环每一个 episode:
    用当前策略 π_θ 采样一整条轨迹 τ = (s_0,a_0,r_0, s_1,a_1,r_1, …)
    对每个时刻 t 计算 G_t（该轨迹上从 t 开始的折扣回报）
    θ ← θ + α · Σ_t ∇log π(a_t|s_t) · G_t
```

三步说完：**采样一整局 → 算每个动作的回报 → 按回报加权放大/缩小动作概率**。

名字来源：Williams (1992) 论文标题 *Simple Statistical Gradient-Following Algorithms for Connectionist Reinforcement Learning* 里这类算法的统称，后来就叫 REINFORCE。

### 2.4 方差问题与 Baseline：REINFORCE 的阿喀琉斯之踵

**问题**：`G_t` 是随机变量的和，方差巨大。一条轨迹 1000 步，第一步的 `G_0` 累积了 1000 个随机奖励 —— 早期动作几乎被噪声淹没，收敛极慢。

**Baseline 减方差**：把更新式改成

```
θ ← θ + α · Σ_t ∇log π(a_t|s_t) · (G_t − b(s_t))
```

**为什么无偏？** 因为减去的项期望为零：

```
E_{a~π}[ ∇log π(a|s) · b(s) ] = b(s) · Σ_a π(a|s) · ∇log π(a|s)
                              = b(s) · ∇ Σ_a π(a|s)
                              = b(s) · ∇ 1 = 0 ✅
```

**最优 baseline**：`b(s) = E_a[Q(s,a)]`，实际常用 `V(s)`。方差分解告诉我们：回报拆成 `A = Q − V`（优势）后，**随机性只来自动作维度**，方差最小。

工程三板斧：**减 baseline、分配合适的 γ（衰减也能降方差，但引入偏差）、用多个 episode 平均梯度**。

### 2.5 Actor-Critic：用 Critic 替掉蒙特卡洛

REINFORCE 要等一整局结束才能更新（MC，慢且方差大）。**Actor-Critic** 的解法：**训练一个值函数 Critic，用它的 bootstrap 估计替代 G_t**。

| 组件 | 角色 | 更新方式 |
|---|---|---|
| **Actor**（策略网络） | 负责**行动**：输出动作分布 | `θ ← θ + α ∇log π(a\|s) · A(s,a)` |
| **Critic**（价值网络） | 负责**打分**：评估动作好坏 | TD 更新：`V(s) ← V(s) + β · (r + γV(s') − V(s))` |

**优势函数** `A(s,a) = Q(s,a) − V(s)`，用 TD 误差 `δ = r + γV(s') − V(s)` 近似 Q−V 后，Actor 的更新变成：

```
θ ← θ + α · ∇log π(a|s) · δ
```

**MC vs TD 在策略梯度中的对照**（正好复习 Day 108 的知识）：

| | REINFORCE | Actor-Critic |
|---|---|---|
| 回报估计 | MC 采样 `G_t`（真实终局回报） | TD bootstrap `r + γV(s')` |
| 偏差 | 无偏 | **有偏**（bootstrap 依赖 Critic 的估计） |
| 方差 | 大 | 小 |
| 更新时机 | 必须等 episode 结束 | **每步都能更新** |
| 策略 | on-policy | on-policy |

这就是经典的**偏差-方差权衡**：Critic 用一点偏差换来了方差的断崖式下降 + 在线更新能力。GAE（Generalized Advantage Estimation，明天 PPO 会用到）就是在这条线上继续调参。

### 2.6 从算法到 agentic RL 工程

这是面试的"降维打击"环节 —— 把经典 RL 和 LLM 训练对上号：

| RL 概念 | agentic RL / LLM 中的对应 |
|---|---|
| 轨迹 τ | 一次 **rollout**：模型生成完整回答（可能多轮工具调用） |
| 状态 s_t | 当前 **context**（prompt + 已生成的 token / 工具结果） |
| 动作 a_t | 下一个 **token**（或一次工具调用） |
| 策略 π(a\|s;θ) | LLM 本身，`log π` 就是 token 的 log-prob 求和 |
| 奖励 r | **RM 打分 / 规则校验**（单条轨迹末端通常只有一个标量） |
| `G_t` | 从 t 开始的后续奖励总和（常 γ=1，只看终局奖励） |
| baseline / Critic | **reward baseline**（一批 rollout 的均值、或 value model） |
| REINFORCE | 朴素 RLHF 的 policy gradient 版 |
| Actor-Critic → PPO | 加 KL 约束 + clipping 后的稳定版（Day 110） |

工程上常被追问的点：**为什么 LLM 场景 G_t 通常退化成"终局奖励"**（只有最后一条 verifier 反馈）→ 信用分配更难，所以 advantage 估计、过程奖励模型（PRM）、以及 GRPO 的"组内相对优势"（Day 112）都是围绕"奖励太稀疏"做文章的。

### 2.7 高频连环问速答

> 💬 **问：策略梯度为什么是无偏的？**
>
> 🎯 采样分布就是 π_θ 本身（on-policy），log-derivative trick 后梯度表达式不含别的分布 → 天然无偏。代价是**必须 on-policy**（旧数据 log-prob 变了就不能用），样本效率低，这是 PPO 要解决的头号问题。

> 💬 **问：baseline 会引入偏差吗？为什么？**
>
> 🎯 不会。`E[∇log π(a|s)·b(s)] = b(s)·∇Σπ = 0`，任何只依赖状态的函数做 baseline 都无偏。这是面试高频推导题，**现场能手推**立刻加分。

> 💬 **问：REINFORCE 和 Q-Learning 的本质区别？**
>
> 🎯 三件事：① 学的对象不同（策略 vs 动作值）；② 是否需要 `max_a`（值方法需要，策略梯度不需要 → 连续动作友好）；③ 稳定性不同（策略梯度是梯度上升保证局部收敛，Q-Learning 的 `max` 与 bootstrapping 组合会发散，需要 target network 等补丁）。

> 💬 **问：Actor-Critic 的 Critic 用什么目标训练？**
>
> 🎯  TD 回归：最小化 `(r + γV(s') − V(s))²`。可以单步 TD（低方差高偏差）、MC（无偏高方差）、或 n-step / GAE 折中。说得出"折中"说明你懂方差从哪来。

> 💬 **问：熵正则（entropy bonus）是干什么的？**
>
> 🎯 目标函数加 `+ β·H(π(·|s))`，奖励探索、防止策略过早坍缩成确定性（高斯策略方差归零 / softmax 尖锐化）。LLM RL 里它就是"别让模型把 log-prob 全压到几个 token 上"，和 KL 约束是亲兄弟。

> 💬 **问：策略梯度能用于离散 + 连续混合的动作空间吗？**（LLM：token 离散 + 工具参数连续）
>
> 🎯 能。每个维度按各自参数化采样（离散用 softmax、连续用高斯），`∇log π` 是各分量的 log-prob 之和，求导线性可加。这也是为什么 PPO 能同时 fine-tune 文本和工具调用。

### 2.8 Day 109 自检清单

- [ ] 手推 log-derivative trick：∇p = p·∇log p，30 秒内
- [ ] 默写策略梯度定理：`∇J = E[Σ ∇log π · G_t]`
- [ ] 说出 REINFORCE 完整流程（采样 → G_t → 更新），三步版
- [ ] 手推 baseline 无偏性：`E[∇log π·b(s)] = 0`
- [ ] 说出 REINFORCE vs Actor-Critic 的偏差/方差/更新时机对照表
- [ ] 说出 agentic RL 映射表：轨迹=rollout、状态=context、动作=token、奖励=RM
- [ ] 说出熵正则的作用 + 和 KL 约束的关系
- [ ] 蓄水池抽样的幸存概率连乘公式 + 归纳证明

---

## 📚 今日参考

- Sutton & Barto《Reinforcement Learning》第 13 章（Policy Gradient Methods）
- Williams (1992), *Simple Statistical Gradient-Following Algorithms for Connectionist RL*
- LeetCode 398. Random Pick Index
- LeetCode 382. Linked List Random Node（蓄水池的链表版）
- Voldemort 的分布式蓄水池抽样论文：*Building a Distributed Sampling Service*（LinkedIn 工程博客）

---

> 🧠 **今天的一句话**：值方法是"给动作打分再选最好的"，策略梯度是"直接调教策略，好就夸、差就骂" —— 而 REINFORCE 到 Actor-Critic 的进化，就是用一点偏差换一整个世界的方差。
