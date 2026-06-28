# Day 23: 接雨水（Trapping Rain Water）

> 日期：2026-06-28  
> 周主题：Week 4 — 状态压缩 DP 与 DP 优化  
> 知识点：双指针 / 单调栈 / 预计算优化（DP 思想）

---

## 一、今日算法题

### 题目描述（LeetCode 42）

给定 `n` 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

**示例 1：**
```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。
```

**示例 2：**
```
输入：height = [4,2,0,3,2,5]
输出：9
```

**约束条件：**
- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`

---

### 思路分析

这道题是面试中的**超高频经典题**，其精髓在于理解"接雨水的条件"：

> **位置 `i` 能接的雨水 = min(左边最高柱子, 右边最高柱子) - height[i]**
> 只有当 `min(left_max, right_max) > height[i]` 时，才能接到雨水。

从暴力解法出发，逐步优化到双指针，完美展示"面试解题思维"：

#### 方法一：暴力法（O(n²)）—— 最直观的起点
对每个位置，向左扫描找最大值，向右扫描找最大值，然后计算。

```python
def trap_brute(height):
    n = len(height)
    ans = 0
    for i in range(n):
        left_max = max(height[:i+1])   # 左边最高（含自己）
        right_max = max(height[i:])      # 右边最高（含自己）
        ans += min(left_max, right_max) - height[i]
    return ans
```

**问题**：每次都要重新扫描，太慢。

---

#### 方法二：预计算优化（O(n) 时间，O(n) 空间）—— DP 思想

既然每个位置都要知道"左边最高"和"右边最高"，我们可以**预先计算**存起来：

- `left_max[i]`：从 0 到 i 的最高柱子
- `right_max[i]`：从 i 到 n-1 的最高柱子

这就是**DP 预计算**的思想：
```
left_max[i] = max(left_max[i-1], height[i])
right_max[i] = max(right_max[i+1], height[i])
```

```python
def trap_dp(height):
    n = len(height)
    if n == 0: return 0
    
    left_max = [0] * n
    right_max = [0] * n
    
    left_max[0] = height[0]
    for i in range(1, n):
        left_max[i] = max(left_max[i-1], height[i])
    
    right_max[n-1] = height[n-1]
    for i in range(n-2, -1, -1):
        right_max[i] = max(right_max[i+1], height[i])
    
    ans = 0
    for i in range(n):
        ans += min(left_max[i], right_max[i]) - height[i]
    
    return ans
```

**分析**：
- 时间：O(n)，三次线性扫描
- 空间：O(n)，两个辅助数组

这是面试中**最安全、最容易讲清楚**的解法。先写这个，确保正确，再谈优化。

---

#### 方法三：单调栈（O(n) 时间，O(n) 空间）—— 空间换思路

用单调递减栈，遇到更高的柱子时，弹出栈顶，计算凹槽中的雨水。

```python
def trap_stack(height):
    stack = []
    ans = 0
    for i, h in enumerate(height):
        while stack and height[stack[-1]] < h:
            top = stack.pop()
            if not stack: break
            # stack[-1] 是左边界，i 是右边界，top 是凹槽底部
            width = i - stack[-1] - 1
            bounded_height = min(height[stack[-1]], h) - height[top]
            ans += width * bounded_height
        stack.append(i)
    return ans
```

**关键点**：栈中存的是**下标**，维护的是**递减的高度**。当遇到更高的柱子，说明找到了右边界，可以计算雨水。

**什么时候用这个？** 如果面试官追问"能不能不用两个数组"，这就是进阶答案。

---

#### 方法四：双指针（O(n) 时间，O(1) 空间）—— 最优解

核心观察：
> 位置 `i` 的雨水量由 **min(left_max, right_max)** 决定。如果 `left_max < right_max`，那么 `left_max` 就是瓶颈，右边哪怕更高也不影响。

所以用两个指针，实时维护 `left_max` 和 `right_max`，向中间移动：

```python
def trap_two_pointers(height):
    left, right = 0, len(height) - 1
    left_max, right_max = 0, 0
    ans = 0
    
    while left < right:
        if height[left] < height[right]:
            # 左边是瓶颈，处理左边
            if height[left] >= left_max:
                left_max = height[left]
            else:
                ans += left_max - height[left]
            left += 1
        else:
            # 右边是瓶颈，处理右边
            if height[right] >= right_max:
                right_max = height[right]
            else:
                ans += right_max - height[right]
            right -= 1
    
    return ans
