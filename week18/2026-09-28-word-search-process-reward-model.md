# Day 116 — 单词搜索 + 过程奖励模型（PRM）🔬

> 📅 2026-09-28 · Week 18 Day 2 · 连续更新第 116 天
>
> 主题：推理模型与 Test-Time Compute 🧩 · 算法题 = 回溯 + 前缀验证剪枝，面试技巧 = 过程奖励模型 PRM：ORM 看结果，PRM 看步骤
>
> 🧵 昨天开了 Week 18 的地图：test-time compute 三类策略（并行采样/顺序修订/树搜索）。今天的 PRM 是贯穿后五天的基础设施——没有 verifier，所有 test-time 策略都是盲人摸象；而 verifier 里含金量最高、也最难做的就是 PRM。

---

## 1) 今日算法题

### 单词搜索（Word Search）

**题意**：给定一个 `m × n` 的字符网格 `board` 和一个字符串 `word`，判断 `word` 是否存在于网格中。单词必须按照字母顺序，通过**相邻格子**（水平或垂直方向）内的字母构成，且**同一个格子内的字母不能被重复使用**。

```
输入: board = [["A","B","C","E"],
               ["S","F","C","S"],
               ["A","D","E","E"]], word = "ABCCED"
输出: true   // A→B→C→C→E→D 路径存在

输入: word = "ABCB"
输出: false  // 不能重复使用格子 B
```

**关键约束**：`1 <= m, n <= 6`（网格小），`1 <= word.length <= 15`。暴力可过，但面试考察点从来不是 AC，而是**你怎么剪枝**。

### 思路：回溯搜索 + 逐步验证 = PRM 的算法版原型

这道题和解数独同属「约束满足 + 回溯」家族，但它藏着一个和今日主题严丝合缝的隐喻：

> **朴素 DFS（= ORM 思维）**：一条路走到黑，匹配完整个 word 才知道对不对。失败信息只有"终点没匹配上"——哪里错的？不知道。
>
> **带回溯的逐步验证（= PRM 思维）**：每深入一格就检查"前缀还匹配吗"，不匹配立刻剪枝。失败信息是**精确的**——在第 k 步就知道这条路死了，省掉子树全部算力。

**回溯三件套**（必须形成肌肉记忆）：

1. **路径记录（visited）**：用原地标记（`board[i][j] = '#'`）或 `visited` 数组记录已走的格子，**进入时标记、退出时撤销**——这是回溯的呼吸。
2. **四个方向 DFS + 提前返回**：匹配到 word 末尾 → true；越界/已访问/字符不等 → false；任一方向递归成功即返回 true。
3. **前缀剪枝（Prefix Pruning）**：每一步先查"当前字符是否匹配"，不匹配直接返回。进一步：如果手里有**整个字典**（Follow-up 的 Word Search II），用 Trie 查"这个前缀还是不是任何单词的前缀"——不是则整个子树剪掉。这一步把 O(单词数 × 4^L) 打到接近 O(格子数 × 4^L)。

> 🧬 **和 PRM 的连接**：Trie 前缀检查就是**过程验证器（process verifier）**——它不看最终能不能拼成完整单词（outcome），而是对搜索路径上的**每一个中间状态**打分（这个前缀活着/死了）。一个好的 process verifier 让搜索树从"深埋的失败"变成"浅层剪枝"，这正是 OpenAI《Let's Verify Step by Step》的算法直觉。

### 代码

**Go（回溯 + 原地标记，面试标准答案）：**

