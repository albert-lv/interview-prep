# 每日面试备战：Day 38 — 单词接龙（Word Ladder）

> **Week 6 — 图论与搜索（BFS/DFS、拓扑排序、最短路）**
> 日期：2026-07-13

---

## 一、今日算法题

### 题目：单词接龙（Word Ladder）

**LeetCode 127** | 难度：🟡 中等偏难 | 推荐指数：⭐⭐⭐⭐⭐

---

### 题目描述

字典 `wordList` 中包含了由小写英文字母组成的单词列表。给定两个单词 `beginWord` 和 `endWord`，找到从 `beginWord` 到 `endWord` 的**最短转换序列**的长度。转换需遵循如下规则：

1. 每次转换只能改变**一个字母**
2. 转换过程中的每个中间词必须是 `wordList` 中的单词
3. `beginWord` 不一定是 `wordList` 中的单词，但 `endWord` 必须是

如果不存在这样的转换序列，返回 `0`。

**示例 1：**

```
输入：beginWord = "hit", endWord = "cog", 
      wordList = ["hot","dot","dog","lot","log","cog"]
输出：5
解释：一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog"，返回它的长度 5。
```

**示例 2：**

```
输入：beginWord = "hit", endWord = "cog",
      wordList = ["hot","dot","dog","lot","log"]
输出：0
解释：endWord "cog" 不在字典中，所以无法进行转换。
```

---

### 核心思路：BFS 在隐式图上的经典应用 🔤

这道题最妙的地方在于：**图不是显式给你的，而是藏在单词之间的"差一个字母"关系里。**

#### 第一步：把问题抽象成图

把每个单词看作图中的一个节点。如果两个单词只相差一个字母，就在它们之间连一条边（无向边，因为可以互相转换）。

那么问题变成：**从节点 beginWord 到 endWord 的最短路径长度是多少？**

因为每条边的"代价"都是 1（改一个字母），所以这就是一个**无权图的最短路问题**，BFS 是最自然的选择。

```
示例 1 的隐式图：

    hit ── hot
           │
    dot ──┼── lot
     │    │    │
    dog ──┘    log
     │
    cog

最短路径：hit → hot → dot → dog → cog，长度 5
```

#### 第二步：怎么高效找"只差一个字母"的邻居？

暴力做法：对于当前单词，遍历字典中所有单词，逐一对比是否只差一个字母。时间复杂度 O(N × L)，其中 N 是字典大小，L 是单词长度。

**优化做法：通配符映射（Pattern Matching）**

对于每个单词，把它每一位替换成 `*`，生成所有可能的"模式"。例如：
- `hot` → `*ot`, `h*t`, `ho*`
- `dot` → `*ot`, `d*t`, `do*`

然后把所有匹配同一个模式的单词归为一组。两个单词能互相转换，当且仅当它们共享至少一个模式。

```
预处理：
  *ot → [hot, dot, lot]
  h*t → [hot]
  ho* → [hot]
  d*t → [dot]
  do* → [dot, dog]
  ...
```

这样，找邻居时只需要查当前单词的所有模式对应的单词列表即可，预处理一次，查询 O(1)（均摊）。

#### 第三步：标准 BFS

从 `beginWord` 开始 BFS，一层一层扩展。第一次到达 `endWord` 时，当前的步数就是最短转换序列的长度。

---

### 代码实现：标准 BFS + 通配符映射

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        # 快速判断 endWord 是否在字典中
        if endWord not in wordList:
            return 0

        n = len(beginWord)

        # 1. 预处理：构建 模式 → 单词列表 的映射
        pattern_to_words = defaultdict(list)
        for word in wordList:
            for i in range(n):
                pattern = word[:i] + '*' + word[i+1:]
                pattern_to_words[pattern].append(word)

        # 2. BFS
        queue = deque([(beginWord, 1)])  # (当前单词, 当前步数)
        visited = {beginWord}  # 避免重复访问

        while queue:
            word, steps = queue.popleft()

            # 尝试改变每一位
            for i in range(n):
                pattern = word[:i] + '*' + word[i+1:]

                # 所有能通过改变第 i 位得到的单词
                for neighbor in pattern_to_words[pattern]:
                    # 找到目标
                    if neighbor == endWord:
                        return steps + 1

                    # 未访问过，加入队列
                    if neighbor not in visited:
                        visited.add(neighbor)
                        queue.append((neighbor, steps + 1))

                # 可选优化：清空已访问的模式，避免重复搜索
                pattern_to_words[pattern] = []

        return 0  # 无法到达
