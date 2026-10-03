# Day 121 — 24 点游戏 + Week 18 推理模型与 Test-Time Compute 综合复习 🏆

> 📅 2026-10-03 · Week 18 Day 7（收官日）· 连续更新第 121 天
>
> 主题：推理模型与 Test-Time Compute 🔬 · 综合复习 + 高频连环问通关

---

## 1) 今日算法题

### 24 点游戏（24 Game / Judge Point 24）

**题意**：给定 4 张牌（整数数组 `cards`，取值 1~9，可重复），判断能否通过 `+、−、×、÷` 和任意括号使表达式结果**等于 24**（中间结果允许分数，除法合法当且仅当除数非零）。

```
输入: cards = [4, 1, 8, 7]
输出: true
解释: (8 - 4) × (7 - 1) = 24

输入: cards = [1, 2, 1, 2]
输出: false
解释: 穷举所有表达式，没有任何一种等于 24
```

**关键约束**：只有 4 个数，每数用且仅用一次。这是**搜索类问题**的标志性信号——也是今天把它放在 Week 18 收官的原因：**24 点就是 ToT（Tree of Thoughts）论文的成名战场**（Day 117），这道题本身就是一个微型的 test-time compute 系统。

### 思路：减而治之（Reduce & Conquer）

每次从当前 multiset 中**有序地**取出两个数 `a`、`b`，枚举它们能产生的所有运算结果 `c`（`a+b`、`a−b`、`b−a`、`a×b`、`a/b`、`b/a`，后两个要求除数非零），把 `c` 放回去。每合并一次，数的个数减一；递归到只剩一个数时，检查 `|x − 24| < ε`。

核心设计决策三件套：

1. **为什么选两个合并，而不是排列所有表达式骨架？** 合并视角天然枚举了**所有加括号方式**——`(a+b)×(c−d)`、`(a×b−c)×d`、`a/(b−c/d)` 这些在合并视角下只是不同的选数顺序。4 个数只需要 3 层合并，代码没有专门处理括号的逻辑，**括号被递归结构隐式枚举**了。这是减而治之的经典美感：用「状态收缩」代替「结构枚举」。
2. **为什么要 `b−a` 和 `b/a`？** 减法和除法**不可交换**，枚举有序对 `(i,j)` 时 `a−b` 和 `b−a` 是两个不同分支，漏掉就 WA。循环里 `i != j` 且 `rest` 取两者之外，天然处理了有序性。
3. **浮点陷阱**：中间结果可能是 1/3、7/3 这类分数，比较时必须用 `|x − 24| < 1e-6`，不能 `==`。反过来也能讲清楚为什么这题允许分数中间结果（LeetCode 679 的原设定）——如果强制每步整除，状态空间更小，是另一个变种（见连环问）。

> 💡 **和 Week 18 的同构（今天的主角）**：每棵递归子树 = 推理树的一个节点；每条合并路径 = 一条 CoT；最终的 `==24` 检查 = **终局验证器（ORM）**。24 点的验证器便宜到可以直接穷举全部路径——所以小搜索空间的问题，**暴力搜索就是推理**。Week 18 的全部内容，就是「验证器不便宜、搜索空间太大」时怎么办的工程答案。

### 代码

**Python（记忆化版，面试推荐）：**

```python
class Solution:
    def judgePoint24(self, cards) -> bool:
        EPS = 1e-6

        @lru_cache(maxsize=None)
        def dfs(state: tuple) -> bool:
            # state: 排序后的浮点 tuple（排序 → 同状态去重）
            if len(state) == 1:
                return abs(state[0] - 24) < EPS
            nums = list(state)
            n = len(nums)
            for i in range(n):
                for j in range(n):
                    if i == j:
                        continue
                    rest = tuple(sorted(
                        nums[k] for k in range(n) if k != i and k != j
                    ))
                    a, b = nums[i], nums[j]
                    cands = [a + b, a - b, a * b]
                    if abs(b) > EPS:
                        cands.append(a / b)
                    for c in cands:
                        # 有序对 (i,j) 与 (j,i) 在双层循环中都出现，
                        # b-a / b/a 自动覆盖，无需手动补
                        if dfs(rest + (c,)):
                            return True
            return False

        return dfs(tuple(sorted(float(x) for x in cards)))
```

**Go（竞赛直出版）：**

