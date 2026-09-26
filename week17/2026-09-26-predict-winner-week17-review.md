# Day 114 — 预测赢家 + Week 17 强化学习与 RL 训练工程综合复习 🏆

> 📅 2026-09-26 · Week 17 Day 7（收官日）· 连续更新第 114 天
>
> 主题：强化学习与 RL 训练工程 🧠 · 综合复习 + 高频连环问通关

---

## 1) 今日算法题

### 预测赢家（Predict the Winner）

**题意**：给你一个整数数组 `nums`。玩家 1 和玩家 2 轮流从数组**两端**取数（玩家 1 先手），两人都**绝对理性**（目标是让自己的总分最大化、让对手最小化）。双方都采取最优策略时，判断玩家 1 是否能获胜（总分 ≥ 玩家 2 即算赢）。

```
输入: nums = [1, 5, 2]
输出: false
解释: 玩家1 只能拿 1 或 2。拿 1 → 玩家2 拿 5；拿 2 → 玩家2 拿 5。玩家1 必输。

输入: nums = [1, 5, 233, 7]
输出: true
解释: 玩家1 拿 1 → 玩家2 只能拿 5 或 7 → 玩家1 拿 233 稳赢。
```

**关键约束**：`1 <= n <= 20`，`-10^7 <= nums[i] <= 10^7`。n 很小——这是**博弈论 DP / Minimax** 的标志性信号。

### 思路：Minimax = 零和博弈的 DP

把游戏抽象成状态 `(i, j)` = 当前还剩 `nums[i..j]`，轮到某玩家行动。定义：

```
dp[i][j] = 当前行动的玩家，在 nums[i..j] 上能获得的最大净胜分
         （自己的得分 − 对手的得分）
```

**转移**：当前玩家有两个选择——拿左边或拿右边。拿完之后就轮到对手在更小的区间上行动，而对手也会最优发挥。零和博弈的精髓：**对手的净胜分 = −自己的净胜分**，所以——

```
拿左: nums[i] − dp[i+1][j]    （自己拿了 nums[i]，对手在剩余区间净胜 dp[i+1][j]）
拿右: nums[j] − dp[i][j-1]
dp[i][j] = max(拿左, 拿右)      // 当前玩家取最优
```

**边界**：`dp[i][i] = nums[i]`（只剩一个数，全归自己）。

**答案**：`dp[0][n-1] >= 0` 即玩家 1 获胜（净胜分非负）。

这个结构为什么是对的？**Minimax 定理**：零和完全信息博弈中，max 玩家选择 max、min 玩家选择 min，递归展开后等价于 negamax 形式——因为收益矩阵反对称（我赢的就是你输的），min  player's max = −(max player's min)，所以 `dp` 只需要一个值，加减号天然完成攻守互换。这就是「预测赢家」和 AlphaGo/AlphaZero 的自博弈（self-play）共享的数学骨架：**状态 + 当前行动方视角的值 + 对手最优响应 = 递归博弈搜索**。

> 💡 **n ≤ 20 的信号**：区间 DP 状态数 O(n²)，转移 O(1)，总复杂度 O(n²)，对 n=20 来说洒洒水。但如果 n 到 10⁵，就要换数学技巧了（见连环问）。

### 代码

**Go（自底向上区间 DP）：**

```go
func predictTheWinner(nums []int) bool {
    n := len(nums)
    dp := make([][]int, n)
    for i := range dp {
        dp[i] = make([]int, n)
        dp[i][i] = nums[i] // 边界：只剩一个数
    }
    // len 从 2 到 n：保证 dp[i+1][j]、dp[i][j-1] 已经算好
    for length := 2; length <= n; length++ {
        for i := 0; i+length <= n; i++ {
            j := i + length - 1
            takeLeft := nums[i] - dp[i+1][j]
            takeRight := nums[j] - dp[i][j-1]
            if takeLeft > takeRight {
                dp[i][j] = takeLeft
            } else {
                dp[i][j] = takeRight
            }
        }
    }
    return dp[0][n-1] >= 0
}
```

**Python（记忆化递归，更像 minimax 的本体）：**

