# Day 22: 参加考试的最大学生数（Max Students Taking Exam）

> 日期：2026-06-27  
> 周主题：Week 4 — 状态压缩 DP 与 DP 优化  
> 知识点：二维状态压缩 + 位运算 + 不相邻约束

---

## 一、今日算法题

### 题目描述（LeetCode 1349）

给你一个 `m × n` 的矩阵表示教室座位，其中：
- `'.'` 表示可坐的座位
- `'#'` 表示坏掉的座位（不能坐）

学生必须遵守以下规则：
1. **左右不能相邻**：一个学生不能坐在另一个学生的左边或右边（同一行）
2. **斜对角不能坐**：一个学生不能坐在另一个学生的左前方、右前方、左后方、右后方（即上下相邻行的对角线位置）

返回最多能坐多少个学生。

**示例 1：**
```
输入: seats = [["#",".","#","#",".","#"],
               [".","#","#","#","#","."],
               ["#",".","#","#",".","#"]]
输出: 4
解释: 可以让 4 个学生参加考试，图中 '#' 表示坏椅子，'.' 表示好椅子。
```

**约束条件：**
- `seats.length == m`
- `seats[i].length == n`
- `1 <= m <= 8`
- `1 <= n <= 8`

---

### 思路分析

#### 🔑 关键观察 1：范围小到可以用状态压缩
`m, n <= 8`，这意味着每一行最多 8 个座位，可以用一个 **8 位二进制数** 表示某一行的就座状态。

- 第 `j` 位为 `1` → 第 `j` 列坐了学生
- 第 `j` 位为 `0` → 第 `j` 列没坐学生

#### 🔑 关键观察 2：三个约束的位运算表达
这道题有三个约束，都可以 elegant 地用位运算表达：

**约束 A：同一行不能左右相邻**  
如果状态 `s` 有相邻的 1，非法。判断方法：
```python
if s & (s << 1):  # 存在相邻的 1
    非法
```

**约束 B：不能坐在坏座位上**  
每一行有些座位是 `'#'`，用一个 bitmask `broken` 表示（`1` 表示坏座位）。状态 `s` 必须满足：
```python
if s & broken:  # 坐了坏座位
    非法
```

**约束 C：上下行不能斜对角**  
上一行状态 `prev_s` 和当前行状态 `s` 不能在对角线位置同时有学生：
```python
if (prev_s << 1) & s:  # 右对角冲突
    非法
if (prev_s >> 1) & s:  # 左对角冲突
    非法
```

#### 🔑 关键观察 3：二维状压的 DP 框架
定义 `dp[i][s]` = 第 `i` 行状态为 `s` 时，前 `i` 行最多能坐多少学生。

转移方程：
```
dp[i][s] = max(dp[i-1][prev_s] + count(s))  
           对所有合法的 prev_s 和 s 的组合
```

其中 `count(s)` 是 `s` 中 `1` 的个数（二进制中可用 `bin(s).count('1')`）。

---

### 代码实现（Python）

```python
class Solution:
    def maxStudents(self, seats: List[List[str]]) -> int:
        m, n = len(seats), len(seats[0])
        
        # 预处理每一行的坏座位 bitmask
        # broken[i]: 第 i 行的坏座位 mask（1 表示坏座位）
        broken = []
        for i in range(m):
            mask = 0
            for j in range(n):
                if seats[i][j] == '#':
                    mask |= (1 << j)
            broken.append(mask)
        
        # 预处理所有"单行合法"的状态
        # 即：没有相邻的 1，且没有坐在坏座位上
        valid_states = []
        for s in range(1 << n):
            # 约束 A：没有左右相邻
            if s & (s << 1):
                continue
            valid_states.append(s)
        
        # dp[s] 表示上一行状态为 s 时的最大学生数
        # 初始化：第 0 行
        dp = {}
        for s in valid_states:
            # 约束 B：不能坐坏座位
            if s & broken[0]:
                continue
            dp[s] = bin(s).count('1')
        
        # 逐行转移
        for i in range(1, m):
            new_dp = {}
            for s in valid_states:
                # 约束 B：当前行不能坐坏座位
                if s & broken[i]:
                    continue
                
                cnt = bin(s).count('1')
                best = 0
                
                for prev_s, prev_val in dp.items():
                    # 约束 C：上下行不能斜对角
                    if (prev_s << 1) & s:
                        continue
                    if (prev_s >> 1) & s:
                        continue
                    
                    best = max(best, prev_val + cnt)
                
                if best > 0 or cnt == 0:  # cnt==0 表示这一行不坐人，也是合法状态
                    new_dp[s] = best
            
            dp = new_dp
        
        return max(dp.values()) if dp else 0
```