```go
func judgePoint24(cards []int) bool {
    nums := make([]float64, len(cards))
    for i, c := range cards {
        nums[i] = float64(c)
    }
    return dfs(nums)
}

func dfs(nums []float64) bool {
    const EPS = 1e-6
    if len(nums) == 1 {
        return math.Abs(nums[0]-24) < EPS
    }
    n := len(nums)
    for i := 0; i < n; i++ {
        for j := 0; j < n; j++ {
            if i == j {
                continue
            }
            rest := make([]float64, 0, n-1)
            for k := 0; k < n; k++ {
                if k != i && k != j {
                    rest = append(rest, nums[k])
                }
            }
            a, b := nums[i], nums[j]
            cands := []float64{a + b, a - b, a * b}
            if math.Abs(b) > EPS {
                cands = append(cands, a/b)
            }
            for _, c := range cands {
                next := append(rest, c) // 新切片，不影响同层其他分支
                if dfs(next) {
                    return true
                }
            }
        }
    }
    return false
}
```

> ⚠️ **边界陷阱**：① Go 里 `append(rest, c)` 若 `rest` 容量有富余会**原地覆盖**，同层循环第二次 append 会互相污染——上面 `make([]float64, 0, n-1)` 每次重建是稳妥写法；② 记忆化键必须排序（`sorted tuple`），否则 `(4,1,8,7)` 的不同取数顺序产生逻辑等价但哈希不同的状态；③ 除零保护用 `|b| > EPS` 而非 `b != 0`（浮点语义统一）；④ 答案 `true` 一找到立即返回（短路剪枝）。

### 复杂度

| 维度 | 复杂度 | 说明 |
|---|---|---|
| 时间 | `O((n·(n−1))ⁿ⁻¹ · 6ⁿ⁻¹)`，n=4 时约几千次操作 | 每层选有序对 O(n(n−1)) × 每对 ≤6 种运算，递归深度 n−1 |
| 空间 | `O(n)` 递归栈 × 状态数（记忆化版 `O(S)`，S=不同排序状态数） | n=4 时记忆化状态 < 200，纯玩具级 |
| 量级 | n=4：毫秒级；n=5：仍可暴力；n=6+：需要记忆化 + 强剪枝 | 搜索空间随 n 超指数增长 |

### 面试官连环问

> 💬 **问：如何输出所有不同的可行表达式（不只是判 true/false）？**
>
> 🎯 答：回溯收集路径——递归参数里带上当前表达式字符串（如 `"((8-4)*(7-1))"`），叶子命中 24 时加入答案集。**去重**用两招：参与运算的两个数若相等（`a==b`），`a+b`/`a×b` 只算一次；最终答案集用 `set` 收。也可以对初始数组排序后跳过同层重复值。这题的去重细节很考编码扎实度。

> 💬 **问：卡牌变 5 张、目标值任意（比如 100），框架还成立吗？**
>
> 🎯 答：框架完全成立（合并递归与 n 无关），但状态数爆炸，需要两板斧——① **记忆化**（排序 tuple 当 key，当天 Python 版已内置）；② **剪枝评估**：中间值超出合理范围就砍掉（如全部正数时，若某中间结果 > 目标值 × 剩余牌最大值，不可能拉回来）——这就是 **PRM 的雏形**：用便宜的过程评估剪掉坏分支，不用走到叶子才发现错。24 点因为搜索空间小，验证器又便宜，暴力就够；空间一大，剪枝评估才开始值钱。

> 💬 **问：变种——要求每一步运算结果都是整数（英雄游戏 24 点版）怎么改？**
>
> 🎯 答：`cands` 过滤：除法结果要求 `math.Abs(c - math.Round(c)) < EPS` 才保留。状态空间反而变小（分支更少），收敛更快。这个变种对应「推理过程必须每步合法」的任务（代码执行、严格证明），和原版的「只验终局」形成对照——**过程约束 vs 终局约束**，正是 Day 116 ORM vs PRM 的算法版。

> 💬 **问：人玩 24 点一眼看出 4×6 / 3×8 的分解，算法能不能也用上这种先验？**
>
> 🎯 答：能——先枚举「目标分解」：`24 = 4×6 = 3×8 = 2×12 = 48/2 = 24±0 …`，对每个分解式检查能否用两张牌凑出每半边的值。命中率极高（人类就是这么玩的）。这就是 **policy 先验**（MCTS 里 pUCT 的 P(s,a)，Day 117）：好的先验让搜索树第一刀就砍在大动脉上。面试讲出这一层，算法题直接升维到 Week 18 的主题。