```python
from functools import lru_cache

def predictTheWinner(nums) -> bool:
    @lru_cache(maxsize=None)
    def dp(i, j):
        """当前玩家在 nums[i..j] 上的最大净胜分"""
        if i == j:
            return nums[i]
        take_left  = nums[i] - dp(i + 1, j)
        take_right = nums[j] - dp(i, j - 1)
        return max(take_left, take_right)

    return dp(0, len(nums) - 1) >= 0
```

**空间优化版（O(n)）**：区间 DP 只看相邻两层，`dp[j]` 滚动更新——`dp[i][j]` 依赖 `dp[i+1][j]`（同列下一行）和 `dp[i][j-1]`（同行前一列），按 i **倒序**、j **正序**遍历即可用一维数组复用。面试能说出思路即可，不要求现场推。

```go
// 一维版核心循环（面试加分项）
dp := make([]int, n)
copy(dp, nums) // dp[j] 初始 = nums[j]，相当于 dp[i][i]
for length := 2; length <= n; length++ {
    for i, j := 0, length-1; j < n; i, j = i+1, j+1 {
        dp[j] = max(nums[i]-dp[j], nums[j]-dp[j-1])
        //       ↑拿左：对手在 [i+1..j] 的净胜分 = 上一行的 dp[j]
        //              ↑拿右：对手在 [i..j-1] 的净胜分 = 本行前面的 dp[j-1]
    }
}
```

> ⚠️ **边界陷阱**：① 区间 DP 的遍历顺序——外层 `length` 从小到大，保证小区间先于大区间算好；② 记忆化版的 `lru_cache` 一定加，裸递归是 O(2ⁿ)；③ 返回值是 `>= 0` 不是 `> 0`（平局算赢）；④ 一维优化时 i 必须倒序遍历，正序会用到本层还没更新的脏值。

### 复杂度

| 维度 | 复杂度 | 说明 |
|---|---|---|
| 时间 | `O(n²)` | 状态 O(n²) × 转移 O(1) |
| 空间 | `O(n²)` → `O(n)` | 记忆化 O(n²)，滚动数组可压到 O(n) |
| 递归版 | `O(2ⁿ)` 无记忆化 | 裸 minimax 是指数爆炸，加缓存才 DP |

### 面试官连环问

> 💬 **问：如果数组长度到 10⁵ 怎么办？O(n²) 肯定过不了。**
>
> 🎯 答：分两步走——先观察小规模 n ≤ 20 找规律（偶数长度的数组先手必胜是个著名结论：**只要偶数长度，先手必能拿到奇数位全满或偶数位全满**中较大的那一组，因为先手控制配对）。所以问题退化为 `max(奇数位和, 偶数位和) >= min(总和 − 该组, ...)`，O(n) 直接判断。这是「博弈论 DP → 数学观察」的经典路径：先暴力解小数据，再找不变量/配对论证推通解。

> 💬 **问：Minimax 和 Alpha-Beta 剪枝是什么关系？**
>
> 🎯 答：Minimax 是完备博弈树搜索，Alpha-Beta 是它的剪枝优化——利用「对手已经有一个更差的选择」提前放弃当前分支。理论上剪枝后访问节点数降到 O(b^(d/2))（b=分支因子，d=深度），效果等同把搜索深度翻倍。AlphaGo 的 MCTS 则更进一步：不展开全部节点，用策略网络指导采样、价值网络评估叶节点，把「算得完」变成「采得准」。

> 💬 **问：这和强化学习有什么关系？为什么要放今天讲？**
>
> 🎯 答：三层关系——① **状态/动作/奖励完全同构**：状态 = 剩余区间，动作 = 拿左/拿右，奖励 = 拿到手的数。预测赢家就是「在完美信息博弈上做 DP 规划」；② **值函数就是 dp 表**：`dp[i][j]` 就是价值 V(s)，递归贝尔曼方程 `V(s) = max_a [r + V(s')]`——和 Day 108 的 Bellman 最优方程一模一样，只是转移概率 = 1（对手也最优，不是随机）；③ **自博弈**：当对手也是学习中的策略（而非全知 oracle），minimax 的 min 端变成采样对手策略，这就是 self-play RL（AlphaZero 的套路：policy + value 双头网络，self-play 生成数据，MCTS 改进策略）。本周讲的所有 RL 算法，扔到零和博弈环境里就是这套。