```

---

### 🔥 进阶：双向 BFS（Bidirectional BFS）

标准 BFS 的时间复杂度是 O(N × L)，其中 N 是字典中单词数。当字典很大时，搜索空间会爆炸。

**双向 BFS 的思想**：同时从起点和终点开始 BFS，两个搜索 frontier 在中间相遇时停止。

> 为什么更快？
> 
> 单向 BFS 搜索树的节点数：1 + b + b² + ... + b^d ≈ b^d（b 是分支因子，d 是最短距离）
> 
> 双向 BFS 每个方向搜索到深度 d/2：2 × (1 + b + b² + ... + b^(d/2)) ≈ 2 × b^(d/2)
> 
> 当 b 较大且 d 较深时，b^(d/2) 远小于 b^d。例如 b=10, d=6：单向 10⁶ = 1,000,000，双向 2×10³ = 2,000。

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0

        n = len(beginWord)
        pattern_to_words = defaultdict(list)
        for word in wordList:
            for i in range(n):
                pattern = word[:i] + '*' + word[i+1:]
                pattern_to_words[pattern].append(word)

        # 双向 BFS：两个集合表示当前层的 frontier
        begin_visited = {beginWord: 1}   # 单词 -> 步数
        end_visited = {endWord: 1}

        while begin_visited and end_visited:
            # 每次扩展较小的 frontier，保持平衡
            if len(begin_visited) > len(end_visited):
                begin_visited, end_visited = end_visited, begin_visited

            next_level = {}  # 下一层

            for word, steps in begin_visited.items():
                for i in range(n):
                    pattern = word[:i] + '*' + word[i+1:]

                    for neighbor in pattern_to_words[pattern]:
                        # 在另一端 frontier 中找到了！
                        if neighbor in end_visited:
                            return steps + end_visited[neighbor]

                        if neighbor not in begin_visited:
                            next_level[neighbor] = steps + 1

                    pattern_to_words[pattern] = []

            begin_visited = next_level

        return 0
```

> ⚠️ **面试技巧**：如果面试官没有特别要求，先写标准 BFS。如果他说"数据范围很大，能不能优化"，再引出双向 BFS。这展示了你对不同复杂度的权衡能力。

---

### 复杂度分析

| 实现方式 | 时间复杂度 | 空间复杂度 | 说明 |
|---------|-----------|-----------|------|
| **标准 BFS** | O(N × L²) | O(N × L) | N = 单词数，L = 单词长度。L² 来自字符串拼接和比较 |
| **双向 BFS** | O(N × L²)（最坏情况同标准BFS，实际快很多） | O(N × L) | 实际搜索空间大幅降低，面试中的"最优解" |

> **注**：预处理构建 pattern_to_words 需要 O(N × L²) 时间，BFS 过程中每个单词最多被访问一次，查询邻居也是 O(L²)。所以总时间复杂度是 O(N × L²)。

---

### 🎯 为什么不用 DFS？

这道题求的是**最短路径长度**，DFS 会一条路走到黑再回溯，虽然在"找到任意一条路径"时可以用，但找最短路径时效率很低——DFS 可能需要遍历整个图才能确定最短距离。

**BFS 的优势**：按层扩展，第一次到达目标时一定是最短路径。这是无权图最短路的经典特性。

**什么时候可以用 DFS？** 如果题目问的是"是否存在一条转换路径"（不需要最短），DFS 也可以。

---

## 二、面试技巧

### 🤝 如何"从不会聊到思路"

看到这道题，第一步不是想 BFS，而是**识别问题结构**：

**推荐话术结构：**

> "题目要求找从 beginWord 到 endWord 的最短转换序列，每次只能改一个字母。这让我想到，可以把每个单词当作图中的一个节点，如果两个单词只差一个字母，就连一条边。这样问题就变成了**在无权图中找最短路**，自然想到用 **BFS** 来解决。"

**面试官想听的：**
1. 你能把"单词转换"抽象成图的节点和边
2. 你知道"最短序列"对应"最短路"
3. 你能判断 BFS 适用于无权图最短路

---

### 🎯 常见追问 & 满分回答