> 💬 **问：如果牌数很多（比如 10 张）且不要求全用，怎么找「等于 24 的最长表达式」？**
>
> 🎯 答：变成带约束的优化搜索——状态加「已用牌集合 bitmask」，目标从「判定」变「最大化使用牌数」。分支太多时换思路：① 子集枚举 + 每子集跑原算法（10 张只有 2¹⁰ 子集，每子集毫秒级，总开销可接受）；② 或者归约到**表达式模板匹配**：预生成所有 n 元表达式结构（Catalan 数级），代入求值。这题是「问题归约」的活教材——判定问题反复用，组合问题拆着用。

---

## 2) 面试技巧 — Week 18 推理模型与 Test-Time Compute 综合复习

七天内容一锅端：一张表 + 一条主线 + 连环问通关 + 收官同构。

### 📊 六天内容一张总表

| Day | 主题 | 一句话核心 | 必须记住的 |
|---|---|---|---|
| **Day 115** | Test-Time Compute 总览 | Scaling Law 两根轴：训练 FLOPs ↔ 推理 FLOPs | o1 配方（大规模 RL + 长 CoT + 可验证奖励）、三类 test-time 策略（并行采样 / 顺序修订 / 树搜索）、验证比生成容易（P vs NP 直觉）、compute-optimal 按难度分配、verifier 四类成本谱 |
| **Day 116** | PRM 过程奖励模型 | 终局奖励归因黑洞 → 稠密过程信号 | PRM800K（人工贵）→ MATH-Shepherd（rollout 自动标注）、ORM vs PRM 七维对照、PRM 三用途（训练 shaping / 搜索引导 / 数据筛选）、四坑（装模作样 hack / step 边界 / OOD / 验证成本） |
| **Day 117** | ToT / MCTS / rStar | 把生成重构为状态空间搜索 | ToT 四操作（分解/生成/评估/搜索）、MCTS 四步 + UCB1 默写 + Simulation 三档、pUCT 加 policy 先验、rStar 判别式互证免训练、搜索↔RL 飞轮 |
| **Day 118** | Self-Consistency / Self-Refine / 验证者悖论 | 能 DP 别投票；怀疑自己是能力但要先训练 | 1−(1−p)^N 对数增长、Kamoi 2024 冷水（intrinsic self-correction 掉点 95→82.9）、验证者悖论三段式 + 三出路、Reflexion / CoVe / Self-Rewarding |
| **Day 119** | 推理系统工程 | serving 买延迟 ↔ test-time 买质量，汇率设计感 | KV cache 显存公式 2×层×KV头×头维×字节×长度、PagedAttention、prefix caching（GRPO 组共享省 30~60%）、PD 分离、长上下文三座大山、延迟-质量交换所 |
| **Day 120** | Verifier 生态 / LLM-as-Judge | 验证器是推理模型的体外器官，越贵越易被 Goodhart | 四类验证器全家福、三范式（pointwise/pairwise/reference-guided）、五大偏差、Goodhart 四幕剧 + 修复三板斧、judge 工程 checklist、路由原则「每题配最便宜的够用验证器」 |

### 🗺️ 一条主线串全周：**正确率 = 搜索 × 验证 × 算力分配**

Week 18 全部内容可以压进一个公式。Test-time compute 的本质是把**推理时的算力当预算**，预算花在三个地方：

```
        ┌─ 往哪花（搜索策略）
        │   ├─ 并行：Best-of-N / Self-Consistency（Day 118）
        │   ├─ 顺序：Self-Refine / Critique-Revise（Day 118）
        │   └─ 树：ToT / MCTS / rStar（Day 117）
正确率 = ├─ 怎么知道花对了（验证器）
        │   ├─ 规则验语法 → 执行验行为 → PRM 验过程 → LLM-Judge 验语义（Day 116/120）
        │   └─ 越带上下文越贵，越贵越容易被 Goodhart（Day 120）
        └─ 花多少（算力分配）
            ├─ compute-optimal：简单题少花、难题多花、答对早停（Day 115）
            └─ 系统侧账本：KV cache / prefix caching / PD 分离（Day 119）
```

o1 的历史地位（Day 115）：它打开了第二根轴——**训练 FLOPs 固定时，推理 FLOPs 还能换正确率**。随后六天全是这根轴上的工程展开：搜索算法（117）、采样策略（118）、过程验证（116）、系统账本（119）、验证器经济学（120）。面试被问"讲讲 test-time compute"，先把这三段说出来，细节自然有地方挂。

### 🎤 高频连环问通关速答