#### 空间优化版本（滚动数组）

```python
class Solution:
    def maxStudents(self, seats: List[List[str]]) -> int:
        m, n = len(seats), len(seats[0])
        
        broken = []
        for i in range(m):
            mask = 0
            for j in range(n):
                if seats[i][j] == '#':
                    mask |= (1 << j)
            broken.append(mask)
        
        # 预计算每个状态的学生数
        popcount = [bin(s).count('1') for s in range(1 << n)]
        
        # 预筛选合法状态（只考虑单行约束）
        valid = [s for s in range(1 << n) if not (s & (s << 1))]
        
        # dp[prev_state] = 前 i-1 行的最大学生数
        dp = {s: popcount[s] for s in valid if not (s & broken[0])}
        
        for i in range(1, m):
            new_dp = {}
            for s in valid:
                if s & broken[i]:
                    continue
                
                cnt = popcount[s]
                # 枚举所有合法的上一行状态
                max_prev = 0
                for prev_s, prev_val in dp.items():
                    if not ((prev_s << 1) & s) and not ((prev_s >> 1) & s):
                        max_prev = max(max_prev, prev_val)
                
                new_dp[s] = max_prev + cnt
            
            dp = new_dp
        
        return max(dp.values()) if dp else 0
```

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|------|--------|------|
| **时间** | O(m × 3^n × 2^n) → 实际 O(m × S²) | S = 合法状态数 ≈ Fib(n+2)，n=8 时 S ≈ 55 |
| **空间** | O(S) | 滚动数组优化后只保留两行 |

> 💡 当 n = 8 时，合法状态数约为 55（斐波那契数列），所以实际复杂度约为 O(m × 55²) ≈ O(m × 3000)，非常轻松。

---

### 举一反三

