# Day 112 — 打乱数组（Fisher-Yates 洗牌）+ GRPO 算法详解 🎰

> 📅 2026-09-24 · Week 17 Day 5 · 连续更新第 112 天
>
> 主题：强化学习与 RL 训练工程 🧠 · 从"四个模型"到"两个模型"，再到"干掉 Critic"

Week 17 的推进路线已经明朗：Day 108 打 MDP / Bellman 的地基，Day 109 上策略梯度，Day 110 学 PPO——InstructGPT 对齐用的大杀器，Day 111 学了 DPO——用监督学习的成本解 RL 的目标。

但 DPO 有个硬伤：**它是 offline 的，不会在数据分布之外探索**。数学推理、代码、agent 多步轨迹这些"答案可验证"的任务，恰恰最需要在线探索——rollout 采出新样本、试错、自我修正。于是 2024 年 2 月，DeepSeekMath 论文提出了 **GRPO（Group Relative Policy Optimization）**，2025 年 1 月 DeepSeek-R1 用它把这条路线推到了聚光灯正中央：**不做 SFT，直接从 base 模型纯 RL 训练，AIME 数学竞赛准确率从 15.6% 干到 71.0%**，还涌现出了"啊哈时刻"式的自我反思行为。开源社区为之疯狂，复现项目把成本压到几百美元量级。

GRPO 对 PPO 的手术干净利落：**把 Critic（价值模型）整个删掉**。PPO 在 LLM 上最痛的不是 clip，不是 KL，是那个永远训不稳的 value model——而 GRPO 发现，**一组采样回答的组内均值，就是最好的基线**。

今天的算法题「打乱数组」看似人畜无害，实则正中要害：GRPO 每个 prompt 要采样 G 条 rollout，**采样的均匀性就是训练的公平性**。Fisher-Yates 洗牌是所有均匀采样的祖师爷，而 `rand() % n` 的隐性偏差，就是工程上最常见的"采样污染"。

---

## 1) 今日算法题

### 打乱数组（Shuffle an Array）

**题意**：实现一个类 `Solution`，支持：

- `Solution(int[] nums)`：用整数数组初始化；
- `int[] reset()`：重置为初始数组；
- `int[] shuffle()`：随机返回数组的一个**排列**，要求 `n!` 个可能排列**每个都等概率出现**。

```
输入: ["Solution", "shuffle", "reset", "shuffle"]
      [[[1, 2, 3]], [], [], []]
输出: [null, [3, 1, 2], [1, 2, 3], [1, 3, 2]]
解释: shuffle 返回 [3,1,2] 或 [1,3,2] 或其他排列的概率都必须恰好是 1/6
约束: 1 <= nums.length <= 200（面试中按通用 n 处理）
```

**关键约束**：等概率的是"排列"（permutation），不是"每个位置独立随机"——后者会产生重复值（比如从原数组有放回抽取），必须排除。

### 思路：Fisher-Yates 洗牌（从后往前，逐步缩小随机域）

