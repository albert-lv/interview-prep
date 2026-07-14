# Day 39：被围绕的区域（Surrounded Regions）

> **Week 6 第 6 天 | 图论与搜索**  
> 2026-07-14

---

## 一、今日算法题

### 题目描述

给定一个 `m × n` 的二维矩阵 `board`，由字符 `'X'` 和 `'O'` 组成。找到所有被 `'X'` 围绕的区域，并将这些区域里所有的 `'O'` 用 `'X'` 填充。

**注意：** 如果 `'O'` 直接或间接与矩阵的边界相连，则它不会被填充。

**示例：**

```
输入：
X X X X
X O O X
X X O X
X O X X

输出：
X X X X
X X X X
X X X X
X O X X
```

解释：被围绕的位于中间的 `'O'` 被翻转为 `'X'`，但与边界相连的左下角的 `'O'` 保持不变。

---

### 思路分析

这道题最直观的想法是：对每个 `'O'` 做 DFS，看它能不能逃到边界。但这样会有很多重复搜索，而且需要记录"是否被围绕"，比较麻烦。

**逆向思维**才是这题的精髓 💡：

> 与其判断哪些 `'O'` 被围绕，不如先找出哪些 `'O'` **不会被围绕**——所有与边界相连的 `'O'` 及其连通块，都是安全的。

**三步走策略：**

1. **边界染色**：从矩阵的四条边上的每个 `'O'` 出发，DFS/BFS 标记所有能到达的 `'O'`（改成临时字符，比如 `'#'`）
2. **填充内部**：遍历整个矩阵，将所有仍然是 `'O'` 的位置变成 `'X'`（这些就是真正被围绕的）
3. **恢复边界**：将所有临时字符 `'#'` 恢复为 `'O'`

---

### 代码实现

```python
class Solution:
    def solve(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        if not board or not board[0]:
            return
        
        m, n = len(board), len(board[0])
        
        def dfs(r, c):
            """从边界出发，标记所有连通的'O'为'#'"""
            if r < 0 or r >= m or c < 0 or c >= n or board[r][c] != 'O':
                return
            board[r][c] = '#'  # 标记为安全区域
            # 四个方向扩散
            dfs(r + 1, c)
            dfs(r - 1, c)
            dfs(r, c + 1)
            dfs(r, c - 1)
        
        # Step 1: 从四条边的'O'开始做DFS
        # 上下两条边
        for j in range(n):
            if board[0][j] == 'O':
                dfs(0, j)
            if board[m - 1][j] == 'O':
                dfs(m - 1, j)
        
        # 左右两条边（注意四个角已经处理过了）
        for i in range(1, m - 1):
            if board[i][0] == 'O':
                dfs(i, 0)
            if board[i][n - 1] == 'O':
                dfs(i, n - 1)
        
        # Step 2 & 3: 遍历矩阵，'O'->'X', '#'->'O'
        for i in range(m):
            for j in range(n):
                if board[i][j] == 'O':
                    board[i][j] = 'X'  # 被围绕，填充
                elif board[i][j] == '#':
                    board[i][j] = 'O'  # 恢复边界连通区域
```

**BFS 版本**（迭代实现，适合深度很大时避免栈溢出）：

```python
from collections import deque

class Solution:
    def solve(self, board: List[List[str]]) -> None:
        if not board or not board[0]:
            return
        
        m, n = len(board), len(board[0])
        queue = deque()
        
        # 将边界所有'O'入队
        for i in range(m):
            for j in [0, n - 1]:
                if board[i][j] == 'O':
                    queue.append((i, j))
                    board[i][j] = '#'
        for j in range(1, n - 1):
            for i in [0, m - 1]:
                if board[i][j] == 'O':
                    queue.append((i, j))
                    board[i][j] = '#'
        
        # BFS 扩散
        while queue:
            r, c = queue.popleft()
            for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < m and 0 <= nc < n and board[nr][nc] == 'O':
                    board[nr][nc] = '#'
                    queue.append((nr, nc))
        
        # 最终处理
        for i in range(m):
            for j in range(n):
                if board[i][j] == '#':
                    board[i][j] = 'O'
                elif board[i][j] == 'O':
                    board[i][j] = 'X'
```

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|------|--------|------|
| 时间 | **O(m × n)** | 每个节点最多被访问一次 |
| 空间 | **O(m × n)** | DFS 递归栈 / BFS 队列，最坏情况是整个矩阵 |

---

## 二、面试技巧

### 1. 图论题的常见开场白

面试官出图论题时，建议**先说清思路再写代码**。一个通用的开场模板：

> "这道题可以把矩阵看作一个图，每个格子是一个节点，相邻格子之间有边。题目要求的是找到所有不与边界连通的 `'O'` 连通块。我的思路是用逆向思维——先从边界出发，标记所有能到达的安全区域，然后再处理内部的被围绕区域。"

### 2. "逆向思维"是图论面试的高频考点

这道题最精彩的部分不是 DFS/BFS 本身，而是**逆向切入的角度**：

| 正向思维 | 逆向思维 |
|----------|----------|
| 对每个内部 `'O'` 判断是否能逃到边界 | 从边界 `'O'` 出发，标记所有安全的 `'O'` |
| 复杂度高，需要重复搜索 | 一次遍历即可 |
| 容易写错边界条件 | 逻辑清晰，三步搞定 |

🎯 **面试话术**："正向判断比较麻烦，我考虑用逆向思维——先找到所有不会被填充的 `'O'`，剩下的就是要填充的。"

### 3. DFS vs BFS 怎么选？

| 场景 | 推荐 | 原因 |
|------|------|------|
| 矩阵/图的规模较小 | DFS | 代码更简洁 |
| 深度可能很大（如蛇形路径）| BFS | 避免递归栈溢出 |
| 需要最短路/最少步数 | BFS | 天然按层遍历 |
| 需要回溯/枚举所有路径 | DFS | 配合剪枝更高效 |

**面试时可以这样说**："我先写 DFS 版本，因为它更直观。如果面试官担心栈溢出，我也可以改成 BFS 的迭代实现。"

### 4. 空间优化小技巧

- 这题要求原地修改矩阵，所以用了临时字符 `'#'` 做标记
- 如果面试官追问"能不能不用临时字符"，可以考虑用位运算（如果字符集允许）或者先收集所有安全坐标再统一处理
- 但通常情况下，临时字符的方法已经足够好，不要过度优化

### 5. 举一反三

这道题的核心模式——**"从边界出发的逆向标记"**——在以下题目中同样适用：

- **Pacific Atlantic Water Flow**（太平洋大西洋水流问题）：从两个海洋边界同时反向搜索
- **Walls and Gates**（墙与门）：从所有门出发 BFS 填充最短距离
- **Number of Enclaves**（飞地的数量）：与这题几乎一模一样

---

## 三、今日小结

- ✅ 图论搜索的核心不是死记模板，而是**建图视角 + 切入角度**
- ✅ 矩阵题优先考虑四个方向的 DFS/BFS
- ✅ 遇到"判断连通性"的问题，试试**逆向思维**——从安全区出发反推
- ✅ DFS 代码简洁，BFS 更安全（无栈溢出风险），面试时两种都能写是加分项

> Week 6（图论与搜索）到此结束！六周坚持下来，你已经覆盖了 DP（入门→线性→区间→树形→状态压缩）、贪心、二分、图论搜索。下周可以进入 **高级数据结构（并查集、Trie、线段树、树状数组）** 或者 **回溯与搜索（排列组合、子集、N皇后）** 的专题。