| 追问 | 面试官想考察 | 回答要点 |
|------|-------------|---------|
| "怎么高效判断两个单词只差一个字母？" | 预处理思维 | "每次遍历字典 O(N) 太慢。可以预处理：对每个单词生成所有'通配符模式'（如 hot → *ot, h*t, ho*），用哈希表按模式分组。查询时直接查当前单词的模式列表，均摊 O(1)。" |
| "能不能用 DFS？" | BFS vs DFS 的理解 | "DFS 也能找路径，但它一条路走到黑再回溯，找最短路径时需要遍历很多分支。BFS 按层扩展，第一次到达目标就是最短路径，更适合这道题。" |
| "字典很大时怎么优化？" | 算法优化能力 | "用双向 BFS。同时从起点和终点开始搜索，两个 frontier 相遇时停止。搜索空间从 b^d 降到 2×b^(d/2)，实际效果提升显著。" |
| "时间复杂度是多少？" | 复杂度分析 | "预处理 O(N×L²)，BFS 每个单词访问一次，总时间 O(N×L²)。空间上需要存 pattern_to_words 和 visited 集合，也是 O(N×L)。" |
| "如果要求输出所有最短路径呢？" | 进阶思考 | "需要记录每个单词的前驱节点（可能多个），然后用 BFS 分层找到所有最短路径，最后回溯构造。LeetCode 126 就是这个问题，要用 DFS + BFS 分层结合。" |
| "如果单词长度很大（比如 1000），但字典很小呢？" | 边界情况分析 | "单词长度 L 很大时，模式数量 L 也会变大，但每个模式对应的单词数可能很少。这种情况下通配符映射仍然有效，但如果 L >> N，也可以考虑直接用位运算或逐字符比较。" |

---

### 📝 代码书写技巧

1. **先检查 endWord 是否在字典中**：如果不在，直接返回 0，避免无意义的搜索
2. **通配符模式预处理**：`word[:i] + '*' + word[i+1:]`，注意字符串切片不会越界
3. **visited 集合必不可少**：无向图中不记录访问状态会死循环（如 hot ↔ dot）
4. **可选优化**：访问过的 pattern 可以清空 `pattern_to_words[pattern] = []`，避免后续重复遍历
5. **双向 BFS 的 swap 技巧**：`if len(begin_visited) > len(end_visited): swap`，保持两边 frontier 大小平衡

---

### 💡 面试常犯的错误

| 错误 | 为什么错 | 怎么改 |
|------|---------|--------|
| 用 DFS 找最短路径 | DFS 找到的不一定是最短路径 | 无权图最短路用 BFS |
| 不做 visited 去重 | 同一个单词可能从多个路径到达，导致重复入队和死循环 | 必须用 visited 集合 |
| 暴力遍历字典找邻居 | 时间复杂度 O(N² × L)，会超时 | 用通配符映射预处理，查询降到 O(1) |
| 忘记清空已访问的 pattern | 同一模式下的单词会被反复遍历 | 访问后 `pattern_to_words[pattern] = []` |
| BFS 步数初始化为 0 | beginWord 本身算第 1 步 | 初始 `steps = 1`，返回时要 +1 |

---

### 🔗 与其他题目的联系

| 题目 | 联系 |
|------|------|
| **单词接龙 II**（LeetCode 126）| 输出所有最短路径，需要 BFS 分层建图 + DFS 回溯 |
| **打开转盘锁**（LeetCode 752）| 类似隐式图 BFS，每次转一位数字 |
| **基因序列最小变化**（LeetCode 433）| 基本一样的模型，只是从单词变成基因片段 |
| **最小基因变化** | 同类型题目，BFS + 隐式图 |
| **岛屿数量**（Day 34）| 图论搜索的入门题，用 DFS/BFS 在显式网格图上遍历 |
| **腐烂的橘子**（Day 35）| 多源 BFS，和单向 BFS 一脉相承 |

---

## 三、今日学习打卡

✅ **算法题**：单词接龙（Word Ladder）— BFS 在隐式图上的经典应用  
✅ **核心技能**：通配符映射预处理、隐式图建模、BFS 最短路、双向 BFS 优化  
✅ **面试技巧**：问题抽象（单词→图节点）、BFS 适用场景判断、双向搜索优化策略

**明日预告**：打开转盘锁（Open the Lock）— 隐式图 BFS 的又一经典，状态和密码锁的转换

---

> 📝 **学习建议**：Word Ladder 是图论 BFS 面试中最高频的题型之一。核心要练会三点：
> 1. **抽象能力**：看到"最短转换序列"立刻反应到"无权图最短路 → BFS"
> 2. **预处理技巧**：通配符映射是这类题的标配优化，必须熟练
> 3. **双向 BFS**：数据量大时的杀手锏，能显著提升搜索效率
>
> 建议把 LeetCode 126（Word Ladder II）也刷了，体会从"求最短长度"到"求所有最短路径"的升级。同时推荐 LeetCode 752（Open the Lock），是同一个思路的不同包装。