算法一句话：**从最后一个位置开始往前，第 i 步从 `[0, i]` 中均匀取一个 j，交换 a[i] 和 a[j]`**。

```
初始: [1, 2, 3, 4, 5]
i=4: 从 [0,4] 随机取 j=2 → 交换 → [1, 5, 3, 4, 2]   ← 位置 4 定了
i=3: 从 [0,3] 随机取 j=0 → 交换 → [4, 5, 3, 1, 2]   ← 位置 3 定了
i=2: 从 [0,2] 随机取 j=2 → 不变 → [4, 5, 3, 1, 2]   ← 位置 2 定了
i=1: 从 [0,1] 随机取 j=0 → 交换 → [5, 4, 3, 1, 2]   ← 位置 1 定了
i=0: 只剩自己，收工
结果: [5, 4, 3, 1, 2]，概率 1/5! = 1/120
```

**为什么每个排列等概率？（归纳证明，面试标准答法）**

看"位置从后往前依次被填上"的过程：

- 位置 n−1 的元素：第 0 步从 n 个里等概率选 → 任意特定元素落在位置 n−1 的概率 = **1/n**；
- 位置 n−2 的元素：第 1 步从剩下 n−1 个里等概率选 → 任意剩下元素落在位置 n−2 的概率 = **1/(n−1)**；
- ……
- 位置 0 的元素：1/1。

于是**任意一个特定排列**出现的概率 = 1/n × 1/(n−1) × … × 1/1 = **1/n!**。每个排列概率都相等 → 均匀。✅

还有一个更对称的视角：**每个元素最终出现在每个位置的概率都恰好是 1/n**（可以画 n×n 的概率方阵验证）。注意"每个位置边缘分布均匀"是必要条件不是充分条件——Fisher-Yates 强在它保证的是整个联合分布 1/n!，而不是 n 个独立的 1/n。

**为什么从后往前而不是从前往后随便选？** 从后往前的每一步，"已确定的后缀"恰好把问题规约成"给前 i+1 个元素均匀洗牌"的子问题，归纳结构干净。Knuth 在 TAOCP 里给的就是这个形式（所以也叫 Knuth Shuffle）。从前往后写 `for i: j = rand in [i, n)` 是等价的对偶形式，别混成 `j = rand in [0, n)`——那就是错的了。

### ⚠️ 工程陷阱：`rand() % n` 的取模偏差（modulo bias）

这是本题最值钱的部分。面试写出 Fisher-Yates 只是及格，能聊清下面这点才是加分：

如果底层随机数发生器产生的是 `[0, RAND_MAX]` 均匀整数，那么 `rand() % n` **不是均匀的**——除非 n 整除 `RAND_MAX + 1`。例子：`RAND_MAX = 2³¹ − 1`，`n = 3`，则 `2³¹ = 2147483648 = 3 × 715827882 + 2`——余数为 0、1 的区间比余数为 2 的区间各多 1 个数，于是 0、1 出现的概率 ≈ (715827883)/2³¹，2 出现的概率 ≈ (715827882)/2³¹，偏差 ~4.7×10⁻¹⁰。单次无所谓，洗十亿次牌，这个偏差就是可测的。

**修复姿势（三选一）：**

1. **拒绝采样**：不断取随机数直到落在 `n × floor((RAND_MAX+1)/n)` 之下，再取模（Day 109 的 rand7→rand10 同款思想）；
2. **用靠谱的库**：Go 的 `math/rand/v2`、Python 的 `random`、C++ 的 `<random>` 配 `uniform_int_distribution`，库内部已经处理了拒绝采样；
3. **密码学场景换 CSPRNG**：洗牌彩票号码、发牌，必须用 `crypto/rand` 级别的源，普通的 PRNG（线性同构）输出可被预测，"均匀"之外还要求"不可预测"。

> 💡 **连接 GRPO**：GRPO 每个 prompt 采 G=8~64 条 rollout，采样偏差 = 隐性的 reward 偏差——模型"以为"自己在均匀探索，实际某些回复被系统性地过采样/欠采样，组内基线就被污染。RL 工程师对 RNG 的敬畏，就是从这种地方长出来的。

### 代码

**Go：**

```go
import (
    "math/rand/v2" // v2：API 清理 + 更好的算法（PCG）
)

type Solution struct {
    original []int
    nums     []int
}

func Constructor(nums []int) Solution {
    cp := make([]int, len(nums))
    copy(cp, nums)
    return Solution{original: nums, nums: cp}
}

func (s *Solution) Reset() []int {
    out := make([]int, len(s.original))
    copy(out, s.original)
    return out
}

func (s *Solution) Shuffle() []int {
    n := len(s.nums)
    // Fisher-Yates：从后往前，第 i 步在 [0, i] 中等概率取 j
    for i := n - 1; i > 0; i-- {
        j := rand.IntN(i + 1) // rand.IntN 内部处理了取模偏差
        s.nums[i], s.nums[j] = s.nums[j], s.nums[i]
    }
    out := make([]int, n)
    copy(out, s.nums)
    return out
}
```

**Python：**

```python
import random

