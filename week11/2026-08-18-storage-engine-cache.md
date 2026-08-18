# Day 75 — 常数时间插入删除获取随机元素 + 存储引擎与缓存一致性

> 📅 2026-08-18 | Week 11 Day 75（Week 11 完结 🎉）| 主题：数据库与存储深度 🔥

---

## 🎯 今日算法题：常数时间插入、删除和获取随机元素（Insert Delete GetRandom O(1)）

### 题目描述

实现 `RandomizedSet` 类：

- `insert(val)` — 向集合中插入项 `val`，若已存在返回 `false`，否则插入并返回 `true`
- `remove(val)` — 从集合中移除项 `val`，若不存在返回 `false`，否则移除并返回 `true`
- `getRandom()` — 随机返回集合中的一个元素，每个元素被返回的概率必须相同

**要求**：`insert`、`remove` 和 `getRandom` 操作的平均时间复杂度均为 **O(1)**。

> 💡 LeetCode 380，面试极高频。表面是数据结构题，本质是考察「如何在两种数据结构之间做 O(1) 的映射与删除」。

---

### 解题思路

#### 核心矛盾

| 数据结构 | 插入 | 删除 | 随机访问 |
|---------|------|------|---------|
| 哈希表 (HashMap) | O(1) | O(1) | ❌ 不支持按索引随机取 |
| 动态数组 (ArrayList) | O(1) 均摊 | O(n) — 需搬移元素 | O(1) |

**解法：两者结合，取长补短**

- **HashMap** 存储 `val → index` 的映射，保证 O(1) 查找
- **ArrayList** 存储实际元素，支持 O(1) 按索引随机访问
- **删除技巧**：将要删的元素与数组最后一个元素交换，然后 `pop_back()`，这样删除就是 O(1)

> 🔑 这个「交换到末尾再删除」的技巧，在很多工程场景都会用到（比如内存池、对象池、ECS 架构中的 sparse set）。

---

### 代码实现

```python
import random

class RandomizedSet:
    def __init__(self):
        # val -> index in nums
        self.index_map = {}
        # 存储实际元素，支持 O(1) 随机访问
        self.nums = []

    def insert(self, val: int) -> bool:
        if val in self.index_map:
            return False
        self.index_map[val] = len(self.nums)
        self.nums.append(val)
        return True

    def remove(self, val: int) -> bool:
        if val not in self.index_map:
            return False
        # 拿到要删除元素的位置
        idx = self.index_map[val]
        last_val = self.nums[-1]
        
        # 交换到末尾（O(1)）
        self.nums[idx] = last_val
        self.index_map[last_val] = idx
        
        # 删除末尾（O(1)）
        self.nums.pop()
        del self.index_map[val]
        return True

    def getRandom(self) -> int:
        return random.choice(self.nums)
```

```java
import java.util.*;

public class RandomizedSet {
    private Map<Integer, Integer> indexMap; // val -> index
    private List<Integer> nums;
    private Random rand;

    public RandomizedSet() {
        indexMap = new HashMap<>();
        nums = new ArrayList<>();
        rand = new Random();
    }

    public boolean insert(int val) {
        if (indexMap.containsKey(val)) return false;
        indexMap.put(val, nums.size());
        nums.add(val);
        return true;
    }

    public boolean remove(int val) {
        if (!indexMap.containsKey(val)) return false;
        int idx = indexMap.get(val);
        int lastVal = nums.get(nums.size() - 1);
        
        // 交换到末尾
        nums.set(idx, lastVal);
        indexMap.put(lastVal, idx);
        
        // 删除末尾
        nums.remove(nums.size() - 1);
        indexMap.remove(val);
        return true;
    }

    public int getRandom() {
        return nums.get(rand.nextInt(nums.size()));
    }
}
```

```go
type RandomizedSet struct {
    indexMap map[int]int // val -> index
    nums     []int
}

func Constructor() RandomizedSet {
    return RandomizedSet{
        indexMap: make(map[int]int),
        nums:     make([]int, 0),
    }
}

func (this *RandomizedSet) Insert(val int) bool {
    if _, ok := this.indexMap[val]; ok {
        return false
    }
    this.indexMap[val] = len(this.nums)
    this.nums = append(this.nums, val)
    return true
}

func (this *RandomizedSet) Remove(val int) bool {
    idx, ok := this.indexMap[val]
    if !ok {
        return false
    }
    lastIdx := len(this.nums) - 1
    lastVal := this.nums[lastIdx]
    
    // 交换到末尾
    this.nums[idx] = lastVal
    this.indexMap[lastVal] = idx
    
    // 删除末尾
    this.nums = this.nums[:lastIdx]
    delete(this.indexMap, val)
    return true
}

func (this *RandomizedSet) GetRandom() int {
    return this.nums[rand.Intn(len(this.nums))]
}
```