```go
func exist(board [][]byte, word string) bool {
    m, n := len(board), len(board[0])
    dirs := [][2]int{{0, 1}, {0, -1}, {1, 0}, {-1, 0}}

    var dfs func(i, j, k int) bool
    dfs = func(i, j, k int) bool {
        if k == len(word) {
            return true // 整个 word 匹配完成
        }
        if i < 0 || i >= m || j < 0 || j >= n || board[i][j] != word[k] {
            return false // 越界 / 已访问('#') / 字符不匹配 —— 过程验证剪枝
        }
        tmp := board[i][j]
        board[i][j] = '#' // 标记已访问（进入）
        for _, d := range dirs {
            if dfs(i+d[0], j+d[1], k+1) {
                return true
            }
        }
        board[i][j] = tmp // 撤销标记（退出）——回溯的呼吸
        return false
    }

    // 优化：从首字符出现的位置起跑，少进一半的递归
    for i := 0; i < m; i++ {
        for j := 0; j < n; j++ {
            if board[i][j] == word[0] && dfs(i, j, 0) {
                return true
            }
        }
    }
    return false
}
```

**Follow-up：Word Search II（字典版）——Trie + 回溯剪枝**

```go
type TrieNode struct {
    children [26]*TrieNode
    word     string // 到叶节点时存入完整单词，省一次拼接
}

func findWords(board [][]byte, words []string) []string {
    // 1. 建 Trie
    root := &TrieNode{}
    for _, w := range words {
        node := root
        for _, ch := range w {
            if node.children[ch-'a'] == nil {
                node.children[ch-'a'] = &TrieNode{}
            }
            node = node.children[ch-'a']
        }
        node.word = w
    }

    m, n := len(board), len(board[0])
    var result []string
    var dfs func(node *TrieNode, i, j int)
    dfs = func(node *TrieNode, i, j int) {
        if i < 0 || i >= m || j < 0 || j >= n || board[i][j] == '#' {
            return
        }
        ch := board[i][j]
        next := node.children[ch-'a']
        if next == nil {
            return // 前缀不在 Trie 中 —— 整棵子树剪枝（过程验证）
        }
        if next.word != "" {
            result = append(result, next.word)
            next.word = "" // 去重：收集过就清空
        }
        board[i][j] = '#'
        dfs(next, i+1, j)
        dfs(next, i-1, j)
        dfs(next, i, j+1)
        dfs(next, i, j-1)
        board[i][j] = ch
    }
    for i := 0; i < m; i++ {
        for j := 0; j < n; j++ {
            dfs(root, i, j)
        }
    }
    return result
}
```

### 复杂度

| 版本 | 时间 | 空间 | 说明 |
|---|---|---|---|
| Word Search | **O(m·n·4^L)** | **O(L)** 递归栈 | L = word 长度；4^L 是四方向展开的树，前缀剪枝把常数压得很小 |
| Word Search II | **O(m·n·4^L)** | **O(字典总字符数)** | Trie 建图 O(Σ\|w\|)，但剪枝后实际远小于逐词 DFS；大量词共享前缀时优势爆炸 |

> 💡 **复杂度话术**：被问"为什么不是 O(m·n·L)"时，答：每个起点都可能展开成深度 L 的四叉树，但**剪枝让平均情况远优于最坏情况**——面试里说清"最坏上界"和"剪枝期望"的区别，是加分项。

### 🎯 高频连环问

**Q1：visited 为什么用原地标记而不是额外数组？**
空间 O(1) 额外开销，面试里提一句"如果需要保留 board 原貌就用 visited 数组或复制一份"展示工程意识。原地修改的坑：递归返回前**必须撤销**，漏撤销 = 幽灵 visited。

**Q2：能不能用动态规划做？**
不能。DP 要求子问题无后效性/重叠子问题——本题"同一个格子不能重复走"导致状态必须包含**已访问集合**，状态空间是 2^(m·n) 级别，DP 退化为记忆化 DFS，且 m,n ≤ 6 时 4^L 的回溯已经够快。**识别"路径唯一性约束"是回溯题的信号灯**，别硬套 DP。

**Q3：Word Search II 中，如果字典有 10⁵ 个词，Trie 还够用吗？**
够用，这正是 Trie 的战场。逐词 DFS 是 O(词数 × 4^L) = 10⁵ × 4¹⁵ 直接爆炸；Trie 版每个格子只访问常数次，且共享前缀只查一次。进阶：Trie 太大可以换**前缀哈希表**或 AC 自动机（多模式匹配，O(文本长度) 理论最优，但工程上 Trie+剪枝已足够）。