class Solution:
    def __init__(self, nums):
        self.original = list(nums)
        self.nums = list(nums)

    def reset(self):
        self.nums = list(self.original)
        return self.nums

    def shuffle(self):
        n = len(self.nums)
        # Fisher-Yates：从后往前，第 i 步在 [0, i] 中等概率取 j
        for i in range(n - 1, 0, -1):
            j = random.randint(0, i)  # 闭区间 [0, i]
            self.nums[i], self.nums[j] = self.nums[j], self.nums[i]
        return self.nums
```

> ⚠️ **边界陷阱**：① 循环边界是 `i > 0` 还是 `i >= 1`（等价），但 i=0 那步 `rand.IntN(1)` 恒为 0，交换自己，可以省掉；② 必须深拷贝返回，不能返回内部切片的引用，否则调用方改乱数组，下次 shuffle 就不均匀了；③ `random.randint(0, i)` 是闭区间，别写成 `randrange(i)`（那是 `[0, i)`，漏了 j=i 的合法分支，分布仍然均匀但和经典实现不等价，面试官会追问）。

### 复杂度

| 维度 | 复杂度 | 说明 |
|---|---|---|
| 时间 | `O(n)` | 一次扫描 + n 次交换 |
| 空间 | `O(1)` | 原地交换（不计返回副本的 O(n)） |

对比"把数组元素放进集合、每次随机抽一个出来"的 `O(n log n)`（或者 hash 集合均摊 O(n) 但常数大、还要处理碰撞）——Fisher-Yates 是最优的，信息论下界就是 Ω(n)（每个元素至少被碰一次）。

### 面试官连环问

> 💬 **问：怎么**测试**你的 shuffle 是均匀的？**
>
> 🎯 答：统计检验。跑 N 次（N ≫ n!），对每个排列计数，做**卡方检验** `χ² = Σ (observed − expected)² / expected`，其中 expected = N/n!；χ² 在自由度 n!−1 下应服从卡方分布，p-value 不能太离谱。辅助指标：位置边缘分布（每个元素在每个位置频率 ≈ 1/n）、相邻位置相关性（相邻两次结果不应相关）。工程上加一条：固定 seed 的确定性测试，保证回归一致。

> 💬 **问：要在 10 亿条数据里随机抽 100 万条（数据流），怎么扩展洗牌？**
>
> 🎯 答：三条路线按场景选：① **蓄水池抽样**（Day 109）：单遍流式等概率抽 k 个，O(n) 时间 O(k) 空间，恰好做过；② **随机键 + 排序**：给每条数据生成随机 key，全排序后取前 k 条——shuffle 的等价形式（sort by random key ≈ permutation），分布式系统天然友好（MR 一次 shuffle 搞定）；③ **Fisher-Yates 的流式变体**：顺序读，第 i 条以 k/i 概率替换结果池中的随机位置——本质就是 ①。分布式加权版本用 Efraimidis-Spirakis（Day 110 的 key=u^(1/w)）。

> 💬 **问：只想要"随机取 m 个，不需要完整洗牌"（m ≪ n）？**
>
> 🎯 答：Partial Fisher-Yates：只跑前 m 步 `for i := n-1; i > n-1-m; i--`，总代价 O(m)。同样保证任意 m 子集的等概率性。这就是"从 n 个里无放回随机抽 m 个"的最优解——抽奖、A/B 分桶、训练集/验证集划分的底层都是它。

> 💬 **问：两个玩家联机打牌，要求洗牌结果可验证公平（事后能审计），怎么做？**
>
> 🎯 答：密码学承诺（commitment）方案：发牌者生成随机排列后，把"每个位置的牌的哈希承诺"先公布（或发给可信第三方）；牌局结束后公布原始排列与随机数，任何人可重算哈希验证与承诺一致。更骚的方案：commit-reveal 多方各自贡献随机种子，异或出最终 RNG 种子（类似以太坊 RANDAO / drand 信标），谁也别想单独操控洗牌结果。考点：均匀性之外的**可验证性**与**抗预测性**。

---

## 2) 面试技巧：GRPO（Group Relative Policy Optimization）

### 2.1 承上启下：PPO 留下来的烂摊子

先把 Week 17 的矛盾链捋直：

- **PPO-RLHF**（Day 110）：效果好，但四个模型同时在线（policy / ref / RM / value），Critic 尤其难伺候——value head 初始化玄学、GAE 的 λ 玄学、token 级信用分配玄学；训崩一天 GPU 起跳。
- **DPO**（Day 111）：砍成两个模型、监督训练，稳得可以单卡跑——但 offline，不探索，天花板被数据分布锁死。
- **GRPO 的定位**：留在 RL 阵营，但把 Critic **整个删掉**——这就是 DeepSeekMath（2024.02）的原话：既然 value model 是给 advantage 提供基线的，那我用**同一 prompt 下 G 条采样的组内均值**当基线，不就行了？

于是 PPO 四模型 → GRPO 两模型（policy + ref；β=0 时甚至只要一个），而且 value model 带来的那堆玄学超参（λ、value clip、value loss 系数）**全部消失**。

### 2.2 GRPO 核心流程（一张图背下来）

```
对每个 prompt q（来自训练集）:
  ① 用旧策略 π_θ_old 采样一组 G 条回答 {o_1, ..., o_G}
  ② 逐条算奖励 r_1, ..., r_G
     ├─ 数学：boxed 答案比对（对 = 1，错 = 0）        ← 可验证奖励，无需 RM！
     ├─ 代码：单元测试通过率
     ├─ agent：任务终局成功信号
     └─ 通用场景：也可以是 RM 打分（GRPO 本身不排斥 RM）
  ③ 组内归一化算优势：
       A_i = (r_i − mean(r_1..r_G)) / (std(r_1..r_G) + ε)   ← DeepSeekMath 原版
       A_i = r_i − mean(r_1..r_G)                            ← DeepSeek-R1 版（去掉 std）
  ④ PPO 同款 surrogate loss + KL 惩罚：
       L = − E[ (1/G) Σ_i (1/|o_i|) Σ_t min(ρ_t·A_i, clip(ρ_t, 1±ε)·A_i) ] + β·D_KL
  ⑤ 梯度上升 max（或 loss 下降），更新 π_θ
