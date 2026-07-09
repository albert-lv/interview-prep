# 每日面试备战：Day 34 — 岛屿数量（Number of Islands）

> **Week 6 — 图论与搜索（BFS/DFS、拓扑排序、最短路）**
> 日期：2026-07-09

---

## 一、今日算法题

### 题目：岛屿数量（Number of Islands）

**LeetCode 200** | 难度：🟡 中等 | 推荐指数：⭐⭐⭐⭐⭐

---

### 题目描述

给定一个由 `'1'`（陆地）和 `'0'`（水）组成的二维网格，计算其中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由**水平或垂直**方向上相邻的陆地连接而成。此外，你可以假设网格的四边均被水包围。

**示例：**

```
输入: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出: 3
```

解释：左上两块连着的 `1` 是第一座岛，中间单独一个 `1` 是第二座，右下两个连着的 `1` 是第三座。

---

### 核心思路：DFS 淹没法 🌊

这道题是**图论搜索的敲门砖**。本质上是统计**连通分量的数量**。

两种经典做法：
- **DFS（深度优先搜索）**：遇到 `1` 就开始"淹没"——把连通的所有 `1` 变成 `0`，每启动一次 DFS 计数 +1
- **BFS（广度优先搜索）**：同理，用队列逐层扩展淹没

**DFS 版本更简洁，BFS 版本更稳定（避免递归栈溢出）。**

---

### 代码实现（Python）

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid or not grid[0]:
            return 0

        rows, cols = len(grid), len(grid[0])
        count = 0

        def dfs(r, c):
            # 越界或遇到水，直接返回
            if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] == '0':
                return
            # 淹掉这块陆地
            grid[r][c] = '0'
            # 四个方向扩散
            dfs(r + 1, c)
            dfs(r - 1, c)
            dfs(r, c + 1)
            dfs(r, c - 1)

        for i in range(rows):
            for j in range(cols):
                if grid[i][j] == '1':
                    count += 1       # 发现新岛屿！
                    dfs(i, j)        # 把整个岛淹掉

        return count
```

---

### BFS 版本（避免递归深度问题）

```python
from collections import deque

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid or not grid[0]:
            return 0

        rows, cols = len(grid), len(grid[0])
        count = 0

        for i in range(rows):
            for j in range(cols):
                if grid[i][j] == '1':
                    count += 1
                    # BFS 淹没
                    queue = deque([(i, j)])
                    grid[i][j] = '0'
                    while queue:
                        r, c = queue.popleft()
                        for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
                            nr, nc = r + dr, c + dc
                            if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == '1':
                                grid[nr][nc] = '0'
                                queue.append((nr, nc))

        return count
```

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|------|--------|------|
| **时间** | O(M × N) | 每个格子最多访问一次 |
| **空间** | O(M × N) | 最坏情况递归栈/队列深度（全是陆地） |

> 面试常考：如果网格极大（如 1e6 × 1e6），递归 DFS 会爆栈，必须用 BFS 或并查集。

---

### 延伸：并查集版本（进阶）

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.count = 0

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px != py:
            self.parent[px] = py
            self.count -= 1

# 思路：把所有陆地初始化进并查集，相邻陆地 union，最后 count 就是答案
```

并查集适合**动态连通性**场景（网格在变化），面试提到能加分 💯

---

## 二、面试技巧

### 🤝 如何"从不会聊到思路"

这道题是面试高频，但很多同学卡在"怎么开始想"。

**推荐话术结构：**

> "看到连通性、相邻区域的问题，我的第一反应是图搜索。可以把每个 `1` 看成节点，相邻关系看成边，那问题就是求**连通分量的个数**..."

**面试官想听的：**
1. 你能把问题抽象成图（节点 + 边）
2. 你知道连通分量 = 岛屿数量
3. 你会比较 DFS/BFS 的优劣

---

### 🎯 常见追问 & 满分回答

| 追问 | 面试官想考察 | 回答要点 |
|------|-------------|---------|
| "DFS 和 BFS 选哪个？" | 对栈/队列的理解 | "DFS 写起来短，但递归深度有限制；BFS 更稳定，适合大图。我实际工作中会看数据规模选。" |
| "空间复杂度能优化吗？" | 原地修改的理解 | "我的代码已经是原地修改了（grid 本身当 visited 用），如果要求不能改原数组，可以额外建一个 visited 矩阵。" |
| "并查集的做法了解吗？" | 知识广度 | "了解，并查集在动态连通、增量合并场景更优，比如网格实时变化。但静态求解 DFS/BFS 就够了。" |
| "如果网格是 1e9 级别？" | 系统设计思维 | "需要分片处理或者流式处理，单机内存装不下，可以上分布式图计算（如 Spark GraphX）。" |

---

### 📝 代码书写技巧

1. **先写边界判断**：`if not grid or not grid[0]: return 0` — 面试官看你细节
2. **方向数组写注释**：`dr = [1, -1, 0, 0]; dc = [0, 0, 1, -1]` — 清晰
3. **BFS 用 popleft 而不是 pop()**：后者是栈，成了 DFS 了 😅

---

### 💡 一题多解的面试加分策略

这道题有 **3 种标准解法**：

| 解法 | 时间 | 空间 | 适用场景 |
|------|------|------|---------|
| DFS | O(MN) | O(MN) | 代码最短，面试首选 |
| BFS | O(MN) | O(MN) | 大图防栈溢出 |
| 并查集 | O(MN × α) | O(MN) | 动态连通、增量查询 |

**面试技巧**：先写 DFS，然后提一句"也可以用 BFS 或并查集"，展示你的知识面。但不要过度展开，除非面试官追问。

---

## 三、今日学习打卡

✅ **算法题**：岛屿数量（Number of Islands）— DFS / BFS / 并查集
✅ **核心技能**：连通分量计数、图搜索入门、原地标记 visited
✅ **面试技巧**：图论问题的抽象方式、多解法对比、追问应答策略

**明日预告**：克隆图（Clone Graph）— DFS/BFS 深拷贝的图版本

---

> 📝 **学习建议**：今天这道题是图论搜索的"Hello World"。务必手撕一遍，确保 DFS 和 BFS 都能流畅写出来。面试中"岛屿数量"和它的变体（如岛屿周长、岛屿最大面积）出现频率极高，掌握这一题等于掌握了一类题。
