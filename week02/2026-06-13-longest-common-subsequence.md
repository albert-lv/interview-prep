# Day 8: 最长公共子序列（Longest Common Subsequence）

> 📅 2026-06-13 | 第 2 周：线性 DP 进阶

---

## 今日算法题

### 题目描述

给定两个字符串 `text1` 和 `text2`，返回它们的最长公共子序列的长度。

**子序列**：不改变剩余字符相对顺序的情况下，删除某些字符后形成的新字符串。

**示例：**
```
输入: text1 = "abcde", text2 = "ace"
输出: 3
解释: 最长公共子序列是 "ace"，长度为 3。

输入: text1 = "abc", text2 = "def"
输出: 0
```

---

### 解题思路

**核心观察**：这题和编辑距离很像，都是双字符串线性 DP。

设 `dp[i][j]` 表示 `text1[0..i-1]` 和 `text2[0..j-1]` 的最长公共子序列长度。

**状态转移：**

```
if text1[i-1] == text2[j-1]:
    dp[i][j] = dp[i-1][j-1] + 1      # 字符匹配，LCS + 1
else:
    dp[i][j] = max(dp[i-1][j], dp[i][j-1])  # 不匹配，取两边最优
```

**边界：**`dp[0][j] = dp[i][0] = 0`

> 和编辑距离的对比：编辑距离是 "怎么改"，LCS 是 "找相同"。两者表格推导方式几乎一样，只是转移方程不同。

---

### 代码实现

#### 二维 DP（面试推荐写法）

```python
def longestCommonSubsequence(text1: str, text2: str) -> int:
    m, n = len(text1), len(text2)
    # dp[i][j]: text1[0..i-1] 和 text2[0..j-1] 的 LCS
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    return dp[m][n]
```

#### 空间压缩（一维）

```python
def longestCommonSubsequence(text1: str, text2: str) -> int:
    m, n = len(text1), len(text2)
    # 确保 text2 更短，节省空间
    if m < n:
        text1, text2 = text2, text1
        m, n = n, m
    
    prev = [0] * (n + 1)
    
    for i in range(1, m + 1):
        curr = [0] * (n + 1)
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                curr[j] = prev[j - 1] + 1
            else:
                curr[j] = max(prev[j], curr[j - 1])
        prev = curr
    
    return prev[n]
```

---

### 复杂度分析

| 维度 | 复杂度 | 说明 |
|------|--------|------|
| 时间 | O(m × n) | 两重循环遍历两个字符串 |
| 空间 | O(m × n) → O(min(m,n)) | 二维可压缩到一维 |

---

### 扩展：如何还原 LCS 内容？

```python
def getLCS(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # 先填表
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    # 倒推还原 LCS
    lcs = []
    i, j = m, n
    while i > 0 and j > 0:
        if text1[i-1] == text2[j-1]:
            lcs.append(text1[i-1])
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            i -= 1
        else:
            j -= 1
    
    return ''.join(reversed(lcs))
```

> 还原时从右下角往左上角走，匹配就收录字符，不匹配就往值大的方向走。

---

### 经典变体

| 变体 | 核心变化 |
|------|---------|
| **最长重复子数组** | 子序列 → 子数组，要求连续，`dp[i][j] = dp[i-1][j-1] + 1` 仅在连续匹配时累加 |
| **最长回文子序列** | 字符串和它的反转求 LCS |
| **删除两字符串使相等的最小步数** | `m + n - 2 * LCS` |
| **最短公共超序列** | `m + n - LCS` |

---

## 面试技巧

### 🎯 如何向面试官解释 LCS

> "LCS 的核心思想是：逐个字符对比，匹配就继承前面的结果 +1，不匹配就取左边或上边的最优值。"

推荐画这个 3×3 的小表格，10 秒讲清楚：

```
      a   c   e
   0  0   0   0
a  0  1   1   1
b  0  1   1   1
c  0  1   2   2
```

### 🎯 高频追问应对

**Q1: 为什么 `text1[i-1] == text2[j-1]` 时取 `dp[i-1][j-1] + 1`，不是 `dp[i-1][j] + 1`？**
> "因为当前两个字符都用了，它们前面的部分都不能再用了，所以看的是 `i-1, j-1` 的状态。"

**Q2: 空间能优化到 O(min(m,n)) 吗？**
> "可以。LCS 的状态只依赖上一行和当前行，用一个滚动数组就够了。如果还想要 O(1) 额外空间，可以用 Hirschberg 算法，但面试一般不要求。"

**Q3: 怎么求具体的最长公共子序列，而不只是长度？**
> "填完 DP 表后从右下角倒推，遇到相等的字符就加入结果，往值大的方向走。"

**Q4: LCS 和编辑距离有关系吗？**
> "有关系。最长公共子序列是删除操作的最小代价，而编辑距离还包含替换和插入。两者的状态定义和递推思路非常像，区别只是转移方程。"

### 🎯 常见踩坑

- ❌ 忘记初始化第一行/第一列为 0
- ❌ 二维数组开成了 `(m, n)` 而不是 `(m+1, n+1)`
- ❌ 倒推还原 LCS 时方向搞混（记住：匹配时走左上，不匹配时走值大的方向）
- ❌ 把子序列和子数组搞混（子数组要求连续，子序列不要求）

---

## 今日记忆点

> 🔑 **匹配 +1，不匹配取 max** —— 这就是 LCS 的全部。
>
> 它和编辑距离是同一类双串 DP，学会一个，另一个自然就会了。

---

**明日预告：** Day 9 —— 最长递增子序列的扩展（LIS + 二分优化）