```

**逐项拆解：**

| 符号 | 含义 | 和 PPO 的关系 |
|---|---|---|
| `G` | 组大小（group size） | PPO 没有的概念。典型 8~64，DeepSeekMath 用 64 |
| `r_i` | 第 i 条回答的奖励 | 同源（RM 或规则） |
| `mean(r)` | **组内基线** | ★ GRPO 的灵魂：替代 value model |
| `/std(r)` | 组内标准差归一化 | R1 删了它（见 2.5） |
| `ρ_t = π_θ/π_θ_old` | 重要性采样比 | PPO 同款 |
| `clip(ρ, 1±ε)` | 信赖域裁剪 | PPO 同款，ε=0.2 经典值 |
| `β·D_KL` | 对 ref 的锚定 | PPO-RLHF 放目标函数里；GRPO 论文也放目标里，R1 实现里更常见的做法是直接揉进奖励 |
| `1/|o_i|` | token 均值化 | Dr.GRPO 论文指出它有长度偏置（见 2.5） |

### 2.3 组均值基线：为什么它对？哪里又不完美？

**直觉**：同一道题采 G 个回答，有的对有的错。把 G 条的平均分当"这道题的平均表现"，每条回答的优势 = 它比同组平均水平好多少——**在同一张考卷上排名次**，比跨考卷排名次公平。这个设计自动完成了 **难度校准**：

- 难题（全组全错）：mean=0，所有 A_i≈0，**不产生梯度**——不把训练预算浪费在超纲题上；
- 易题（全组全对）：同样 A_i≈0，自动跳过；
- 中等题（有的对有的错）：梯度信号最丰富，模型从"差一点就对了"的回答里学到最多。

这就是 GRPO 数据效率的来源之一，也是它天然适配可验证奖励的原因：**reward 只有对错两个取值时，难度校准就是组内均值唯一要做的事**。

**严格性追问：组均值基线无偏吗？**

不完美，两个瑕疵：

1. **基线包含自身**：A_i 的均值里混入了 r_i 自己。G 越大污染越小（1/G 量级），但理论上严格的做法是 **leave-one-out**——A_i 用除自己外 G−1 条的均值。这就是 RLOO 论文（Ahmadian et al. 2024）的核心论点，实测略有提升。
2. **G 有限 → 蒙特卡洛噪声**：组均值只是"该 prompt 期望奖励"的 G 样本估计。G→∞ 时趋于真基线；G=8 时方差可观。std 归一化原本就是在补这个洞。

面试答法：**"组均值是 value model 的免训练蒙特卡洛近似——有 1/G 量级的自包含偏差和采样方差，但换来零成本、零超参、难度自校准。工程上 G 取 8~64 是偏差-方差-算力的 sweet spot。"**

### 2.4 KL 惩罚与 k3 估计器

GRPO 的 KL 项 `D_KL(π_θ ‖ π_ref)` 在 token 级别无法解析求（动作空间是词表 × 序列，求和不可能），只能**采样估计**。在 rollout 的 token 上，工程标准做法是 **k3 无偏估计器**（Schulman, 2020）：

```
令 δ_t = log π_ref(a_t|s_t) − log π_θ(a_t|s_t)

