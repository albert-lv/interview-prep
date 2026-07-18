# Day 43 — 计算右侧小于当前元素的个数（Count of Smaller Numbers After Self）

> **Week 7 · 高级数据结构 — 树状数组进阶 + 线段树懒标记**
> 📅 2026-07-18 | ⏱️ 预计 35 分钟 | 🎯 面试高频 Hard

---

## 一、今日算法题

### 题目描述

**LeetCode 315.** 给你一个整数数组 `nums`，按要求返回一个新数组 `counts`。其中 `counts[i]` 表示在 `nums` 中位于 `i` 右侧且比 `nums[i]` 小的元素个数。

**示例：**
```
输入: nums = [5, 2, 6, 1]
输出: [2, 1, 1, 0]
解释:
- 5 右侧比它小的: 2, 1 → 2 个
- 2 右侧比它小的: 1 → 1 个
- 6 右侧比它小的: 1 → 1 个
- 1 右侧没有元素 → 0 个
```

---

### 核心思路：离散化 + 树状数组（Fenwick Tree）

这道题是**树状数组从「模板」到「实战」的分水岭**。

朴素做法是两两重循环比较，O(n²)，面试里说出这个只能拿「基础分」。最优解是 **O(n log n)**，用树状数组维护「已经遍历过的数字的频率分布」。

**关键洞察：**
- 从右往左遍历 `nums`。
- 对于 `nums[i]`，它右侧比它小的元素个数 = 树状数组中值域在 `(-∞, nums[i]-1]` 范围内的元素总数。
- 查完再把 `nums[i]` 插入树状数组（频率 +1），供左侧元素查询。

**但有个坑：** `nums` 里的值可能是负数，范围很大（比如 -10⁴ ~ 10⁴），不能直接当下标塞进树状数组。

**解决：离散化（Coordinate Compression）**
- 把 `nums` 所有值排序去重，映射到 `1 ~ k` 的连续整数。
- 树状数组只开 `k` 大小（k ≤ n），空间完美压缩。

> 面试加分话术：「这里需要离散化，因为值域不连续且可能包含负数，树状数组依赖下标索引。」

---

### 代码实现（Python）

```python
class BIT:
    """树状数组 — 维护频率前缀和"""
    def __init__(self, n):
        self.n = n
        self.tree = [0] * (n + 1)
    
    def add(self, i, delta=1):
        """在位置 i 增加 delta"""
        while i <= self.n:
            self.tree[i] += delta
            i += i & -i
    
    def query(self, i):
        """查询前缀和 [1, i]"""
        s = 0
        while i > 0:
            s += self.tree[i]
            i -= i & -i
        return s

class Solution:
    def countSmaller(self, nums: List[int]) -> List[int]:
        # 1. 离散化
        sorted_unique = sorted(set(nums))
        rank = {v: i + 1 for i, v in enumerate(sorted_unique)}  # 从 1 开始
        
        bit = BIT(len(sorted_unique))
        n = len(nums)
        res = [0] * n
        
        # 2. 从右向左遍历
        for i in range(n - 1, -1, -1):
            r = rank[nums[i]]
            # 查询：右侧比当前小的个数 = 前缀和 [1, r-1]
            res[i] = bit.query(r - 1)
            # 插入当前元素
            bit.add(r, 1)
        
        return res
```

---

### 复杂度分析

| 维度 | 复杂度 | 说明 |
|------|--------|------|
| 时间 | **O(n log n)** | 离散化 O(n log n) + 每个元素一次 add + 一次 query，各 O(log n) |
| 空间 | **O(n)** | 离散化数组 + 树状数组 |

---

### 扩展：归并排序解法

这道题还有一个经典解法是**归并排序变体**（O(n log n)），在归并过程中统计「左侧元素比右侧元素大」的逆序对数量。

- 优点：不需要离散化，空间是 O(n)，常数比树状数组小。
- 缺点：代码稍长，边界容易写错。

> **面试技巧：** 如果你时间够，可以**先说归并排序思路，再提树状数组**，展示你懂多种做法。如果时间紧，直接写树状数组更稳。

---

## 二、面试技巧

### 1. 线段树懒标记（Lazy Propagation）— 区间更新 + 区间查询

昨天的线段树只支持**单点更新 + 区间查询**。如果面试官追问：「那区间更新呢？」你就需要祭出 **懒标记**。

**核心思想：**
- 如果某个节点的区间 `[l, r]` 完全被更新区间覆盖，**不继续递归到叶子**，而是直接在该节点打上一个「待下发」的标记（lazy tag）。
- 只有当后续查询需要进入该节点的子区间时，才把标记「下推」（push down）到左右子节点。
- 保证每次操作仍然是 O(log n)。

**懒标记线段树模板（区间加法 + 区间求和）：**

