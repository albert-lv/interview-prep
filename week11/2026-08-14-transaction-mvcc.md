# Day 71 — 快照数组 + MySQL 事务与 MVCC

> 📅 2026-08-14 | Week 11 Day 2 | 主题：数据库与存储深度

---

## 1. 今日算法题：快照数组（Snapshot Array）

### 题目描述

实现一个 `SnapshotArray` 类，支持以下操作：

- `SnapshotArray(int length)`：初始化一个长度为 `length` 的数组，初始值均为 0。
- `void set(int index, int val)`：将数组 `index` 位置的值设为 `val`。
- `int snap()`：拍摄一次快照，返回快照 ID（从 0 开始递增）。
- `int get(int index, int snap_id)`：获取快照 ID 为 `snap_id` 时，`index` 位置的值。

**示例：**
```
SnapshotArray snapshotArr = new SnapshotArray(3); // 初始化 [0,0,0]
snapshotArr.set(0, 5);      // 设置 [5,0,0]
snapshotArr.snap();         // 拍摄快照 0，返回 0
snapshotArr.set(0, 3);      // 设置 [3,0,0]
snapshotArr.get(0, 0);      // 返回 5（快照 0 时 index=0 的值）
snapshotArr.snap();         // 拍摄快照 1，返回 1
snapshotArr.get(0, 1);      // 返回 3（快照 1 时 index=0 的值）
```

**进阶：** 能否将 `set`、`snap`、`get` 的时间复杂度都优化到 `O(log n)`？

---

### 思路分析

这道题的本质是 **MVCC（多版本并发控制）** 的简化模型：

- `set` = 写入新版本数据
- `snap` = 创建一个全局版本号（时间戳）
- `get` = 读取指定历史版本的快照数据

#### 核心思想：为每个位置维护一个「版本链」

不用每次都复制整个数组（那会是 `O(n)` 的 snap），而是：

1. **每个索引维护一个有序列表**，记录 `(snap_id, value)` 的变更历史
2. **`set` 时**：在当前位置追加一条变更记录（如果当前 snap_id 已有记录则覆盖）
3. **`snap` 时**：全局版本号 +1，返回旧版本号
4. **`get` 时**：在该位置的历史记录中二分查找不超过 `snap_id` 的最新版本

```
index 0: [(snap=-1, val=0), (snap=0, val=5), (snap=1, val=3)]
index 1: [(snap=-1, val=0)]
index 2: [(snap=-1, val=0)]

snap_id 全局计数器: 2（下一次 snap 返回 2）
```

**数据库联想：** 这就是 InnoDB 的 MVCC 机制缩影。每行数据维护 `DB_TRX_ID`（创建版本）和 `DB_ROLL_PTR`（回滚指针），`SELECT` 时根据 ReadView 判断可见性，无需加锁即可实现一致性非锁定读。

---

### 代码实现（Go）

```go
package main

import (
	"fmt"
	"sort"
)

// Change 记录一次变更
type Change struct {
	snapID int
	val    int
}

type SnapshotArray struct {
	data   [][]Change // 每个索引的版本链
	snapID int        // 全局快照计数器
}

func Constructor(length int) SnapshotArray {
	data := make([][]Change, length)
	for i := 0; i < length; i++ {
		// 初始值 0，snapID = -1 表示初始化状态
		data[i] = []Change{{-1, 0}}
	}
	return SnapshotArray{data: data, snapID: 0}
}

func (sa *SnapshotArray) Set(index int, val int) {
	// 如果当前 snapID 已经有一条记录，覆盖；否则追加
	changes := sa.data[index]
	last := changes[len(changes)-1]
	if last.snapID == sa.snapID {
		changes[len(changes)-1].val = val
	} else {
		sa.data[index] = append(changes, Change{sa.snapID, val})
	}
}

func (sa *SnapshotArray) Snap() int {
	id := sa.snapID
	sa.snapID++
	return id
}

func (sa *SnapshotArray) Get(index int, snapID int) int {
	changes := sa.data[index]
	// 二分查找不超过 snapID 的最新版本
	// changes 按 snapID 升序排列，找最后一个小于等于 snapID 的位置
	idx := sort.Search(len(changes), func(i int) bool {
		return changes[i].snapID > snapID
	})
	// idx 是第一个 > snapID 的位置，目标位置是 idx-1
	return changes[idx-1].val
}

func main() {
	sa := Constructor(3)
	sa.Set(0, 5)
	fmt.Println(sa.Snap())        // 0
	sa.Set(0, 3)
	fmt.Println(sa.Get(0, 0))     // 5
	fmt.Println(sa.Snap())        // 1
	fmt.Println(sa.Get(0, 1))     // 3

	// 更多测试
	sa.Set(1, 10)
	sa.Set(0, 7)
	fmt.Println(sa.Snap())        // 2
	fmt.Println(sa.Get(0, 0))     // 5
	fmt.Println(sa.Get(0, 1))     // 3
	fmt.Println(sa.Get(0, 2))     // 7
	fmt.Println(sa.Get(1, 2))     // 10
	fmt.Println(sa.Get(1, 0))     // 0
}
```