| 题目 | 类型 | 关联点 |
|------|------|--------|
| [1931. 用三种不同颜色为网格涂色](https://leetcode.cn/problems/painting-a-grid-with-three-different-colors/) | 二维状压 + 相邻约束 | 每行状态扩展为颜色组合，约束更复杂 |
| [2386. 找出数组的第 K 大和](https://leetcode.cn/problems/find-the-k-sum-of-an-array/) | 堆 + 子集 | 涉及子集枚举的另一种技巧 |
| [1411. 给 N x 3 网格图涂色的方案数](https://leetcode.cn/problems/number-of-ways-to-paint-n-3-grid/) | 状压 + 计数 | 只有 3 列，状态数极少，经典入门 |

---

## 二、面试技巧

### 🎯 1. 如何自然引出「二维状压」

这道题比一维状压多了一层「空间维度」，面试中你可以这样展开：

> *"我注意到行数 m 最多只有 8，列数 n 也是最多 8。这意味着每一行的状态可以用一个很短的二进制数表示——最多 8 位。然后我对每一行枚举所有可能的就座状态，检查行间约束，用 DP 逐行转移。"*

**关键递进：**
1. **先讲约束** → "学生不能左右坐，也不能斜对角坐"
2. **再讲状态设计** → "每一行就座情况就是一个 bitmask"
3. **最后讲转移** → "当前行只和上一行有关，所以是 O(m × states²) 的 DP"

---

### 🎯 2. 位运算的「三板斧」

这道题用到了位运算的三个经典技巧，面试中主动提及会显得很专业：

| 技巧 | 表达式 | 本题用途 |
|------|--------|----------|
| **相邻检测** | `s & (s << 1)` | 检测是否有相邻的 1 |
| **不相交检测** | `a & b == 0` | 检查两个集合是否无交集 |
| **偏移检测** | `(a << 1) & b` 或 `(a >> 1) & b` | 检测斜对角冲突 |

**话术示例：**
> *"判断两个状态是否斜对角冲突，本质上是检查上一行的学生是否'偏移一位'后落在当前行学生的位置。位运算的左移右移正好对应这个几何关系。"*

---

### 🎯 3. 预处理优化：展示工程思维

代码中做了两个预处理，面试中可以主动提到：

1. **预筛选合法状态**：不是所有 `2^n` 个状态都合法，先筛掉有相邻 1 的状态，减少后续枚举
2. **预计算 popcount**：`bin(s).count('1')` 每次都算很慢，可以预先算好所有状态的 1 的个数

**话术：**
> *"我先做一次预处理，把满足'单行合法'的状态全部筛出来。对于 n=8，合法状态其实只有大约 55 个，远少于 256 个。我还预计算了每个状态的 popcount，避免在 DP 循环里反复算。"*

---

### 🎯 4. 常见 Follow-up 及应对

**Q1：如果 n 达到 20 还能用状压吗？**  
→ 2^20 状态数太大，需要换思路。可以提出：
- **轮廓线 DP（Plug DP）**：更高级的状压技巧
- **网络流 / 二分图匹配**：这道题本质是求二分图最大独立集！

> 💡 彩蛋：这道题其实等价于求一个二分图的最大独立集。把座位看成图的节点，冲突的座位连边，答案就是最大独立集。可以用匈牙利算法或网络流解决，复杂度与 n 无关，只与冲突边数有关。

**Q2：如何输出具体的就座方案？**  
→ DP 过程中记录前驱状态，最后回溯重建路径。

```python
# 伪代码
parent = {}  # (i, s) -> (prev_s)
dp[i][s] = max(...) 时记录 parent[(i, s)] = best_prev_s

# 最后回溯
i, s = m-1, best_final_state
while i >= 0:
    output 第 i 行就座方案 = s
    s = parent[(i, s)]
    i -= 1
```

**Q3：如果座位是三维的呢（多层教室）？**  
→ 状态维度增加，每行的状态还是 `n` 位，但行间约束还要考虑「层间」。如果层数也很小，可以继续状压；否则需要换思路。

---

### 🎯 5. 二维状压的「通用模板」

这类问题的套路非常固定：

```python
# 1. 确定每行的状态表示（bitmask）
# 2. 预处理单行合法状态
valid = [s for s in range(1<<n) if 满足单行约束(s)]

# 3. 初始化第一行
dp = {s: value(s) for s in valid if 满足第一行额外约束(s)}

# 4. 逐行转移
for i in range(1, m):
    new_dp = {}
    for s in valid:  # 当前行状态
        if not 满足第i行约束(s): continue
        best = 0
        for prev_s, prev_val in dp.items():  # 上一行状态
            if 满足行间约束(prev_s, s):
                best = max(best, prev_val + value(s))
        new_dp[s] = best
    dp = new_dp

return max(dp.values())
```

**面试中说：**
> *"二维状压的核心思路是把'行'当作 DP 的阶段，每一行的状态是一个 bitmask，然后行间约束用位运算快速判断。这个框架可以套在很多网格类问题上。"*

---

## 三、今日总结

- **核心题型**：二维状态压缩 DP
- **关键技巧**：位运算表达相邻/不相交约束、预筛选合法状态、滚动数组优化
- **面试价值**：⭐⭐⭐⭐⭐（非常经典的状压面试题，Google、Meta 都考过类似题）
- **易错点**：
  - `s << 1` 和 `s >> 1` 搞混方向
  - 忘记预处理坏座位约束
  - 边界情况（第一行没有上一行、最后一行不需要向后检查）

> **一句话口诀**：*行是阶段列是位，相邻约束位运算；单行合法先筛选，行间冲突再判断。*

> 💡 **隐藏考点**：这道题等价于二分图最大独立集。如果你能把这个转化讲出来，面试官会眼前一亮。