per-token KL̂ = e^{δ_t} − δ_t − 1        ← k3 估计器
```

三条性质（面试要能默写）：

1. **非负**：`e^x − x − 1 ≥ 0`，等号仅在 x=0（即 π_θ = π_ref）取得——每个 token 都在"远离 ref"时产生惩罚，方向感干净；
2. **采样下无偏**：在从 p 采样的样本上，`E[r − 1 − log r]`（r = q/p 形式）恰好等于 KL(p‖q)——数学上一行就能推（E_p[q/p] = 1）；
3. **比 k1 稳**：朴素估计 `δ`（k1）可正可负、方差大，蒙特卡洛平均会互相抵消出毛刺；k3 逐点非负，loss 曲线好看得多。

R1-Zero 的经验：**必须加 KL**。完全不加，纯规则奖励会把 base 模型拉得语言漂移（中英混杂、输出格式崩坏）——这和 DPO 的 degeneration 病理是同源病（Day 111 预告过）：**只给方向、不给锚，模型就会走捷径**。开源复现（TinyZero、Open-R1、TRL）里大量实验是 `β=0` 直接冲的——在小模型短任务上可行，但上了规模 KL 就是保险丝。

### 2.5 争议与修正：std 归一化和 token 均值化的两桩公案

**公案一：R1 为什么去掉 std 归一化？**

DeepSeekMath 原版 `A_i = (r_i − mean)/std`。R1 改成 `A_i = r_i − mean`。理由：

- 二值 reward（0/1）下，std 由"组内答对比例"决定：一组 7 错 1 对，std 小 → 那唯一正确的回答 A 被放大 G−1 倍；一组 4 对 4 错，同样的"答对"只被放大 1 倍。**奖励相同的回答，梯度权重差出几倍**——而这仅仅因为它同组的错误率高。
- std 归一化本意是抵消 reward 的尺度差异，但当 reward 本身就是有界的（0/1），它引入的不是校准，是**难度相关的随机缩放**。

**公案二：`1/|o_i|` token 均值化有长度偏置？**

GRPO loss 对每个回答先按 token 数取均值，再加总。Dr.GRPO（2025）指出：长回答的逐 token 梯度被摊薄，模型被系统性激励"**短答蒙对**"；正确做法是在组内做 token-sum 然后整体归一（或用长度无关的 baseline）。R1 是否采纳有争议，但**面试被问到 GRPO 的已知坑，这条是 2025 年的标准答案**。

### 2.6 GRPO vs PPO vs DPO vs REINFORCE 收束大表

| 维度 | REINFORCE | PPO(-RLHF) | DPO | **GRPO** |
|---|---|---|---|---|
| 基线 | 常数/baseline 网络 | value model + GAE | 无（成对比较自带） | **组内均值（免训练）** |
| 同时在线模型 | 2（policy+ref） | 4 | 2 | **2（β=0 时 1）** |
| 采样 | on-policy rollout | on-policy rollout | 无 | **on-policy，每 prompt G 条** |
| 信赖域 | 无 | clip + KL | KL（β 锚定） | clip + KL |
| 奖励 | 标量 | RM | 隐式（偏好对） | **可验证规则 / RM 皆可** |
| 探索能力 | 有 | 有 | 无 | **有（且鼓励多样组内对比）** |
| 数据效率 | 低（每轨迹一次更新） | 中 | 高（监督） | **中高（同 prompt 组内对比 + 难度自校准）** |
| 主要痛点 | 方差爆炸 | Critic 玄学 + 四模型 | offline 天花板 | token 信用分配 + 规则可被 hack |
| 代表作 | — | InstructGPT | Zephyr | **DeepSeekMath / R1** |

还有一个灵魂追问的答案藏在表里：**GRPO ≈ REINFORCE + 组基线 + PPO clip + KL 锚定**。DeepSeekMath 自己的消融显示 PPO-clip 相对 REINFORCE-clip 增益不大——clip 的真实价值在 multi-epoch 复用数据（off-policyness 保险）时体现。所以别把 GRPO 神化成新范式，它是**经典组件的精准重排**，而重排的出发点是大模型工程（杀 Critic）。

### 2.7 DeepSeek-R1：为什么 GRPO 出圈了

**R1-Zero 的配方（2025.01，arXiv:2501.12948）：**

```
底座：DeepSeek-V3-Base（预训练完、没做过任何对齐 SFT！）
奖励：accuracy（数学 boxed 答案对/错）+ format（思考必须包在 <think> 里）
算法：GRPO，大规模
结果：AIME 2024 pass@1 15.6% → 71.0%（@64 多数投票 86.7%，对标 o1-1217 的 74.4%/83.3%）
```

过程中观察到的涌现行为：CoT 长度随训练持续增长、出现"啊哈时刻"（自我怀疑→重试→修正）、自我验证。论文自己都说：这是 RL 的激励让 base 模型**预训练里沉睡的推理能力被唤醒并显性化**——不是 RL 凭空造出来的。

**R1 的完整版配方**（R1-Zero 有两个病：语言混杂、可读性差）：

```
少量高质量长 CoT 冷启动 SFT（几千条）
  → GRPO（加语言一致性奖励）
  → 拒绝采样造 80 万条 SFT 数据
  → 全量 SFT
  → 第二轮 GRPO（对齐人类偏好）