### 复杂度分析

| 操作 | 时间 | 空间 | 说明 |
|------|------|------|------|
| `Set` | `O(1)` 均摊 | `O(1)` | 均摊到每次 snap 上的变更数 |
| `Snap` | `O(1)` | `O(1)` | 仅递增计数器 |
| `Get` | `O(log M)` | `O(1)` | M 为该位置的变更次数，二分查找 |

**总空间：** `O(N + S)`，N 为数组长度，S 为总 `set` 次数（每次 `set` 最多产生一条新记录）。

**Follow-up 追问：**
- 💬 **如果 snap 次数很多（百万级），但 set 很少，空间会浪费吗？** → 不会，只有被 set 过的位置才会记录变更，未修改的位置始终只有初始值一条记录
- 💬 **如何实现真正的并发安全（多线程 set/get）？** → 需要加锁或使用原子操作；`snap` 可设计成全局原子计数器，`get` 不加锁利用 MVCC 的「快照读」特性
- 💬 **这题和数据库的 MVCC 有什么对应关系？** → `snap()` ≈ 创建 ReadView，`get()` ≈ 一致性非锁定读，版本链 ≈ undo log 链

---

## 2. 面试技巧：MySQL 事务与 MVCC

### 2.1 ACID 与实现机制

| 特性 | 含义 | InnoDB 实现 |
|------|------|------------|
| **A**tomicity | 原子性 | undo log（回滚日志），事务失败或回滚时用 undo log 恢复 |
| **C**onsistency | 一致性 | 约束检查 + 级联更新 + 触发器，由 AID 共同保证 |
| **I**solation | 隔离性 | 锁（Locking）+ MVCC（多版本并发控制） |
| **D**urability | 持久性 | redo log（重做日志）+ binlog，先写日志再刷盘（WAL） |

**redo log vs undo log vs binlog：**

```
事务执行：UPDATE user SET balance = 100 WHERE id = 1;

1. 写 undo log: "把 balance 从 200 改回 200"（用于回滚）
2. 修改内存中的数据页（buffer pool）
3. 写 redo log: "页号 X，偏移 Y，旧值 200，新值 100"（用于崩溃恢复）
4. COMMIT 时：redo log 刷盘（fsync），binlog 刷盘（组提交优化）
5. 后台线程异步将脏页刷回磁盘
```

| 日志 | 作用 | 写时机 | 格式 |
|------|------|--------|------|
| **undo log** | 回滚、MVCC 版本链 | 事务开始前 | 逻辑日志（反向 SQL） |
| **redo log** | 崩溃恢复（物理级别） | 事务执行中 | 物理日志（页偏移） |
| **binlog** | 主从复制、时间点恢复 | COMMIT 时 | 逻辑日志（SQL 语句/行变更） |

> 💡 **面试速答：** redo 保事务、undo 保回滚、binlog 保复制。两阶段提交（2PC）保证 redo 和 binlog 的一致性。