---

### 复杂度分析

| 操作 | 时间 | 空间 |
|------|------|------|
| `insert()` | **O(1)** 均摊 | O(n) |
| `remove()` | **O(1)** | O(n) |
| `getRandom()` | **O(1)** | O(n) |

> ⚠️ `remove` 的 O(1) 依赖「交换到末尾」技巧。如果直接删除中间元素，数组搬移会导致 O(n)。

---

### 🔥 Follow-up 升级：允许重复元素（Insert Delete GetRandom O(1) — Duplicates allowed）

**LeetCode 381**：集合中允许重复值，`insert`/`remove`/`getRandom` 仍要求 O(1)。

**解法**：
- HashMap 的 value 从单个 index 变成 **index 的集合**（`HashSet<Integer>`）
- 删除时，从该 val 的 index 集合中任取一个位置进行交换删除
- `getRandom` 仍从数组中随机取，但因为同一 val 可能占多个位置，概率自然加权

```java
// 核心变化：HashMap<Integer, Set<Integer>>
// insert: 加到 set 中
// remove: 从 set 中取一个 index，交换删除后从 set 移除
```

> 💡 面试时说出「value 变成 Set」这个点，就已经打败了 70% 的候选人。

---

### 💬 面试官爱问的延伸

| 问题 | 一句话速答 |
|------|-----------|
| 如果元素有 10 亿个，内存不够怎么办？ | 分片 + 外部存储；或用布隆过滤器做存在性判断， misses 时查磁盘 |
| `getRandom` 要求严格的均匀分布，数组扩容时怎么办？ | ArrayList 扩容是均摊 O(1)，不影响随机分布；若要求绝对均匀，可用拒绝采样配合容量预分配 |
| 这个数据结构在工程中有什么应用？ | 游戏抽卡池（从奖池中随机抽取并移除）、A/B 测试分组、负载均衡的加权随机 |
| 线程安全版本怎么实现？ | 读写锁（读多写少场景）或分段锁；高并发下可用 `ConcurrentHashMap` + `CopyOnWriteArrayList` 权衡 |

---

## 🎤 面试技巧：存储引擎深度解析 + 缓存一致性策略

> Week 11 最后一天，把存储层的两个终极话题打通：磁盘上的数据怎么存（存储引擎），内存里的缓存怎么和磁盘同步（缓存一致性）。

---

### 1. B+Tree vs LSM-Tree：存储引擎的两大阵营

#### B+Tree（MySQL InnoDB、PostgreSQL）

**结构**：
- 多路平衡搜索树，所有数据存在叶子节点，叶子节点通过链表相连
- 非叶子节点只存键值，作为索引引导搜索
- 树高通常为 2~4 层，一次查询只需 2~4 次磁盘 IO

**为什么用 B+Tree 而不是 B-Tree？**
- B+Tree 叶子节点链表相连，**范围查询**只需顺序遍历叶子（B-Tree需中序遍历）
- B+Tree 非叶子节点不存数据，**扇出更高**，树更矮，IO 更少
- B+Tree 所有查询路径等长，**性能稳定**

**写入过程**：
1. 从根节点找到目标叶子节点
2. 若叶子节点有空位，直接插入
3. 若叶子节点满了，**分裂** — 取中间键提升到父节点，叶子分裂为两个
4. 分裂可能向上传播，最坏情况下从叶子分裂到根

> ⚠️ **随机写入的痛点**：B+Tree 的页分裂会导致大量随机磁盘 IO，且分裂后页利用率可能只有 50%。

#### LSM-Tree（RocksDB、LevelDB、Cassandra、TiKV）

**核心思想**：
- **写入优先**：所有写入先追加到内存中的有序结构（MemTable），再批量刷盘
- **顺序写**：磁盘上的 SSTable（Sorted String Table）只追加不修改，充分利用磁盘顺序写性能
- **后台合并**：定期合并多个 SSTable，清理过期数据，维持读取性能

**写入过程**：
1. 写 WAL（Write-Ahead Log）保证持久化
2. 写入内存 MemTable（通常用跳表或红黑树实现）
3. MemTable 满后，转为不可变的 Immutable MemTable，并新建空 MemTable
4. Immutable MemTable 刷盘为 Level-0 SSTable
5. 后台 Compaction 将低层 SSTable 合并到高层，清理重复和删除标记