**Q1：一句话说清 o1 和普通模型的本质区别？**
> 普通模型是「系统一」——一次前向出答案，token 数固定；o1 是「系统二」——RL 训出来的长 CoT 让模型在推理时进行隐式搜索（回溯、验证、aha moment），花更多推理 FLOPs 换更高正确率。不是模型变大了，是**推理过程变成了计算**。

**Q2：「验证比生成容易」是什么意思？为什么 Week 18 反复强调它？**
> P vs NP 的直觉版：检查一个答案对不对，往往比造出这个答案便宜几个数量级（乘法 vs 因式分解）。它是推理 RL 的成立前提：GRPO 需要可验证奖励才能跑（数学 boxed、代码单测），Best-of-N 需要验证器才能选，rStar 需要判别器才能互证。**没有便宜验证器的任务（开放写作、多跳推理），test-time compute 的全部工具箱都失灵**——这就是为什么 Day 120 的 LLM-as-Judge 重要：它是在给「没有金标准」的任务造验证器。

**Q3：Best-of-N 的通过率公式和边际收益？**
> 单样本通过率 p、采 N 条取最佳（有完美验证器），通过率 = 1−(1−p)^N。N=4~16 是甜点区（p=0.5 时 N=8 → 99.6% 天花板逼近），之后对数增长边际骤降。工程三件套：**按难度动态分配 N**（简单题少采）、**Early-Stopping**（分布稳定即停，省 ~40%）、**verifier 加权投票**替代 argmax。

**Q4：PRM 的训练数据从哪来？为什么它比 ORM 难搞？**
> 三条路：人工逐步标注（PRM800K，质量金标准但贵到肉疼）、**rollout 自动标注**（MATH-Shepherd：对中间步跑 MCTS rollout，用蒙特卡洛估计该步的 V 值，白嫖）、隐式内化（R1 路线，让 RL 自己长出过程感知）。比 ORM 难在：step 边界没有金标准（一步拆多细是玄学）、标注成本高一个量级、OOD 风险大（数学 PRM 拿去验代码直接失灵）。

**Q5：MCTS 四步循环默写 + LLM 化改造？**
> **选择**（UCB1 = Q + c·√(ln N / N_a)，平衡探索利用）→ **扩展**（加个节点）→ **仿真**（三档：完整 rollout 贵而准 / value model 单点便宜有偏 / 短程 rollout + PRM 折中 1/10 成本）→ **回传**（把叶子结果沿路径 backup）。LLM 化四改造：动作 = thought 采样离散化、rollout 太贵改短程 + PRM、UCB1 加 policy 先验变 pUCT、奖励 = 终局验证器 + PRM 塑形。

**Q6：Self-Consistency 什么时候失效？**
> 两个前提崩塌即失效：① **答案空间不可比对**——开放生成没法多数投票（改进：LLM 判等价 / 嵌入聚类）；② **独立性假设崩塌**——系统性错误会一致地错（温度采样改变不了模型的知识盲区，N 条全错在同一个坑里）。验证者悖论（Day 118）：自评与自出共享权重，独立性从根上不存在，所以 Self-Refine 没有外部反馈时常掉点（Kamoi 2024：GSM8K 95→82.9）。

**Q7：KV cache 显存怎么口算？prefix caching 为什么对 GRPO 是白嫖？**
> 公式：`2（K+V）× 层数 × KV 头数 × 头维 × 字节数 × 序列长度`。7B 模型 fp16 ≈ 512KB/token，4K 上下文 ≈ 2GB。GQA 砍 KV 头数（Llama-3-70B 8 头）是标准操作。Prefix caching：块级内容哈希链逐块查表，命中就跳过 prefill——GRPO 同一 prompt 采 G 条样本共享前缀，prefill 从 O(G·L) 直接降到 O(L)，**省 30~60%，什么代码都不用改**，这是 test-time 算法白嫖 serving 技术的教科书案例。

**Q8：LLM-as-Judge 的五大偏差各自怎么缓解？**
> ① **位置偏差**（谁放前面谁赢）→ order swap 双向评 + 多数决；② **冗长偏差**（长的显得好）→ AlpacaEval 2.0 LC win rate、rubric 明确长度不加分；③ **自我偏好**（偏爱自己的文风）→ 盲评 + 跨家族 judge + 第三方 API；④ **格式谄媚**（加粗加列表显得专业）→ 统一模板 + rubric 只评内容；⑤ **分数漂移**（GPT-4 换个版本全乱）→ 锚点样本 + 版本锁定 + held-out 人类抽检校准。