---

### 2.2 隔离级别与问题

| 隔离级别 | 脏读 | 不可重复读 | 幻读 | 实现方式 |
|---------|------|-----------|------|---------|
| READ UNCOMMITTED | ✅ 可能 | ✅ 可能 | ✅ 可能 | 不加锁，直接读最新 |
| READ COMMITTED | ❌ 不会 | ✅ 可能 | ✅ 可能 | MVCC：每次 SELECT 生成新 ReadView |
| REPEATABLE READ | ❌ 不会 | ❌ 不会 | ❌ 不会* | MVCC：事务开始时生成 ReadView，全程复用 |
| SERIALIZABLE | ❌ 不会 | ❌ 不会 | ❌ 不会 | 所有 SELECT 加共享锁 |

*MySQL InnoDB 的 RR 通过「间隙锁（Gap Lock）」+ MVCC 基本解决了幻读，但极端场景（如 `SELECT ... FOR UPDATE` 后另一个事务插入）仍可能触发。

**三种读取问题速记：**
- **脏读（Dirty Read）**：读到其他事务未提交的数据 → `READ COMMITTED` 解决
- **不可重复读（Non-repeatable Read）**：同一事务内两次读同一行，结果不同 → `REPEATABLE READ` 解决
- **幻读（Phantom Read）**：同一事务内两次查询，行数不同（多了或少了行）→ `SERIALIZABLE` 或 InnoDB 间隙锁解决

---

### 2.3 MVCC 核心：ReadView 与可见性判断

InnoDB 每行数据有两个隐藏列：
- `DB_TRX_ID`（6字节）：最后修改该记录的事务 ID
- `DB_ROLL_PTR`（7字节）：回滚指针，指向 undo log

**ReadView（读视图）** 包含：
- `creator_trx_id`：创建该 ReadView 的事务 ID
- `m_ids`：生成 ReadView 时，所有活跃（未提交）事务 ID 列表
- `min_trx_id`：`m_ids` 中的最小值
- `max_trx_id`：生成 ReadView 时，系统将要分配的下一个事务 ID

**可见性判断规则（事务 A 要读某行，该行的 `DB_TRX_ID` = `trx_id`）：**

```
1. 如果 trx_id == creator_trx_id → 可见（自己改的）
2. 如果 trx_id < min_trx_id → 可见（在 ReadView 创建前已提交）
3. 如果 trx_id >= max_trx_id → 不可见（在 ReadView 创建后开始的）
4. 如果 min_trx_id <= trx_id < max_trx_id：
   - trx_id 在 m_ids 中 → 不可见（未提交）
   - trx_id 不在 m_ids 中 → 可见（已提交）
```

不可见时，顺着 `DB_ROLL_PTR` 找 undo log 链上的历史版本，直到找到可见的版本。

**RC vs RR 的区别核心：**
- **RC（READ COMMITTED）**：每次 `SELECT` 都生成新的 ReadView → 能看到其他事务已提交的最新变更
- **RR（REPEATABLE READ）**：事务开始时生成一次 ReadView，之后复用 → 全程看到事务开始时的快照

---

### 2.4 锁机制全景图

```
InnoDB 锁
├── 行锁（Record Lock）
│   ├── 共享锁（S Lock）：SELECT ... LOCK IN SHARE MODE
│   └── 排他锁（X Lock）：INSERT/UPDATE/DELETE / SELECT ... FOR UPDATE
├── 间隙锁（Gap Lock）：锁定索引记录之间的间隙，防止幻读
├── 临键锁（Next-Key Lock）：行锁 + 间隙锁，InnoDB 默认加锁单位
└── 表锁
    ├── 意向共享锁（IS）
    └── 意向排他锁（IX）
```

**锁兼容性矩阵：**

| 请求锁 ↓ \ 已有锁 → | X | S | IX | IS |
|---|---|---|---|---|
| X | ❌ | ❌ | ❌ | ❌ |
| S | ❌ | ✅ | ❌ | ✅ |
| IX | ❌ | ❌ | ✅ | ✅ |
| IS | ❌ | ✅ | ✅ | ✅ |