> 💬 **问：如果玩家 2 不理性（随机取数）怎么改 dp？**
>
> 🎯 答：min 端从「对手取 min」变成「对手动作的概率期望」——`dp[i][j] = max(拿左净胜, 拿右净胜)`，其中对手的净胜分从「确定值」换成「期望净胜分」。这正好回到 Day 108 的 **MDP 期望贝尔曼方程**（对手策略作为环境转移概率），一天的内容闭环了。

> 💬 **问：记忆化递归和递推怎么选？**
>
> 🎯 答：博弈类问题状态转移**不对称**（拿左依赖 `[i+1][j]`、拿右依赖 `[i][j-1]`，两个方向都有），自底向上递推的循环边界容易写错；记忆化递归只关心「需要哪个算哪个」，代码最短最不易错。面试**先写记忆化版保证正确**，被追问空间优化再展示递推/滚动数组。工程上递推版常数更小（无递归开销），但 n ≤ 20 的博弈题两者都是零头。

---

## 2) 面试技巧 — Week 17 强化学习与 RL 训练工程综合复习

七天内容一锅端：一张表 + 两条主线 + 连环问通关 + 收官加菜（自博弈）。

### 📊 六天内容一张总表

| Day | 主题 | 一句话核心 | 必须记住的 |
|---|---|---|---|
| **Day 108** | MDP / Bellman / Q-Learning | 强化学习 = 延迟奖励下的序贯决策 | 期望 vs 最优方程、策略迭代 vs 值迭代、SARSA vs Q-Learning、DQN 两大 trick（经验回放 + 目标网络） |
| **Day 109** | 策略梯度 REINFORCE / Actor-Critic | 直接对策略求梯度，baseline 减方差无偏 | ∇J 定理、log-derivative trick、最优 baseline=V(s)、TD 换 MC 的偏差-方差权衡、熵正则防坍缩 |
| **Day 110** | PPO | clip 软信赖域，KL 约束换一阶 SGD | L^CLIP 逐符号、重要性采样、GAE λ、RLHF 映射（RM + KL 防 reward hacking） |
| **Day 111** | DPO / RLHF | 闭式解消掉 RL，监督学习直接学偏好 | 三步推导（π*→反解 r→消 Z(x)）、隐式奖励 β·log π/π_ref、length bias / degeneration、PPO vs DPO 天花板 |
| **Day 112** | GRPO | 删 Critic，组内均值当基线，可验证奖励上天堂 | A_i=(r_i−mean)/std、R1 去 std、k3 KL 估计器、R1-Zero（base 纯 RL）、Dr.GRPO 长度偏置、G=8~64 |
| **Day 113** | veRL / Rollout 基础设施 | 单控制器编排，colocate 编排显存，rollout 占 70~90% 时间 | HybridFlow 架构、GRPO step 七步、sleep/wake、NCCL 权重同步、吞吐四板斧、agentic 三大痛点 |

### 🗺️ 两条主线串起全周

**主线一：算法演进 —— 从「值」到「策略」到「组」**

```
值方法（Q-Learning/DQN）          策略方法（REINFORCE）              信赖域（PPO）
 学 V/Q，贪心出策略          →      直接 ∇log π·A，加 baseline    →    clip 软约束 + GAE
 死于：连续动作/POMDP/          死于：方差爆炸 + 步长玄学           死于：四模型在线工程地狱
 最大动作枚举                                                    ↓
                                                          DPO（闭式解消 RL）
                                                          死于：offline 天花板 + length bias
                                                                ↓
                                                          GRPO（组基线 + 可验证奖励）
                                                          优：两模型在线、难度自校准、
                                                             prefix caching 隐性红利
```

一条演进逻辑：**每个算法都在解决上一个的死穴，同时引入自己的新坑**。面试被问"PPO 和 GRPO 区别"，先把这条链背出来再填细节。

**主线二：工程落地 —— 算法公式一页纸，系统问题一箩筐**

GRPO 公式三行能写完，但把它跑起来要回答：rollout 占 step 时间 70~90% 怎么办（四板斧）、训练和生成抢显存怎么办（colocate sleep/wake）、权重怎么同步（NCCL broadcast + per-shard load）、GPU 不够怎么办（分离式部署）、多轮 agent 轨迹怎么采（异步 + 沙箱 + 失败轨迹不能丢）。**算法工程师问"怎么收敛"，系统工程师问"step 时间花在哪"**——Week 17 就是把你从前者训练成后者。

