# Day 70 — 合并 K 个升序链表 + MySQL 索引原理

> 📅 2026-08-13 | Week 11 Day 1 | 主题：数据库与存储深度

---

## 1. 今日算法题：合并 K 个升序链表

### 题目描述

给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

**示例：**
```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
```

**进阶：** 能否在 `O(n log k)` 时间复杂度内完成？（n 为所有节点总数，k 为链表数）

---

### 思路分析

这题是**归并排序思想**在链表上的直接应用，也是数据库**多路归并（K-way Merge）**的经典模型。

面试时最常见的两种解法：

#### 方法一：最小堆（优先队列）
- 维护一个大小为 k 的最小堆，堆顶是当前 k 个链表中最小的节点
- 每次取出堆顶，将其 next 节点入堆
- 时间复杂度：`O(n log k)`，空间复杂度：`O(k)`

#### 方法二：分治合并（两两归并）
- 递归地将链表数组分成两半，分别合并，再将结果合并
- 本质是归并排序的 merge 过程
- 时间复杂度：`O(n log k)`，空间复杂度：`O(log k)`（递归栈）

**数据库联想：** MySQL 的排序缓冲区（sort_buffer）不足时，会触发**外部排序（External Sort）**——先分块排序生成有序 run，再用多路归并合并。这题就是多路归并的缩影。

---

### 代码实现（Go — 最小堆版）

```go
package main

import (
	"container/heap"
	"fmt"
)

type ListNode struct {
	Val  int
	Next *ListNode
}

// 最小堆定义
type MinHeap []*ListNode

func (h MinHeap) Len() int            { return len(h) }
func (h MinHeap) Less(i, j int) bool  { return h[i].Val < h[j].Val }
func (h MinHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *MinHeap) Push(x interface{}) { *h = append(*h, x.(*ListNode)) }
func (h *MinHeap) Pop() interface{} {
	old := *h
	n := len(old)
	node := old[n-1]
	*h = old[:n-1]
	return node
}

func mergeKLists(lists []*ListNode) *ListNode {
	// 虚拟头节点，简化边界处理
	dummy := &ListNode{}
	tail := dummy
	
	// 初始化最小堆：每个链表的头节点入堆
	minHeap := &MinHeap{}
	heap.Init(minHeap)
	
	for _, head := range lists {
		if head != nil {
			heap.Push(minHeap, head)
		}
	}
	
	// 每次取最小，将其后继入堆
	for minHeap.Len() > 0 {
		node := heap.Pop(minHeap).(*ListNode)
		tail.Next = node
		tail = tail.Next
		
		if node.Next != nil {
			heap.Push(minHeap, node.Next)
		}
	}
	
	return dummy.Next
}

// 辅助函数：构建链表
func buildList(vals []int) *ListNode {
	dummy := &ListNode{}
	tail := dummy
	for _, v := range vals {
		tail.Next = &ListNode{Val: v}
		tail = tail.Next
	}
	return dummy.Next
}

// 辅助函数：打印链表
func printList(head *ListNode) {
	for head != nil {
		fmt.Printf("%d ", head.Val)
		head = head.Next
	}
	fmt.Println()
}

func main() {
	lists := []*ListNode{
		buildList([]int{1, 4, 5}),
		buildList([]int{1, 3, 4}),
		buildList([]int{2, 6}),
	}
	
	result := mergeKLists(lists)
	printList(result) // 1 1 2 3 4 4 5 6
}
```

### 复杂度分析

| 指标 | 值 | 说明 |
|------|---|------|
| **时间** | `O(n log k)` | 每个节点入堆/出堆一次，堆操作 `O(log k)` |
| **空间** | `O(k)` | 堆中最多 k 个节点 |

**Follow-up 追问：**
- 💬 **如果 k 很大（百万级），内存不够怎么办？** → 分治归并，降低同时需要加载到内存的链表数
- 💬 **如果数据在磁盘上（外部排序场景）？** → 引入缓冲区，分批读取，归并时采用置换选择（Replacement Selection）减少 run 数

---

## 2. 面试技巧：MySQL 索引原理与查询优化

### 2.1 B+ 树索引结构

MySQL InnoDB 使用 **B+ 树**作为索引数据结构：

```
                    [10 | 30 | 50]
                   /     |      \
            [3|5|7]  [15|20|25]  [35|40|45]  [55|60]
              |          |           |          |
            叶子节点（双向链表连接，存储完整数据行）
```

**为什么选 B+ 树而不是 B 树？**
- ✅ B+ 树所有数据都在叶子节点，非叶子节点只存 key，更矮胖，IO 更少
- ✅ 叶子节点用双向链表连接，**范围查询**只需顺序遍历，无需回溯
- ✅ B 树数据分散在各层，范围查询需要中序遍历，磁盘随机 IO 多