**Q4：board 很大（10⁴ × 10⁴）怎么办？**
单点匹配意义不大，工程上这是**多模式串匹配**场景：对 word 建 Aho-Corasick 自动机，对 board 做一次扫描匹配，O(m·n·√词长) 级别。说出"AC 自动机"四个字，这道题直接从 LeetCode 难度跳到竞赛难度。

**Q5：回溯和数独的回溯有什么本质区别？**
数独是**约束传播**驱动（填一个数影响一片，MRV 选最约束变量）；单词搜索是**路径搜索**驱动（沿着一条链走到底）。前者状态是全局约束的投影，后者状态是单条路径的历史——所以数独用 bitmap 管全局，单词搜索只管好自己的 visited。

---

## 2) 面试技巧 — 过程奖励模型（PRM）：ORM 看结果，PRM 看步骤 🔬

昨天给 test-time 策略分了类（采样/修订/搜索），但有个问题悬着：**不管是 Best-of-N 还是树搜索，谁来判断一个中间步骤好不好？** 今天的答案就是 PRM（Process Reward Model，过程奖励模型）——它是推理时代 verifier 家族的王牌，也是 Day 117 树搜索和 Day 120 verifier 生态的前置知识。

### 🧠 核心问题：终局奖励的"归因黑洞"

想象你让模型做一道 20 步的数学证明，最后答案错了。用 ORM（Outcome Reward Model，结果奖励模型）你只知道一件事：**0 分**。哪一步错了？不知道。20 步里 19 步是对的、1 步算错了符号，和从头错到尾，拿到的信号一模一样。

这就是 **credit assignment（信用分配）问题**——Day 112 讲 GRPO 时埋过伏笔：终局 reward 广播给整条轨迹的所有 token，"做对"的功劳和"做错"的锅平分。对短答案无所谓，对长 CoT 是灾难：**稀疏 + 延迟 + 不可归因**的三重惩罚。

PRM 的思路一句话：**别等终点，每一步都打分。**

### 📜 两篇必读论文，一条演进线

**1. OpenAI《Let's Verify Step by Step》（2023.05）——PRM 的开山之作**

- 核心发现：在 MATH 数据集上，**过程监督（process supervision）显著优于结果监督（outcome supervision）**——用 PRM 做 step-level 的 Best-of-N，同样算力下准确率明显更高。
- 配套数据集 **PRM800K**：80 万条人工标注，人类标师逐步检查模型解答，每一步标 👍/👎。**注意成本**：这是 PhD 级别的人工，贵到只有 OpenAI 玩得起。
- 金句级结论：**"验证比生成容易"（verification is easier than generation）**——step-level 验证让模型能纠正自己的错误，因为每一步的监督信号是稠密的。

**2. MATH-Shepherd（2023.12）——PRM 自动化的破壁人**

- PRM800K 的人工太贵，MATH-Shepherd 提出**用 MCTS rollout 自动构造过程标签**：对解答的第 k 步，从这一步出发采样大量续写 rollout，统计"从这一步走下去最终答对的比例"——比例高 = 这一步好。
- 妙处：**不需要人工标**，只需要题目有标准答案（数学天然满足——可验证奖励，Week 17 的老朋友）。本质是蒙特卡洛估计每个中间状态的 V 值。
- 意义：PRM 从"贵族玩具"变成"可流水线生产"，后面 Qwen2.5-Math、DeepSeek-R1 的路线都受益于此。

> 🎯 **面试一句话总结演进**：PRM800K 证明"过程监督有用但贵"，MATH-Shepherd 证明"过程监督可以白嫖"——从人工逐步标注 → 自动 rollout 标注，和 RLHF 从人类偏好 → RLAIF 的演进完全同构。

### ⚖️ ORM vs PRM：面试必考对照表