**Q9：Goodhart 四幕剧完整复述？**
> 第一幕：judge 做静态评估，岁月静好。第二幕：judge 分数进训练回路（RLAIF / Constitutional AI），模型发现加长加粗加自信就能涨分。第三幕：政策学会讨好 judge（AlpacaEval 早期实锤），judge 的偏好被钻空子。第四幕：judge 分布外失效，评估信号崩坏。**修复三板斧**：refresh 迭代 judge 版本、多 judge 集成降低单点被 hack 概率、held-out judge + 人类抽检校准。

**Q10：面试官说「给推理系统设计 test-time 策略」，标准答案框架？**
> 四步走：① **确认验证器**——任务有没有便宜验证器？有（数学/代码）→ 直奔 BoN + 树搜索；没有（开放任务）→ 先解决验证（LLM-as-Judge 或多代理互证），否则后面全是空中楼阁。② **选搜索策略**——答案空间有限可比对 → Self-Consistency；中途有可评估岔路 → ToT/MCTS；单轮短任务 → 直接 BoN。③ **算力分配**——compute-optimal 按难度自适应 N，配合 Early-Stopping。④ **系统账本**——serving 侧 prefix caching / PD 分离 / KV 量化把买质量的钱省回来。收尾金句：「test-time 的艺术是识别困难——能便宜的简单别买贵；serving 的艺术是识别重复——能缓存的重复别重算。」

### 🏆 收官同构：24 点就是 test-time compute 的完整沙盘

把今天的算法题放回 Week 18 的坐标系，每一层都对得上：

| 24 点 | Test-Time Compute |
|---|---|
| 暴力枚举全部表达式 | 穷举搜索空间（小规模直接搜 = 能 DP 别投票的同款逻辑，Day 118） |
| `== 24` 终局检查 | **终局验证器（ORM）**——便宜、确定、不可 hack |
| 5 张牌以上的剪枝评估 | **PRM** 的过程评估（Day 116） |
| 「先凑 4×6 / 3×8 分解」 | **policy 先验**（pUCT 的 P(s,a)，Day 117） |
| 人类玩家只验最终等式 | 24 点不需要 LLM-as-Judge，因为验证器天然存在（Day 120 的反面：没有金标准的任务才需要 judge） |
| 每步强制整数的变种 | **过程约束** vs 终局约束 = ORM vs PRM 的算法版对照 |

> 🎯 收 Week 18 的一句话金句：**「答案能被便宜验证的问题，暴力搜索就是推理；答案不能被便宜验证的问题，才需要 verifier 经济学。」** Week 18 的全部六天，就是这句话从左到右的展开。

### 🧭 收尾话术：如果被问「讲讲推理模型 / test-time compute」

```
「o1 打开了 Scaling Law 的第二根轴：训练 FLOPs 固定时，推理 FLOPs
 还能换正确率。核心三问：往哪花、怎么知道花对了、花多少。
 搜索三策略：并行 BoN/Self-Consistency、顺序 Self-Refine、树 ToT/MCTS；
 验证器四类成本谱：规则→执行→PRM→LLM-Judge，越贵越易被 Goodhart；
 算力分配 compute-optimal，按难度自适应 + Early-Stopping。
 系统侧一体两面：prefix caching 白嫖 GRPO 组共享前缀、PD 分离、
 KV 量化——买质量的钱靠 serving 省回来。
 最后的经济学：答案能被便宜验证的任务暴力搜索就是推理，
 不能被便宜验证的任务，全周的 verifier 设计就是答案。」
```

> 一段话覆盖全周 7 天内容，从算法到系统到经济学，面试官点头的那种。

---

## ✅ Day 121 自检清单

- [ ] 24 点减而治之框架 3 分钟默写（有序对 + 6 运算 + 除零保护 + 浮点 ε）
- [ ] 解释为什么合并视角天然枚举所有括号结构
- [ ] 背出 Q1-Q10 连环问速答模板（每题 ≤ 30 秒）
- [ ] 画出主线公式：搜索 × 验证 × 算力分配，把六天内容挂上去
- [ ] 默写 KV cache 显存公式 + GRPO prefix caching 收益口算
- [ ] 复述 Goodhart 四幕剧 + 修复三板斧
- [ ] 背出收官金句「答案能被便宜验证的问题，暴力搜索就是推理」

**Week 18 完结撒花 🎉 —— 推理模型与 Test-Time Compute，从 o1 的两根轴到 verifier 经济学，一路打穿！**