**为什么不是哈希表？**
- 哈希表只支持等值查询，`O(1)` 很快，但不支持范围查询、排序、最左前缀匹配
- MySQL 8.0 支持自适应哈希索引（Adaptive Hash Index），作为 B+ 树的补充

---

### 2.2 聚簇索引 vs 非聚簇索引

| | **聚簇索引（Clustered）** | **非聚簇索引（Secondary）** |
|---|---|---|
| **定义** | 数据行和索引存储在一起 | 索引和数据分开存储 |
| **实现** | InnoDB 主键索引 | InnoDB 非主键索引 |
| **叶子节点** | 存完整行数据 | 存主键值（回表查询） |
| **数量** | 一张表只能有一个 | 可以有多个 |

**回表查询（Lookup）：**
```sql
-- name 有二级索引，但查询了 *（所有列）
SELECT * FROM user WHERE name = 'Alice';
-- 执行过程：先查二级索引找到主键 → 再用主键查聚簇索引拿整行数据
```

**覆盖索引（Covering Index）——避免回表：**
```sql
-- id 和 name 联合索引 (name, id)
SELECT id, name FROM user WHERE name = 'Alice';
-- 二级索引叶子节点已经有 name 和 id，无需回表 ✅
```

---

### 2.3 最左前缀原则

联合索引 `(a, b, c)` 的生效规则：

```sql
-- ✅ 走索引
WHERE a = 1
WHERE a = 1 AND b = 2
WHERE a = 1 AND b = 2 AND c = 3
WHERE a = 1 AND c = 3          -- a 走索引，c 不走（b 断了）

-- ❌ 不走索引
WHERE b = 2                    -- 缺少最左列 a
WHERE b = 2 AND c = 3          -- 缺少最左列 a
WHERE a > 1 AND b = 2          -- a 范围查询后，b 不走索引（范围断后）
```

**口诀：从左到右依次匹配，中间断了后面全断；范围查询后不再走。**

---

### 2.4 EXPLAIN 分析实战

```sql
EXPLAIN SELECT * FROM user WHERE name = 'Alice' AND age > 20;
```

关键字段解读：

| 字段 | 含义 | 面试重点 |
|------|------|---------|
| `type` | 访问类型 | **system > const > eq_ref > ref > range > index > ALL**，至少达到 `range`，避免 `ALL` |
| `key` | 实际使用的索引 | 为 `NULL` 说明没走索引 |
| `rows` | 扫描行数估算 | 越小越好 |
| `Extra` | 额外信息 | `Using index`（覆盖索引）、`Using where`（回表过滤）、`Using filesort`（需排序优化） |

**高频面试题速答：**

> 💬 **面试官：EXPLAIN 里 type=ALL 是什么意思？怎么优化？**
>
> 🎯 **你：** 表示全表扫描，最坏的情况。优化方向：
> 1. 加索引（优先加 WHERE 条件里的列）
> 2. 检查是否走了最左前缀
> 3. 数据量太小（< 几百行）时优化器可能故意选全表，不用强转索引
> 4. 用 `FORCE INDEX` 强制指定（不推荐，先分析原因）

> 💬 **面试官：索引下推（Index Condition Pushdown）是什么？**
>
> 🎯 **你：** MySQL 5.6 引入的优化。以前存储引擎把索引命中的行全部回表给 Server 层过滤；现在把 WHERE 条件下推到存储引擎，在索引遍历时就过滤，减少回表次数。比如索引 `(name, age)`，查询 `WHERE name LIKE 'A%' AND age = 20`，ICP 可以在索引层就过滤 age，不用把所有 A 开头的都回表。

---

### 2.5 索引设计 Checklist

```
✅ WHERE 条件里的列优先建索引
✅ 联合索引把区分度高的列放左边
✅ 避免冗余索引（如已有 (a,b)，不需要单独的 (a)）
✅ 长字符串用前缀索引（如 VARCHAR(255) 只索引前 10 个字符）
✅ 索引不是越多越好：写操作（INSERT/UPDATE/DELETE）需要维护索引，过多会拖慢写入
✅ 定期用 ANALYZE TABLE 更新统计信息，避免优化器选错执行计划
```

---

## 3. 今日总结

| 项目 | 内容 |
|------|------|
| **算法题** | 合并 K 个升序链表 — 最小堆 / 分治归并，时间 `O(n log k)` |
| **核心技巧** | 虚拟头节点简化链表操作；堆的 Push/Pop 套路 |
| **面试考点** | MySQL B+ 树索引结构、聚簇/非聚簇、最左前缀、覆盖索引、EXPLAIN 分析 |
| **高频追问** | 为什么选 B+ 树？回表怎么避免？索引下推是什么？ |

---

> 🎯 **明日预告**：MySQL 事务与锁 — ACID、隔离级别、MVCC、死锁排查
>
> 数据库是面试最高频的领域之一，这周每天一道题配一个数据库深度考点，刷完这周你对存储层的理解会上一个台阶。
