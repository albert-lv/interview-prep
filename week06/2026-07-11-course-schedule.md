# 每日面试备战：Day 36 — 课程表（Course Schedule）

> **Week 6 — 图论与搜索（BFS/DFS、拓扑排序、最短路）**
> 日期：2026-07-11

---

## 一、今日算法题

### 题目：课程表（Course Schedule）

**LeetCode 207** | 难度：🟡 中等 | 推荐指数：⭐⭐⭐⭐⭐

---

### 题目描述

你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1`。在选修某些课程之前需要一些先修课程。例如，想要学习课程 `0`，你需要先完成课程 `1`，我们用一个匹配来表示： `[1, 0]` 。

给定课程总量以及它们的先决条件，判断是否可能完成所有课程的学习。

**示例 1：**

```
输入: numCourses = 2, prerequisites = [[1,0]]
输出: true
解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成课程 0。这是可能的。
```

**示例 2：**

```
输入: numCourses = 2, prerequisites = [[1,0],[0,1]]
输出: false
解释: 学习课程 1 之前，你需要先完成课程 0；并且学习课程 0 之前，你也先完成课程 1。这是不可能的。
```

---

### 核心思路：拓扑排序 — 判断有向图是否存在环 🔄

这道题的本质是：**给定一个有向图，判断是否存在环**。如果存在环，说明有循环依赖，无法完成所有课程。

**拓扑排序**就是对有向无环图（DAG）的节点进行线性排序，使得对于图中的每条有向边 `(u, v)`，节点 `u` 在排序中都出现在节点 `v` 之前。

两种经典解法：
- **Kahn 算法（BFS）**：计算每个节点的入度，不断移除入度为 0 的节点
- **DFS 后序遍历**：利用 DFS 检测环，通过后序遍历得到拓扑序

**Kahn 算法更直观，DFS 版本更简洁。**

---

### 代码实现：Kahn 算法（BFS）

```python
from collections import deque

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        # 构建邻接表和入度数组
        graph = [[] for _ in range(numCourses)]
        in_degree = [0] * numCourses

        for a, b in prerequisites:
            # b -> a，表示先修 b 才能修 a
            graph[b].append(a)
            in_degree[a] += 1

        # 将所有入度为 0 的节点入队（这些课程没有先修要求，可以直接学）
        queue = deque()
        for i in range(numCourses):
            if in_degree[i] == 0:
                queue.append(i)

        visited = 0  # 记录已经处理的课程数

        # BFS：不断移除入度为 0 的节点
        while queue:
            course = queue.popleft()
            visited += 1
            for next_course in graph[course]:
                in_degree[next_course] -= 1  # 相当于移除边 course -> next_course
                if in_degree[next_course] == 0:
                    queue.append(next_course)

        # 如果所有课程都被处理，说明没有环
        return visited == numCourses
```

---

### 为什么这样能检测环？

**核心直觉：有向无环图（DAG）一定至少存在一个入度为 0 的节点。**

- 如果没有环，总能找到一个"起点"（没有先修要求的课程）
- 把这个起点移除后，剩下的图仍然是一个 DAG，继续找下一个起点
- 如果最后还有节点没被移除，说明这些节点互相依赖，形成了环

```
示例：[[1,0],[2,1],[3,2]]

  0 -> 1 -> 2 -> 3

入度：0:0, 1:1, 2:1, 3:1

Step 1: 0 入度为 0，移除 0。1 的入度变为 0
Step 2: 1 入度为 0，移除 1。2 的入度变为 0
Step 3: 2 入度为 0，移除 2。3 的入度变为 0
Step 4: 3 入度为 0，移除 3

visited = 4 == numCourses，返回 true ✅

示例：[[1,0],[0,1]]

  0 <-> 1  （有环！）

入度：0:1, 1:1

没有入度为 0 的节点！queue 为空，直接结束
visited = 0 != 2，返回 false ❌
```

---

### DFS 版本（检测环 + 拓扑序）

```python
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        graph = [[] for _ in range(numCourses)]
        for a, b in prerequisites:
            graph[b].append(a)

        # 0 = 未访问，1 = 正在访问（当前 DFS 路径中），2 = 已访问
        state = [0] * numCourses

        def has_cycle(course):
            if state[course] == 1:  # 在当前 DFS 路径中再次遇到，说明有环
                return True
            if state[course] == 2:  # 已经处理过，直接返回
                return False

            state[course] = 1  # 标记为"正在访问"
            for next_course in graph[course]:
                if has_cycle(next_course):
                    return True
            state[course] = 2  # 标记为"已访问"
            return False

        for i in range(numCourses):
            if state[i] == 0 and has_cycle(i):
                return False
        return True