```

**为什么这样是对的？**
- 当 `height[left] < height[right]`，`left_max` 一定是 `left` 位置的真实左边界最大值（因为 right 那边有更高的保证）。
- 所以可以放心用 `left_max` 计算 `left` 位置的雨水。
- 右边同理。

---

### 复杂度对比

| 方法 | 时间 | 空间 | 面试定位 |
|------|------|------|----------|
| 暴力 | O(n²) | O(1) | 快速展示思路，但别写代码 |
| 预计算 | O(n) | O(n) | **首推面试写法**，清晰、安全、好讲 |
| 单调栈 | O(n) | O(n) | 进阶展示，体现栈的掌握 |
| 双指针 | O(n) | O(1) | 最优解，展示空间优化能力 |

---

## 二、面试技巧

### 🎯 技巧 1：面试中的"解题表演学"

接雨水这道题，**不要一上来就写双指针**。

正确的话术节奏：
1. **先确认理解**："位置 i 能接多少水，取决于左边最高和右边最高，取 min 减去当前高度"
2. **先写暴力**："最直观的是对每个位置，左右都扫一遍找最大值，O(n²)"
3. **指出瓶颈**："但这样重复计算了，其实每个位置的 left_max 和 right_max 可以预先算出来"
4. **给出预计算**："用两次线性扫描，先把 left_max 和 right_max 数组求出来，再算一遍，O(n) 时间 O(n) 空间"
5. **（可选）进阶优化**："如果面试官要求优化空间，可以用双指针，观察到瓶颈是 min(left_max, right_max) 的较小者..."

**为什么这样？** 面试官要看你的**思维过程**，不是要你秀最优解。从暴力到优化，体现你的分析能力和优化意识。

---

### 🎯 技巧 2：DP 优化的通用思维框架

从接雨水这道题，可以提炼出**DP 优化的三段论**：

```
原始 DP  →  发现瓶颈  →  针对性优化
 ↓           ↓            ↓
状态定义    重复计算？     预计算/滚动数组/单调结构
状态转移    单调性？       二分查找/双指针
           可维护性？     单调栈/单调队列
```

| 优化类型 | 适用场景 | 典型工具 |
|----------|----------|----------|
| **空间优化** | 只需要前一/几行状态 | 滚动数组、降维 |
| **预处理优化** | 重复查询某范围最值 | 前缀最值、ST表、线段树 |
| **单调栈优化** | 找前一个更大/更小元素 | 单调递减/递增栈 |
| **单调队列优化** | 滑动窗口最值 | 双端队列 |
| **二分优化** | DP 值具有单调性 | 二分查找、树状数组 |

接雨水从 O(n²) → O(n) 空间 → O(1) 空间，就是**预计算 → 双指针**的典型路径。

---

### 🎯 技巧 3：单调栈的"万能口诀"

面试中遇到"找前一个更大/更小的元素"，立刻想单调栈：

```
"遇到更高的，弹出算面积；遇到更低的，入栈等机会。"
```

- 接雨水：遇到更高的，弹出栈顶，计算凹槽
- 柱状图最大矩形：遇到更低的，弹出栈顶，计算矩形
- 每日温度：遇到更高的，弹出栈顶，记录距离

这三道题是**单调栈的面试三板斧**，建议一起准备。

---

### 🎯 技巧 4：双指针的"移动策略"

双指针不是无脑向中间移动，而是有**决策逻辑**：

```python
# 接雨水的核心决策：哪边矮，哪边就是瓶颈，处理哪边
if height[left] < height[right]:
    # 左边矮 → 左边接水量由 left_max 决定（右边更高，有保障）
    process(left)
    left += 1
else:
    # 右边矮或相等 → 处理右边
    process(right)
    right -= 1
```

**面试时可以这样解释**："两边向中间夹，矮的那边先处理，因为矮边是瓶颈，高边再高也溢不出去。"

---

### 🎯 技巧 5：调试与验证

写完代码后，用示例手动 trace：

```
height = [0,1,0,2,1,0,1,3,2,1,2,1]

index:  0 1 2 3 4 5 6 7 8 9 10 11
height: 0 1 0 2 1 0 1 3 2 1 2  1
left:   0 ←           
right:              → 11

left=0, right=11: height[0]=0 < height[11]=1, 处理左边
  left_max=0, height[0]=0, 不加水, left=1

left=1, right=11: height[1]=1 < height[11]=1, 左边<=右边, 处理左边
  left_max=1, height[1]=1, 不加水, left=2

left=2, right=11: height[2]=0 < height[11]=1, 处理左边
  left_max=1, height[2]=0, 加水 1-0=1, left=3
  ...
```

面试时现场 trace 一两个位置，展示你对代码的掌控力。

---

## 三、今日总结

- ✅ 理解接雨水的核心公式：`min(left_max, right_max) - height[i]`
- ✅ 掌握预计算方法（最推荐的面试安全写法）
- ✅ 了解单调栈解法（展示数据结构功底）
- ✅ 理解双指针最优解（展示空间优化能力）
- ✅ 掌握"从暴力到优化"的面试叙述节奏

**明日预告**：Day 24 — 柱状图中最大的矩形（Monotonic Stack 经典进阶）

---

*今日寄语：*  
> "好的面试不是背诵最优解，而是展示你如何从问题出发，一步步走到答案。接雨水这道题，过程比结果更重要。"