**高频面试速答：**

> 💬 **面试官：InnoDB 的行锁是锁在索引上还是数据上？**
>
> 🎯 **你：** 锁在索引上。如果 WHERE 条件没有命中索引，会退化为表锁（实际上是把所有行的聚集索引都锁住）。所以加锁查询一定要走索引。

> 💬 **面试官：UPDATE 没走索引，会锁全表吗？**
>
> 🎯 **你：** 是的。InnoDB 的行锁是基于索引的，如果 UPDATE 的 WHERE 条件没有可用索引，扫描全表时会给每一行的聚集索引加 X 锁，等效于锁表。这是线上事故的常见原因。

> 💬 **面试官：RR 下 `SELECT` 不加锁能防止幻读吗？**
>
> 🎯 **你：** 普通 `SELECT`（快照读）靠 MVCC 防止幻读，因为 ReadView 在事务开始时生成，后续插入的数据 `trx_id` 不在可见范围内。但 `SELECT ... FOR UPDATE`（当前读）会加 Next-Key Lock，此时幻读仍可能发生——所以 InnoDB 用间隙锁来堵这个漏洞。

---

### 2.5 死锁排查与预防

**死锁产生的四个条件：**
1. 互斥（资源独占）
2. 请求并保持（拿到一个还想要另一个）
3. 不可剥夺（不能强制释放）
4. 循环等待（A 等 B，B 等 A）

**InnoDB 死锁检测与处理：**
- 自动检测：通过 `wait-for graph`（等待图）检测循环依赖
- 自动回滚：选择一个「代价最小」的事务回滚（undo 最少）
- 错误码：被回滚的事务收到 `ERROR 1213 (40001): Deadlock found`

**查看死锁日志：**
```sql
-- 查看最新死锁信息
SHOW ENGINE INNODB STATUS;
-- 或开启死锁日志记录
SET GLOBAL innodb_print_all_deadlocks = ON;
```

**死锁预防 Checklist：**
```
✅ 按固定顺序访问资源（如按主键 ID 升序加锁）
✅ 缩短事务长度，尽快提交/回滚
✅ 避免在事务中做用户交互、RPC 调用
✅ 为 WHERE 条件建立合适索引，减少锁粒度
✅ 低并发场景可用 SELECT ... FOR UPDATE NOWAIT 快速失败
✅ 高并发场景考虑降级为 READ COMMITTED + 应用层幂等控制
```

---

### 2.6 事务设计 Checklist

```
✅ 默认使用 REPEATABLE READ，除非业务明确需要读最新提交
✅ 事务内只包含必要的 SQL，不要夹杂业务逻辑/RPC
✅ 大事务拆小事务，避免长时间持有锁
✅ UPDATE/DELETE 必须带 WHERE 且走索引
✅ 高并发写入场景，考虑乐观锁（version 字段）替代悲观锁
✅ 监控：关注 `SHOW ENGINE INNODB STATUS` 中的锁等待和死锁信息
```

---

## 3. 今日总结

| 项目 | 内容 |
|------|------|
| **算法题** | 快照数组 — 版本链 + 二分查找，完美映射 MVCC 思想 |
| **核心技巧** | 不复制全量数据，用「变更历史 + 二分」实现 O(log n) 快照读 |
| **面试考点** | ACID 实现（undo/redo/binlog）、隔离级别与问题、MVCC ReadView、锁机制、死锁排查 |
| **高频追问** | RC vs RR 区别？MVCC 怎么判断可见性？UPDATE 没走索引会怎样？死锁怎么排查？ |

---

> 🎯 **明日预告**：SQL 优化与执行计划 — 慢查询诊断、索引优化实战、Explain 深度解读
>
> 事务是数据库面试的「必考题中的必考题」，今天的内容覆盖了 90% 的高频追问。建议把 MVCC 的可见性判断规则手推一遍，面试时会很稳。