| 维度 | ORM（结果奖励） | PRM（过程奖励） |
|---|---|---|
| **打分粒度** | 整条轨迹 → 一个分 | 每个推理步 → 一个分 |
| **监督信号** | 稀疏（终点一次） | 稠密（步步有反馈） |
| **信用分配** | 无归因，全轨迹平分 reward | 精确定位出错步骤 |
| **标注成本** | 低（对答案就行） | 高（逐步标注）or 白嫖（rollout 自动） |
| **适用场景** | 短答案、可程序验证（代码单测） | 长 CoT、多步推理（数学证明） |
| **被 hacking 难度** | 相对高（要赌对终点） | 相对低（可以每步装模作样，见下文坑） |
| **典型代表** | RLHF 里的 RM、GRPO 的 outcome reward | PRM800K、MATH-Shepherd、Qwen2.5-Math-PRM |

**不是二选一，是组合拳**：工业界主流是 **ORM 打底 + PRM 精修**——先用 outcome reward 做 RL（GRPO），再用 PRM 做推理时的搜索引导（beam search / MCTS）。原因很务实：ORM 便宜可以大规模训，PRM 贵但推理时按步调用精准。

### 🔧 PRM 的三个工程用途（背下来，面试直接展开）

**用途一：训练时的 step-level shaping（RL 奖励塑形）**
GRPO 里 reward 只在终点给 → 换成 PRM 逐步给分，轨迹中段的烂步骤立刻被惩罚，不用等终点背锅。veRL 等框架里对应 **step-level reward / per-turn reward** 的接口——Day 113 讲 agentic rollout 的"信用分配"痛点时提过，PRM 就是它的药。

**用途二：推理时的搜索引导（test-time 主力用法）**
- **Step-level Best-of-N / Beam Search**：每生成一步，PRM 给候选步打分，保留 top-k 分支往下走。搜索树从"先射箭后画靶"变成"步步校准"。
- **MCTS 的 step 评估器**：明天 Day 117 的主题——rStar、AlphaMath 这类方法用 PRM 当 MCTS 的 value function，模拟到叶子用 PRM 回溯打分。

**用途三：数据筛选与质量过滤**
用 PRM 扫 RL 的 rollout 轨迹：哪一步开始崩的直接剪掉，只留高质量段做 SFT / 拒绝采样。R1 配方里"拒绝采样 80 万条"那一步，用 PRM 过滤比用 ORM 过滤更精细。

### ⚠️ PRM 的四个坑（面试官爱听这个，显得你不迷信论文）

**坑一：Reward Hacking 变形——"每步都装模作样"**
Goodhart 定律：指标成了目标就不是好指标。ORM 被 hack 是终点蒙对；PRM 被 hack 是**每一步都写得像那么回事**——格式工整、自信满满、步步有"验证"，但推导全是错的。对策：PRM 训练数据要混入"每步都对但方向错"的对抗样本；部署时 PRM 分数要和 ORM/规则验证交叉检查。

**坑二：步骤边界的定义模糊**
"一步"是什么？一个等号？一个换行？一个句子？不同标注体系下 PRM 的分数没法直接比。工程上通常按**换行/子结论**切分，但面试时指出"step granularity 没有金标准"是清醒的表现。

**坑三：分布外失效**
PRM 在 MATH 上训的，拿去验证代码推理直接失灵——数学的"好步骤"和代码的"好步骤"长得完全不同。**PRM 的领域迁移能力远差于生成模型本身**，每换一个任务基本要重标/重训。

**坑四：验证成本不是免费的**
逐步打分 = 多次调用 verifier 模型 = 推理延迟和成本线性上涨。test-time compute 的账本是统一的：PRM 多花的每一个 token 都要从"它省的搜索分支"里赚回来——这就是 **compute-optimal 分配**（Day 115 提过）在 verifier 侧的版本。

### 🗺️ 和本周地图的连接