**读取过程**：
1. 先查 MemTable
2. 再查 Immutable MemTable
3. 按 Level 从高到低查 SSTable（每层可能多个文件）
4. 找到即返回，没找到返回不存在

> ⚠️ **读取的痛点**：最坏情况下要查所有 Level 的所有文件，读放大严重。

#### 对比总结

| 维度 | B+Tree | LSM-Tree |
|------|--------|----------|
| **读性能** | 稳定 O(log n)，2~4 次 IO | 可能 O(log n) ~ O(n)，读放大 |
| **写性能** | 随机写，页分裂开销大 | 顺序写，内存写，极高吞吐 |
| **范围查询** | 叶子链表，极高效 | 需合并多层，一般 |
| **空间放大** | 低（除了分裂碎片） | 高（多版本共存，需 Compaction） |
| **适合场景** | 读多写少，事务型 OLTP | 写多读少，日志型、时序数据 |
| **代表数据库** | MySQL、PostgreSQL、SQL Server | RocksDB、Cassandra、TiDB、HBase |

> 💡 **面试回答模板**：
> 
> "B+Tree 追求读性能的稳定和事务友好，适合 OLTP；LSM-Tree 通过牺牲部分读性能换取极高的写入吞吐，适合日志、时序、大数据场景。TiDB 底层 TiKV 用 LSM-Tree 就是为了支撑海量写入，上层 SQL 层做事务补偿。"

---

### 2. 缓存一致性策略：缓存和数据库怎么同步？

这是面试最高频的「送命题」之一。四种模式要烂熟于心。

#### 模式一：Cache-Aside（旁路缓存，最常用）

```
读：先读缓存 → 命中返回，未命中读数据库 → 写入缓存 → 返回
写：先更新数据库 → 再删缓存（不是更新缓存！）
```

**为什么写操作是「删缓存」而不是「更新缓存」？**
- 两个并发写可能乱序，导致缓存里是旧值（A 先写 DB，B 后写 DB，但 B 先写缓存，A 后写缓存 → 缓存是 A 的值）
- 删除缓存简单、安全，下次读自然会回填最新值

**Race Condition（缓存穿透）**：
```
线程 A 读缓存未命中 → 读数据库得 v1
线程 B 更新数据库为 v2 → 删除缓存
线程 A 将 v1 写入缓存  ← ❌ 缓存里是旧值 v1，直到过期
```

**概率**：极低。因为 B 的「更新 DB + 删缓存」通常比 A 的「读 DB + 写缓存」快（写缓存是内存操作，但前面读 DB 是慢操作）。但如果发生：

**解法**：
1. **设置缓存 TTL** — 最终一致性，等过期自动修复
2. **延迟双删** — 删缓存 → 更新 DB → 休眠 200ms → 再删一次缓存
3. **Binlog 异步删** — 用 Canal/Debezium 监听 MySQL binlog，异步删缓存，保证最终一致性
4. **分布式锁** — 太重，一般不用

> 💡 面试时说：「我们线上用 Cache-Aside + 延迟双删 + Canal 监听 binlog 兜底，保证最终一致性。」

#### 模式二：Read-Through（读穿透）

```
读：应用只读缓存，缓存未命中时自动从数据库加载（缓存层自己处理）
写：应用只写数据库，由缓存层或外部机制同步
```

- 优点：应用逻辑简单
- 缺点：缓存层实现复杂，缓存和数据库耦合
- 代表：Redis 的 `read_through` 不是原生支持，通常需要客户端库或代理层实现

#### 模式三：Write-Through（直写）

```
写：同时更新缓存和数据库，两者都成功才返回
读：只读缓存
```

- 优点：缓存和数据库强一致
- 缺点：写延迟高（要等两个操作），写吞吐低
- 适用：对一致性要求极高的金融场景，但很少用

#### 模式四：Write-Behind / Write-Back（异步回写）

```
写：只写缓存，立即返回；缓存异步批量写回数据库
读：只读缓存
```

- 优点：写性能极高（纯内存操作）
- 缺点：数据丢失风险大（缓存挂了，数据没落盘）
- 适用：写密集型、可容忍少量丢失的场景（如计数器、日志）
- 代表：Linux Page Cache、MySQL InnoDB Buffer Pool 的脏页刷盘

#### 四种模式对比

