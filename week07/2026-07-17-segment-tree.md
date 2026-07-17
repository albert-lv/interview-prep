# Day 42: 区域和检索 - 数组可修改（Range Sum Query - Mutable）

> **日期：** 2026-07-17  
> **主题：** 线段树（Segment Tree）  
> **难度：** 中等  
> **LeetCode:** [307. 区域和检索 - 数组可修改](https://leetcode.cn/problems/range-sum-query-mutable/)

---

## 今日算法题

### 题目描述

给定一个整数数组 `nums`，处理以下类型的查询：

1. **`update(int index, int val)`**：将 `nums[index]` 的值更新为 `val`。
2. **`sumRange(int left, int right)`**：返回数组 `nums` 中索引 `left` 和 `right`（包含）之间的元素和。

注意：会有大量查询操作，需要保证每次查询和更新的效率。

**示例：**

```
输入：
["NumArray", "sumRange", "update", "sumRange"]
[[[1, 3, 5]], [0, 2], [1, 2], [0, 2]]
输出：
[null, 9, null, 8]

解释：
NumArray numArray = new NumArray([1, 3, 5]);
numArray.sumRange(0, 2); // 返回 1 + 3 + 5 = 9
numArray.update(1, 2);   // nums = [1, 2, 5]
numArray.sumRange(0, 2); // 返回 1 + 2 + 5 = 8
```

---

### 思路分析

这道题看着很基础，但如果暴力解法——每次 `sumRange` 都遍历一遍，时间复杂度是 O(n)，在大量查询面前直接超时。两个核心操作的理想复杂度是：

- **单点更新**：O(log n)
- **区间查询**：O(log n)

能达到这个效率的利器，就是 **线段树（Segment Tree）**。

#### 线段树的核心思想

线段树是一种二叉树，每个节点代表一个区间。根节点代表整个数组 `[0, n-1]`，每个叶子节点代表单个元素 `[i, i]`，非叶子节点代表两个子区间的并集。

核心操作只有三个：

1. **建树（Build）**：递归地把数组划分成两半，每个节点存储对应区间的和。
2. **更新（Update）**：从根节点走到目标叶子节点，更新沿途所有节点的区间和。
3. **查询（Query）**：如果当前区间完全在查询范围内，直接返回；否则递归查询左右子树。

> 💡 一句口诀：**完全覆盖直接拿，部分覆盖往下钻，没有交集就不管。**

---

### 代码实现

```python
class NumArray:
    def __init__(self, nums: List[int]):
        self.n = len(nums)
        self.tree = [0] * (self.n * 4)  # 线段树数组，4倍空间足够
        self.nums = nums
        self._build(nums, 0, 0, self.n - 1)

    def _build(self, nums, node, left, right):
        """建树：构建线段树"""
        if left == right:
            self.tree[node] = nums[left]
            return
        mid = (left + right) // 2
        self._build(nums, node * 2 + 1, left, mid)       # 左子树
        self._build(nums, node * 2 + 2, mid + 1, right)   # 右子树
        self.tree[node] = self.tree[node * 2 + 1] + self.tree[node * 2 + 2]

    def update(self, index: int, val: int) -> None:
        """单点更新：将 index 位置的值改为 val"""
        self._update(0, 0, self.n - 1, index, val)

    def _update(self, node, left, right, index, val):
        if left == right:
            self.tree[node] = val
            return
        mid = (left + right) // 2
        if index <= mid:
            self._update(node * 2 + 1, left, mid, index, val)
        else:
            self._update(node * 2 + 2, mid + 1, right, index, val)
        self.tree[node] = self.tree[node * 2 + 1] + self.tree[node * 2 + 2]

    def sumRange(self, left: int, right: int) -> int:
        """区间查询：返回 [left, right] 的和"""
        return self._query(0, 0, self.n - 1, left, right)

    def _query(self, node, left, right, ql, qr):
        # 完全覆盖：当前区间完全在查询范围内
        if ql <= left and right <= qr:
            return self.tree[node]
        # 无交集：当前区间完全在查询范围外
        if right < ql or left > qr:
            return 0
        # 部分覆盖：需要递归查询子树
        mid = (left + right) // 2
        left_sum = self._query(node * 2 + 1, left, mid, ql, qr)
        right_sum = self._query(node * 2 + 2, mid + 1, right, ql, qr)
        return left_sum + right_sum
```

#### 树状数组（BIT / Fenwick Tree）的替代方案

如果只是想应付这道题，也可以用 **树状数组**。它占空间更小，代码更短，但通用性不如线段树（树状数组只能处理满足结合律和可差分的信息，比如求和；求最大值就不太行）。

```python
class NumArray:
    def __init__(self, nums: List[int]):
        self.n = len(nums)
        self.tree = [0] * (self.n + 1)
        self.nums = [0] * self.n
        for i, num in enumerate(nums):
            self.update(i, num)

    def _lowbit(self, x):
        return x & (-x)

    def update(self, index: int, val: int) -> None:
        diff = val - self.nums[index]
        self.nums[index] = val
        i = index + 1
        while i <= self.n:
            self.tree[i] += diff
            i += self._lowbit(i)

    def _query(self, index):
        """查询前缀和 [0, index]"""
        res = 0
        i = index + 1
        while i > 0:
            res += self.tree[i]
            i -= self._lowbit(i)
        return res

    def sumRange(self, left: int, right: int) -> int:
        return self._query(right) - self._query(left - 1)
```

---

### 复杂度分析

| 操作 | 时间复杂度 | 空间复杂度 |
|------|-----------|-----------|
| 建树 | O(n) | O(n) |
| 单点更新 | O(log n) | O(1) 额外 |
| 区间查询 | O(log n) | O(1) 额外 |
| 总体 | O(n) 建树 + O(q log n) 查询 | O(n) |

---

### 延伸思考

1. **如果数组是静态的，只查询不更新**，用前缀和数组更优，区间查询 O(1)，空间 O(n)。
2. **如果需要区间更新 + 单点查询**，树状数组或线段树配合懒标记可以处理。
3. **如果需要区间更新 + 区间查询**，需要 **带懒标记的线段树**（Lazy Propagation），这是面试进阶考点。
4. **线段树的应用场景**：不仅仅是求和——区间最大值、最小值、GCD、异或和等都可以，只要操作满足**结合律**即可。

---

## 面试技巧

### 1. 当面试官问你“这道题怎么优化”时

如果先给出暴力解法，不要不好意思。**先给出暴力解，然后分析瓶颈，再引导到优化方案**——这是面试中最标准的节奏。

> "暴力做法就是每次查询都遍历一遍，时间 O(n)。但如果查询次数很多，比如 q 次，总复杂度就是 O(nq)，这不行。这里每次操作其实是独立的，我想到用线段树来把单点更新和区间查询都降到 O(log n)。"

### 2. 线段树不会完整写？退一步用树状数组

如果在现场手抖写不完线段树，可以主动提树状数组：

> "这道题用线段树可以解决。不过如果面试官允许，树状数组代码更短，也能实现 O(log n) 的更新和查询。线段树的优势是更通用，比如求区间最大值时树状数组就不太方便。"

这展示了你的**知识广度和权衡能力**。

### 3. 画一棵树来讲解

面试中如果写代码卡住了，可以画一个简单的线段树结构来说明。比如数组 `[1, 3, 5]` 的线段树：

```
        [0-2] sum=9
       /          \
  [0-1] sum=4   [2-2] sum=5
  /      \
[0-0]=1  [1-1]=3
```

> "根节点存整个区间和，每次更新只需要从根走到对应叶子，沿途更新 O(log n) 个节点。查询也是同理，如果当前区间完全包含在查询范围内，直接返回，不用继续往下。"

### 4. 常见追问：懒标记（Lazy Propagation）

面试官可能会追问：**如果区间更新 + 区间查询怎么办？**

这时候就是懒标记上场了。核心思想：
- 当某个节点代表的区间**完全被覆盖**时，不继续往下更新，而是给这个节点打一个标记，记录"这个区间整体加了 X"。
- 下次需要访问这个节点的子节点时，再把这个标记"推下去"（push down）。

> "懒标记的好处是避免不必要的更新。如果某个大区间完全覆盖在更新范围内，我只需要更新这个节点的值，然后标记一下它的子节点待更新。这样复杂度仍然是 O(log n)。"

---

## 一句话总结

> 线段树不是那种天天手写的结构，但它的核心思想——**区间拆分、递归合并、延迟更新**——在面试中非常加分。能讲清楚、能写出来，说明你不仅懂数据结构，还懂怎么用它解决实际问题。这就是面试官想看到的。