### 🎤 高频连环问通关速答

**Q1：一句话区分 SARSA 和 Q-Learning？**
> 都是 TD，SARSA 用**实际执行的下一个动作**更新（on-policy），Q-Learning 用**下一个状态的最大 Q** 更新（off-policy）。一个学「我这条路的真实期望」，一个学「这条路尽头的理论最优」。

**Q2：为什么策略梯度要减 baseline？不减会怎样？**
> G_t 是随机变量之和，方差大到训练不动；减 b(s) 后期望不变（E[∇log π·b]=b·∇Σπ=0，一行手推），方差断崖下降。最优 baseline 是 V(s)——这就是 Actor-Critic 里 Critic 存在的意义。

**Q3：PPO 的 clip 到底在干什么？ε=0.2 是什么意思？**
> 重要性采样让旧数据可复用，但新旧策略偏离太远时比值 r_t 爆炸、方差失控。clip 把 r_t 钳在 [1−ε, 1+ε]，A>0 时奖励推不动就梯度归零（停止贪婪），A<0 时惩罚压不下去也归零。ε=0.2 是经验甜点——悲观界 min 保证 loss 是真实目标的**下界**，梯度不会虚高。

**Q4：DPO 的三步推导复述一遍？**
> ① KL 约束的 RL 目标有闭式解 π* = π_ref·e^{r/β}/Z(x)；② 反解出 r = β·log(π*/π_ref) + β·logZ(x)；③ 代入 Bradley-Terry 模型，Z(x) 只依赖 prompt 相减消掉 → 隐式奖励 β·log(π_θ/π_ref)，模型自己就是 RM。结论：**DPO 是在闭式解空间里做监督学习**，不需要采样、不需要 RM 在线推理。

**Q5：GRPO 为什么删 Critic？组基线怎么算？**
> PPO 的 Critic 难训（同 backbone 要另起炉灶）且 four-model 在线显存爆炸。GRPO 用同 prompt 采的 G 条样本的**组内均值**当基线：A_i = (r_i − mean) / std。全对全错时梯度自动归零（自动跳题），β·KL(π_θ‖π_ref) 防漂移。R1 版本去掉 std（难度相关缩放污染梯度）。

**Q6：k3 KL 估计器为什么是 e^δ − δ − 1？**
> δ = log π_ref − log π_θ。k3 = e^δ − δ − 1 三个性质：① 非负（e^x ≥ x+1）；② 采样无偏（E_p[e^δ]=1 一行证）；③ 梯度友好。比 naive 的 δ² 在训练初期（分布差大）数值稳定得多。

**Q7：R1-Zero 为什么重要？它证明了什么？**
> base 模型 + 纯 GRPO（不加 SFT 冷启动）就能让推理能力起飞：AIME 15.6%→71.0%（@64 86.7%）。证明两点：① **RL 可以唤醒 base 预训练沉睡的能力**，不是凭空造新能力（aha moment 自发出现）；② 可验证奖励（数学 boxed、代码单测）是推理 RL 的引擎。也留下了 R1 配方：冷启动 SFT → GRPO（加语言一致奖励）→ 拒绝采样 80 万条 → 全量 SFT → 二轮 GRPO。

**Q8：veRL 的 colocate 模式在干什么？**
> 训练和 rollout 共用同一批 GPU。矛盾三角：训练要 activations+梯度+优化器状态，vLLM 要 KV cache+激活，加起来爆显存。解法：训练阶段 vLLM `sleep` 释放 KV cache → 训练 → `wake_up` 重新预分配 + NCCL broadcast 同步新权重。中小模型 / 卡紧张选 colocate，吞吐优先 / 大模型选分离式，判断指标是 rollout:train ≈ 1:1。

**Q9：agentic RL 的 rollout 和文本 RL 差在哪？**
> 三大新痛点：① **长度爆炸**——上下文随轮数线性涨，要截断策略 + max_steps + 长度惩罚；② **环境异构**——搜索 API 延迟、沙箱冷启动，时间方差爆炸，要异步 rollout + 动态 batching + 超时熔断；③ **信用分配**——终局 reward 稀疏，要 per-step shaping / PRM / turn-level advantage。外加一条铁律：**失败轨迹给惩罚 reward 不能丢**，丢了就 bias 数据分布（难任务被过滤，模型越训越软）。