```python
class SegmentTree:
    def __init__(self, nums):
        self.n = len(nums)
        self.tree = [0] * (4 * self.n)
        self.lazy = [0] * (4 * self.n)  # 懒标记
        self._build(nums, 0, 0, self.n - 1)
    
    def _build(self, nums, node, l, r):
        if l == r:
            self.tree[node] = nums[l]
            return
        mid = (l + r) // 2
        self._build(nums, node * 2 + 1, l, mid)
        self._build(nums, node * 2 + 2, mid + 1, r)
        self.tree[node] = self.tree[node * 2 + 1] + self.tree[node * 2 + 2]
    
    def _push_down(self, node, l, r):
        """将懒标记下推给子节点"""
        if self.lazy[node] != 0 and l != r:
            mid = (l + r) // 2
            left_len = mid - l + 1
            right_len = r - mid
            
            # 下推左子树
            self.lazy[node * 2 + 1] += self.lazy[node]
            self.tree[node * 2 + 1] += self.lazy[node] * left_len
            
            # 下推右子树
            self.lazy[node * 2 + 2] += self.lazy[node]
            self.tree[node * 2 + 2] += self.lazy[node] * right_len
            
            self.lazy[node] = 0  # 清除当前标记
    
    def range_update(self, ql, qr, val, node=0, l=0, r=None):
        """区间 [ql, qr] 内每个元素加 val"""
        if r is None: r = self.n - 1
        if ql <= l and r <= qr:
            self.lazy[node] += val
            self.tree[node] += val * (r - l + 1)
            return
        self._push_down(node, l, r)
        mid = (l + r) // 2
        if ql <= mid: self.range_update(ql, qr, val, node * 2 + 1, l, mid)
        if qr > mid:  self.range_update(ql, qr, val, node * 2 + 2, mid + 1, r)
        self.tree[node] = self.tree[node * 2 + 1] + self.tree[node * 2 + 2]
    
    def range_query(self, ql, qr, node=0, l=0, r=None):
        """查询区间 [ql, qr] 的和"""
        if r is None: r = self.n - 1
        if ql <= l and r <= qr:
            return self.tree[node]
        self._push_down(node, l, r)
        mid = (l + r) // 2
        res = 0
        if ql <= mid: res += self.range_query(ql, qr, node * 2 + 1, l, mid)
        if qr > mid:  res += self.range_query(ql, qr, node * 2 + 2, mid + 1, r)
        return res
```

> **背模板技巧：** 先写 `_push_down`，再写 `range_update` 和 `range_query`。记住「完全覆盖就打标，不覆盖才下推」的口诀。

---

### 2. 树状数组 vs 线段树：面试怎么选？

| 场景 | 推荐 | 原因 |
|------|------|------|
| 单点更新 + 区间查询 | 树状数组 | 代码短，常数小，不易出错 |
| 区间更新 + 单点查询 | 树状数组（差分思想）| 两个树状数组或差分即可 |
| 区间更新 + 区间查询 | 线段树（懒标记）| 树状数组需要维护两个，容易翻车 |
| 需要维护最值/乘积/GCD | 线段树 | 树状数组只支持可逆运算（加法）|
| 二维/动态开点 | 线段树 | 树状数组也能做二维，但线段树更灵活 |

> **面试一句话：**「如果操作是单点更新前缀和，用树状数组；如果涉及区间修改或需要维护非求和信息，用线段树。」

---

### 3. 高频面试追问

**Q1: 为什么树状数组不能直接用区间更新 + 区间查询？**
> A: 树状数组的本质是维护差分数组的前缀和。单点更新对应差分数组的区间加，区间查询对应差分数组的前缀和的前缀和。如果要同时支持「区间加」和「区间求和」，需要维护两个树状数组（一个维护差分，一个维护差分×下标），公式推导容易出错。面试时间紧，建议直接用线段树懒标记。

**Q2: 懒标记的标记下推时机？**
> A: 两个时机：(1) 当前节点的区间没有被完全覆盖，需要继续递归到子节点时；(2) 查询/更新路径经过当前节点时。记住口诀：**「完全覆盖就打标，部分覆盖就下推」**。

**Q3: 如果值域很大但 n 很小（比如 n=10⁵, 值域=10⁹），怎么处理？**
> A: 离散化。这是树状数组和线段树面试的「必考基本功」。把值映射到排名，保持相对顺序不变即可。

---

### 4. 今日答题话术模板

**开场（30 秒定调）：**
> 「这道题我想到两种做法。一种是归并排序，在 merge 过程中统计逆序对；另一种是从右往左遍历，用树状数组维护频率，每次查询前缀和。我选树状数组来写，代码更简洁。」

**讲到离散化时：**
> 「因为值域包含负数且范围很大，我先把所有值离散化到 1~k，这样树状数组的下标就是连续的正整数。」

**如果面试官追问线段树：**
> 「树状数组适合单点更新 + 前缀和查询。如果这道题改成『区间加减 + 区间求和』，我会用线段树加懒标记，这样每次操作仍然是 O(log n)。」

---

## 三、自测 & 打卡

- [ ] 能独立写出树状数组的 `add` 和 `query`（不翻模板）
- [ ] 能解释清楚「为什么需要离散化」和「离散化的具体步骤」
- [ ] 能口述懒标记的核心思想（完全覆盖 vs 部分覆盖的处理区别）
- [ ] 能对比树状数组和线段树的适用场景，面试时快速选择

---

> 💡 **明日预告：** Week 7 收尾 — 高级数据结构综合：LCA（最近公共祖先）+ 树链剖分 / 单调栈与单调队列在树上的应用

**今日算法：树状数组进阶 — 离散化 + 逆序对统计。** 你背会的不只是模板，是「值域压缩」这个思想。下周图论高级算法见 🤝