| 模式 | 一致性 | 读性能 | 写性能 | 复杂度 | 典型场景 |
|------|--------|--------|--------|--------|---------|
| **Cache-Aside** | 最终一致 | 高 | 高 | 低 | **互联网业务最常用** |
| **Read-Through** | 最终一致 | 高 | 中 | 中 | 需要缓存框架支撑 |
| **Write-Through** | 强一致 | 极高 | 低 | 中 | 金融、强一致场景 |
| **Write-Behind** | 弱一致 | 极高 | 极高 | 高 | 计数器、日志、缓冲层 |

---

### 3. 面试高频连环追问

**面试官**：Cache-Aside 下，先删缓存再更新数据库，有什么问题？

**你**：
```
线程 A 删缓存
线程 B 读缓存未命中 → 读数据库得旧值 → 写缓存
线程 A 更新数据库为新值
→ 缓存里是旧值，数据库是新值，不一致窗口更长
```
结论：**先更新数据库再删缓存更好**，不一致窗口更短（因为写 DB 比读 DB 慢，B 在 A 写 DB 期间读到旧值的概率更低）。

**面试官**：那极端情况下还是不一致怎么办？

**你**：
1. 业务容忍 — 大多数场景最终一致即可（如商品详情页 1 秒内不一致无感）
2. TTL 兜底 — 设置合理的过期时间
3. 延迟双删 — 写后休眠几百毫秒再删一次
4. Binlog 监听 — Canal/Debezium 做异步兜底
5. 分布式锁 — 强一致但性能差，非必要不用

**面试官**：Redis 和 MySQL 数据不一致，怎么排查？

**你**：
1. **看监控** — 缓存命中率是否骤降（可能大面积失效）
2. **对账** — 抽样对比 Redis 和 MySQL 的关键字段
3. **查日志** — 看是否有异常导致删缓存失败
4. **看 TTL** — 是否设置过长，导致旧数据滞留太久
5. **查 Race** — 高并发场景下是否触发了经典的读写竞争

---

## 📋 今日速记卡

### 算法题速记
```
RandomizedSet — HashMap + ArrayList
  ├─ HashMap: val -> index（O(1) 查找）
  ├─ ArrayList: 存元素（O(1) 随机访问）
  ├─ 删除: 交换到末尾 → pop_back() → O(1)
  └─ 重复元素版: value 改为 Set<Integer>
```

### 存储引擎速记
```
B+Tree（MySQL）         LSM-Tree（RocksDB）
  ├─ 读快，写慢           ├─ 写快，读可能慢
  ├─ 页分裂随机 IO        ├─ 顺序写 + 后台合并
  ├─ 适合 OLTP            ├─ 适合日志/时序
  └─ 树高 2~4 层          └─ MemTable + WAL + SSTable
```

### 缓存一致性速记
```
Cache-Aside（最常用）:
  读: 先缓存 → 未命中查 DB → 回填
  写: 先 DB → 再删缓存（不是更新！）
  防竞争: 延迟双删 + Canal 监听 binlog

Write-Through: 双写，强一致，写慢
Write-Behind: 只写缓存异步刷盘，高性能但可能丢
```

---

## 🔗 关联复习

- [Day 70 — MySQL 索引原理](2026-08-13-mysql-index.md) — B+Tree 索引结构基础
- [Day 71 — MySQL 事务与 MVCC](2026-08-14-transaction-mvcc.md) — 存储引擎的事务支持
- [Day 72 — 设计排行榜 + Redis 核心](2026-08-15-redis-leaderboard.md) — Redis 缓存设计与三坑
- [Day 73 — LFU 缓存设计](2026-08-16-lfu-cache.md) — 缓存淘汰策略深度
- [Day 74 — 点击量统计器 + 分库分表](2026-08-17-hit-counter.md) — 分布式存储策略
- [Day 46 — LRU 缓存设计](../week07/2026-07-22-lru-cache.md) — HashMap + 双向链表经典
- [Day 47 — 设计跳表](../week07/2026-07-23-skiplist.md) — LSM-Tree MemTable 常用实现

---

> 📝 **Week 11 完结撒花 🎉**
>
> 这七天我们覆盖了整个存储层：MySQL 索引与事务 → Redis 核心与设计 → 连接池与 SQL 优化 → 分库分表 → 存储引擎对比 → 缓存一致性。
>
> **下周预告（Week 12）**：大数据与实时计算 — MapReduce / Spark 核心原理、流处理 Flink、数据仓库与 ETL、ClickHouse 列式存储。
>
> 74 + 1 = **75 天，连续更新从未中断。** 🔥