**Q10：面试官说"手写一个最小 GRPO 训练循环"？**
> 20 行 driver 伪代码：`prompts = sample(B) → rollout_workers.generate(G 条) → rewards = score(规则/RM) → adv = group_norm(rewards) → actor.update(clip_loss + β·KL) → broadcast 权重`。说完补一句："这段代码里最慢的是第 2 行，所以真正的工程全在 rollout 引擎和调度上"——面试收尾的金句。

### 🏆 收官加菜：自博弈（Self-Play）—— 从预测赢家到 AlphaZero

算法题是完美信息博弈的 minimax，真实世界的对手不是全知的，而是**也在学习的**。这就是 self-play，当前 agentic RL 最热的方向之一：

- **为什么有效**：零和博弈里，对手就是你的环境。对手变强 = 环境变难，训练信号永远不会枯竭（对比模仿学习：数据用完就没了）。
- **AlphaZero 三件套**：**policy + value 双头网络**（一个输出动作分布，一个输出局面胜率）、**self-play 生成对局**（MCTS 改进策略后落子）、**迭代训练**（新网络打旧网络，胜率达 55% 就替换）。MCTS 的 UCB 选择兼顾探索（Q 值低但访问少的节点）和利用（Q 值高的节点）。
- **与本周内容的连接**：policy 网络 = REINFORCE/Actor-Critic 的策略端；value 网络 = baseline/Critic（Day 109 的最优 baseline=V(s) 的字面落地）；MCTS 的 backup = Bellman 期望方程的回传（Day 108）；self-play 数据分布漂移 = 需要 importance ratio / clip（Day 110）；最新的工作甚至把 **GRPO 直接搬进 self-play**（双角色组基线：同一 prompt 让 policy 和 opponent 各生成，组内对比算 advantage）。
- **Agent 时代的 self-play**：debate（两个 agent 辩论，judge 打分）、code-attack-defense（一个写漏洞一个找漏洞）、social simulation——**对手池（population）对抗防坍缩**是关键工程（只打最新自己会被针对性 exploit，OpenAI 的 FTW、DeepMind 的 League Training 都是先例）。

> 🎯 面试加一句「minimax 是对手全知的 DP，self-play 是对手也在学的 RL」，Week 17 的高度直接拉满。

### 🧭 收尾话术：如果被问"讲讲你对 RLHF / 推理 RL 的理解"

```
「RLHF 主线是 SFT → RM → PPO 三段，工程痛点在 PPO 的四模型在线；
 DPO 用闭式解消掉采样和 RM，但 offline 天花板 + length bias 是原罪。
 推理 RL 的答案是 GRPO：删 Critic、组内均值基线、可验证奖励，
 R1-Zero 证明纯 RL 能唤醒 base 的沉睡能力。
 落地层面 rollout 占 70~90% 时间，veRL 用单控制器 + colocate 编排
 + vLLM 四板斧解决。Agent 时代的新战场是 self-play：
 minimax 是对手全知的 DP，self-play 是对手也在学的 RL。」
```

> 一段话覆盖全周 7 天内容，从算法到系统到前沿，面试官点头的那种。

---

## ✅ Day 114 自检清单

- [ ] Minimax/Negamax 转移方程 3 分钟默写：`dp[i][j] = max(nums[i]−dp[i+1][j], nums[j]−dp[i][j−1])`
- [ ] 解释为什么零和博弈只需要一个 dp 值（min 端 = −max 端）
- [ ] 背出 Q1-Q10 连环问速答模板（每题 ≤ 30 秒）
- [ ] 画出算法演进主线：Q-Learning → REINFORCE → PPO → DPO → GRPO，每步的死穴与新坑
- [ ] 默写 GRPO step 数据流七步，指出瓶颈行
- [ ] 说出自博弈三件套（policy+value 双头、self-play 数据、胜率替换）与本周各 Day 的连接
- [ ] 复述「RLHF / 推理 RL 理解」收尾话术

**Week 17 完结撒花 🎉 —— 强化学习与 RL 训练工程，从 Bellman 方程到 veRL 流水线，一路打穿！**