```

> DFS 版本用三种状态标记节点，是**检测有向图环**的经典技巧。`state[course] == 1` 意味着当前 DFS 路径上已经访问过这个节点，说明有环。

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|------|--------|------|
| **时间** | O(V + E) | V 是课程数，E 是先修关系数。建图 + 遍历 |
| **空间** | O(V + E) | 邻接表 + 入度数组/访问状态数组 |

> 线性复杂度，非常高效。面试中如果写出了 O(V²) 的解法，需要优化到 O(V + E)。

---

### 🎯 扩展：Course Schedule II（LeetCode 210）

如果不仅要判断能否完成，还要输出**具体的修课顺序**，就是拓扑排序的完整版本。

```python
# Kahn 算法，只需在 popped 时记录顺序
order = []
while queue:
    course = queue.popleft()
    order.append(course)
    # ... 同上
return order if len(order) == numCourses else []
```

DFS 版本也可以通过**后序遍历的逆序**得到拓扑序。

---

## 二、面试技巧

### 🤝 如何"从不会聊到思路"

这道题看起来是课程规划，但本质是图问题。

**推荐话术结构：**

> "看到课程先修关系，我意识到这是一个**有向图**问题：每门课程是节点，先修关系是边。问题变成了：这张图是否存在**环**？如果存在环，说明有循环依赖，无法完成所有课程。判断有向图是否有环，我想到**拓扑排序**..."

**面试官想听的：**
1. 你能把课程依赖抽象成有向图
2. 你知道有环 = 无法完成
3. 你会用拓扑排序或 DFS 三色标记检测环

---

### 🎯 常见追问 & 满分回答

| 追问 | 面试官想考察 | 回答要点 |
|------|-------------|---------|
| "拓扑排序除了 Kahn 算法，还有别的做法吗？" | 知识广度 | "DFS 后序遍历的逆序也是拓扑序。DFS 用三色标记（0=未访问，1=访问中，2=已访问）可以检测环。" |
| "时间复杂度为什么是 O(V+E)？" | 复杂度分析 | "建图遍历所有边 O(E)，每个节点和边在 BFS/DFS 中只被处理一次 O(V+E)，总体 O(V+E)。" |
| "如果要求输出所有可能的拓扑序呢？" | 进阶思考 | "那需要回溯，每次选择入度为 0 的节点中的一个，复杂度会上升到指数级。通常只要求一个拓扑序。" |
| "实际应用场景？" | 系统设计思维 | "任务调度、编译依赖（Makefile）、包管理器（npm 依赖解析）、数据库 schema 迁移顺序等。" |
| "DFS 和 BFS 版本选哪个？" | 算法选择 | "BFS 的 Kahn 算法更直观，能直接得到拓扑序；DFS 代码更短，检测环更自然。我通常写 Kahn 算法。" |

---

### 📝 代码书写技巧

1. **邻接表的构建方向别搞反**：`[a, b]` 表示 `b -> a`（先修 b 再修 a），不要写成 `graph[a].append(b)`
2. **入度数组要更新对**：移除边 `b -> a` 时，`in_degree[a]` 减 1，不是 `in_degree[b]`
3. **DFS 状态标记用 0/1/2 而不是 bool**：`True/False` 无法区分"正在访问"和"已访问"

---

### 💡 面试常犯的错误

| 错误 | 为什么错 | 怎么改 |
|------|---------|--------|
| 用无向图建图 | 先修关系是有方向的 | 明确 `[a, b]` 是 `b -> a` |
| DFS 只用 visited 数组（bool） | 无法检测回溯路径上的环 | 用三色标记：0/1/2 |
| BFS 忘记更新邻接节点的入度 | 入度永远减不到 0，队列提前为空 | 记得 `in_degree[next] -= 1` |
| 最后只检查 queue 是否为空 | 可能有多个连通分量 | 检查 `visited == numCourses` |

---

### 🔗 与其他题目的联系

| 题目 | 联系 |
|------|------|
| **Course Schedule II**（LeetCode 210） | 输出拓扑序的完整版本 |
| **外星词典**（LeetCode 269） | 通过字母顺序推导有向边，再拓扑排序 |
| **序列重建**（LeetCode 444） | 拓扑排序 + 验证序列唯一性 |
| **有向图检测环**（各种变形） | 三色标记 DFS 是通用模板 |
| **钥匙和房间**（LeetCode 841） | 无向图/有向图的连通性，搜索模板 |

---

## 三、今日学习打卡

✅ **算法题**：课程表（Course Schedule）— 拓扑排序 / 有向图环检测
✅ **核心技能**：Kahn 算法（BFS）、DFS 三色标记、入度管理、DAG 判断
✅ **面试技巧**：有向图抽象、拓扑排序应用场景、追问应答策略

**明日预告**：实现 Trie（前缀树）— 字符串搜索与自动补全数据结构

---

> 📝 **学习建议**：拓扑排序是图论中面试频率极高的考点。记住这个核心：**"有向无环图才能拓扑排序，存在环就无法排序"**。Kahn 算法（BFS）和 DFS 三色标记都要会写，面试时根据题目要求灵活选择。建议趁热打铁把 LeetCode 210（Course Schedule II）也刷了，完整理解拓扑序的输出。