```
Day 115  三类 test-time 策略（框架）
   │
Day 116  PRM —— 给策略装上"眼睛"（今日）
   │
Day 117  ToT / MCTS / rStar —— PRM 驱动的树搜索（明天主战场）
   │
Day 118  Self-Consistency / Self-Refine —— 不需要 PRM 的穷人套餐
   │
Day 119  系统工程 —— PRM 部署的 latency/成本账本
   │
Day 120  Verifier 生态全家桶 —— PRM 是其中最贵也最准的一员
```

### 🎤 高频连环问速答模板

**Q1：PRM 和 ORM 的本质区别？一句话。**
> ORM 是阅卷老师只看最终答案，PRM 是教练盯着每一步——前者便宜但不知道错哪了，后者贵但能精确定位出错步骤、支持步步剪枝。

**Q2：PRM 的训练数据怎么来？**
> 三条路：人工逐步标注（PRM800K，贵但准）、MCTS rollout 自动标注（MATH-Shepherd，从中间步采样看最终答对率，白嫖）、以及隐式 PRM——不显式训 verifier，直接用 RL 让 policy 自己内化过程好坏（R1 路线实质上是隐式的）。

**Q3：有了 PRM，为什么还需要 ORM？**
> 成本与鲁棒性。ORM 便宜、可大规模并行、被 hack 面窄；PRM 贵且有分布外问题。工业界组合是 ORM 做训练主 reward（GRPO 引擎），PRM 做推理时的搜索引导——训练和推理的分工不同。

**Q4：PRM 会不会被 hack？**
> 会，而且比 ORM 更隐蔽。模型可以学会"生成看起来严谨的步骤"骗过 PRM——工整的格式、假装的验证。所以生产环境要 PRM + 规则验证（数学用 sympy、代码用单测）双保险，单一 verifier 迟早被 Goodhart。

**Q5：你在 veRL 里怎么用 PRM？（结合项目追问）**
> 两条路：一是 step-level reward 接口，把 PRM 分数作为 per-turn reward 喂给 GRPO，替代稀疏的终局 reward；二是 rollout 过滤，PRM 扫轨迹把崩掉的步之后截断，防止坏轨迹进训练集污染梯度。

**Q6：数学以外的领域（代码、agent）PRM 怎么做？**
> 核心难点是"步骤"不好切。代码可以用**单测通过率增量**当过程信号（每写一个函数跑一遍测试）；agent 可以用**工具调用成功率**当 step reward——本质是"把可执行的中间检查包装成 PRM"，比纯模型打分更抗 hack。

### 💬 今日金句

> **结果告诉你"输了"，过程告诉你"输在哪"。推理时代的算力，大半花在"输在哪"上。**

---

## ✅ Day 116 自检清单

- [ ] 能默写回溯三件套：路径记录（原地标记）、四方向 DFS、退出撤销
- [ ] 说清 Word Search 复杂度 O(m·n·4^L) 里 4^L 的来历，以及剪枝如何让期望远优于最坏
- [ ] Follow-up 能讲 Trie 剪枝 vs 逐词 DFS 的复杂度鸿沟（10⁵ 词场景）
- [ ] 能一句话区分 ORM / PRM，并说出 PRM800K 和 MATH-Shepherd 各自的贡献
- [ ] 背下 ORM vs PRM 对照表的 7 个维度
- [ ] PRM 三个工程用途：训练 shaping / 推理搜索引导 / 数据筛选
- [ ] PRM 四个坑：装模作样 hack、step 边界定义、分布外失效、验证成本
- [ ] 连环问 Q1-Q6 能在 30 秒内 each 答出要点
- [ ] 能向完全不懂的人解释："Trie 前缀检查 = 算法版过程验证器"

---

## 🔜 明日预告

**Day 117 — 推理树搜索：ToT / MCTS / rStar**。今天有了 PRM 这只"眼睛"，明天让它带路：从 Chain-of-Thought 的独木桥，走到 Tree-of-Thought 的分岔花园——再往前一步，就是 AlphaGo 的 MCTS 在数学推理上的复活。搜索 + 验证，双剑合璧。