```

MIT 协议开源 → 社区海啸：Open-R1（HuggingFace 官方复现）、TinyZero（小成本复现 aha moment）……"R1-Zero-like training" 成为 2025 上半年最高频的复现关键词。**对面试的意义：GRPO 从"一个 PPO 变体"升级成了"开源推理模型的标准训练范式"。**

### 2.8 工程落地要点（veRL 视角预告）

- **瓶颈在 rollout**：GRPO 的算力大头是 G 倍于 PPO 的采样生成。工业解法：vLLM/SGLang 做分离式 rollout 引擎、与 trainer 权重同步（NCCL/RPC）、continuous batching、partial rollout 流水线。Day 113 讲 veRL 架构时把这些拆开。
- **K epoch 别贪**：组内数据复用 K 个 epoch 时 ρ 会漂，K 取 1~4 保守；漂了 clip 兜底的代价是有效梯度变少。
- **reward 设计是新的 reward hacking 面**：规则越具体，hack 越精确（数学格式作弊、代码空过测试）。对抗手段：多 reward 加权、 held-out 测试集、人工抽检、奖励复杂度守恒。
- **和 DPO 的配方分工**：DPO 管"风格、偏好、表达"（数据分布内的对齐），GRPO 管"可验证推理能力"（需要探索的）。SFT → DPO → GRPO 是 2025 年开源 post-training 的主流三段式——这也正好呼应 veRL 生态：DPO 打底，GRPO 冲顶（Day 111 结尾预告过，今天兑现）。

### 2.9 高频连环问速答

> 💬 **问：GRPO 和 PPO 的本质区别是什么？**
>
> 🎯 答：三件事：① **删 Critic**——value model 和 GAE 全部不要，用同 prompt 下 G 条采样的组内均值当基线；② **KL 进奖励**（多数实现）——token 级 k3 估计器算 π_θ 对 π_ref 的 KL，直接罚在 reward 上；③ **优势按组归一化**——每条回答的 advantage 相对同组校准，天然难度感知。clip、重要性采样比这些 PPO 组件原样保留。

> 💬 **问：组均值基线无偏吗？**
>
> 🎯 答：渐近无偏、有限 G 有偏：G→∞ 时组均值 → 该 prompt 在旧策略下的期望奖励，A_i 就是标准优势；有限 G 有两处偏差——基线含自身（1/G 量级，RLOO 用 leave-one-out 修）和蒙特卡洛方差（std 归一化在补，但 R1 因它会引入难度相关缩放而删掉）。总体是"偏差-方差-成本"三方的甜点：一个免费基线，换来免训练、免超参、难度自校准。

> 💬 **问：G 取多大？为什么不是越大越好？**
>
> 🎯 答：8~64 是常见区间，DeepSeekMath 用 64。收益递减：G 翻倍，基线方差减半，但 rollout 成本翻倍——而 rollout 本来就是 GRPO 的瓶颈。另外 G 大了对 prompt 的"局部难度"估计更准，但全局多样性没增加（同一 prompt）。实践中按 reward 噪声程度定：二值 reward、难题占比高 → G 大些；reward 平滑 → G 小些也稳。

> 💬 **问：GRPO 是不是 REINFORCE 套壳？**
>
> 🎯 答：数学骨架是：REINFORCE + 组基线 + PPO clip + KL 锚定。套壳与否看怎么定义——它的新意不在公式而在**工程重排**：杀掉 value model 这个 LLM 上最难训的组件，换来 PPO 级的稳定性和 REINFORCE 级的简单。DeepSeekMath 消融里 PPO-clip 对比 REINFORCE-clip 提升有限，说明 clip 是保险（multi-epoch off-policy 时）而非核心增益。面试里承认"组件经典、组合精准"反而显得懂行。

> 💬 **问：为什么 R1-Zero 里 KL 不能省？**
>
> 🎯 答：纯规则奖励会把策略拉离 base 模型，表现为语言混杂（中英漂移）、格式崩坏、可读性归零——模型为了拿 reward 把整个输出分布都扭了。KL 锚定 π_ref 保证"说人话"的底质不被牺牲。这和 DPO 的 degeneration 同源：**只优化目标函数不给正则锚点，模型必然走捷径**。β 的权衡和 DPO 完全一样：小 → 学得快但漂，大 → 稳但学不动。

> 💬 **问：GRPO 解决了 token 级信用分配吗？**
>
> 🎯 答：没有，这是它最大的遗留问题。整局终局 reward 通过同一个 A_i 广播到该回答的所有 token——回答里哪个 token 该为成功负责，一无所知。缓解：PRM（过程奖励模型，逐步打分）、多步 agent 任务里用 per-step reward、或把 GRPO 变体到 loop 级/agent 级。Day 113 讲 veRL 的 agentic rollout 时会看到工程侧怎么补。

> 💬 **问：可验证奖励为什么重要？reward 设计有什么原则？**
>
> 🎯 答：可验证 = 无需人工、无需 RM、零标注成本、零 RM 被 hack 的面；且对错分明，组内均值基线最锋利。原则四条：**可判**（答案能机械校验）、**抗 hack**（规则被钻空子的成本 > 收益，多规则交叉）、**密度适中**（太稀疏学不动，太密会被 shaping 带偏）、**可扩展**（新增任务类型只需加校验器）。代码任务用单测、数学用 boxed 比对、agent 用终局状态，都是同一哲学。

> 💬 **问：GRPO 和 DPO 的关系？实际配方怎么用？**
>
> 🎯 答：分工而非替代。DPO：offline、监督、便宜，管数据分布内的偏好对齐（风格、无害、表达）；GRPO：online、采样、贵，管需要探索的可验证能力（推理、代码、agent）。开源标准配方：**SFT 打底 → DPO 对齐偏好 → GRPO 冲推理天花板**（也可迭代：GRPO 采的数据回灌 DPO）。面试被问"怎么对齐你们的大模型"，把这条三段式报出来已经赢过 80% 候选人。

> 💬 **问：多轮 agent 场景怎么用 GRPO？终局奖励很稀疏。**
>
> 🎯 答：三条路：① **轨迹级 GRPO**：整个 rollout 当一个动作序列，终局 reward 广播（最朴素，信用分配最差）；② **步级奖励塑形**：工具调用成功、中间状态合法都给小 reward，把稀疏变稠密（风险：shaping 偏置）；③ **agentic 变体**：loop 级优势——同 prompt 下 G 条 agent 轨迹按成功率分组对比，配合 PRM。工程上需要多轮 rollout 基础设施（veRL 的 agent loop、沙箱执行），这正是 Day 113 的主角。

> 💬 **问：训练时 rollout 慢成狗，怎么办？**
>
> 🎯 答：rollout 是 GRPO 的第一瓶颈（G 倍采样 + 自回归生成）。三板斧：① **分离式推理引擎**——vLLM/SGLang 独立部署，continuous batching 吞吐拉满，和训练进程权重同步（NCCL 直传/参数服务器）；② **partial rollout 流水线**——先完成的序列先回传训练，不等全组（牺牲一点组内一致性换吞吐，工程上常分组对齐）；③ **长度控制**——max_tokens 截断、长回答降采样，治长尾。核心指标：rollout 生成时间与 GPU 训练时间的占比，优化到接近 1:1 才算健康。

### 2.10 Day 112 自检清单

- [ ] 徒手写出 GRPO 完整目标函数：组采样、组内归一化优势、PPO clip、KL 项，一个符号都不漏
- [ ] 说清组均值基线的三个性质：免训练、难度自校准、含自身的 1/G 偏差
- [ ] 写出 k3 KL 估计器 `e^δ − δ − 1`，证明非负 + 采样下无偏（E_p[q/p]=1）
- [ ] 解释 R1 去掉 std 归一化的理由（二值 reward 下引入难度相关缩放）
- [ ] 报出 GRPO vs PPO vs DPO vs REINFORCE 四方对比表的核心行
- [ ] 背出 R1-Zero 的数字：AIME 15.6% → 71.0%，@64 86.7%，对标 o1
- [ ] 说出 R1 完整配方：冷启动 SFT → GRPO → 拒绝采样造数据 → SFT → 再 GRPO
- [ ] 说出 token 均值化的长度偏置问题（Dr.GRPO），以及至少一种修复
- [ ] 答出"GRPO 为什么是 REINFORCE 套壳又不是"的标准话术
- [ ] 背出"GRPO 落地三板斧"：分离式推理引擎、partial rollout、长度控制

---

## 📚 今日参考

- Shao et al. (2024), *DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models*（arXiv:2402.03300）—— GRPO 原始论文
- DeepSeek-AI (2025), *DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning*（arXiv:2501.12948）—— R1-Zero 与 R1 配方
- Schulman (2020), *Approximating KL Divergence*（个人博客）—— k1/k2/k3 估计器
- Ahmadian et al. (2024), *Back to Basics: REINFORCE with Baseline*（RLOO，arXiv:2402.14740）—— leave-one-out 基线修正
- Liu et al. (2025), *Understanding R1-Zero-Like Training: A Critical Perspective*（Dr. GRPO，arXiv:2503.20783）—— std 归一化与 token 均值化批判
- Hu et al. (2025), *REINFORCE++: An Efficient RLHF Algorithm with Global and Local Contexts*（arXiv:2501.03262）
- TinyZero（Jiayi Pan）、Open-R1（HuggingFace）—— 社区复现
- TRL `GRPOTrainer` 文档与源码 —— 工程实现细节
- LeetCode 384. Shuffle an Array；Knuth, TAOCP Vol. 2 §3.4.2 —— Fisher-Yates

---

> 🧠 **今天的一句话**：PPO 花大价钱请了个价值模型当裁判，GRPO 说不用——让同一道题的 G 个考生互相当裁判，平均分就是基线。好的简化不是偷工减料，是发现哪些复杂性本来就不必存在。洗牌如此，训练亦如此。
