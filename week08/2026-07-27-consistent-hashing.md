# Day 51: 一致性哈希（Consistent Hashing）

> 分布式系统的"罗盘"——节点动态扩缩容时，让数据迁移成本降到最低的经典算法。

---

## 1. 今日设计题

### 题目描述
设计一个分布式缓存系统（如 Redis Cluster、Memcached），需要解决数据在多个节点间的分布问题。要求：
- 数据能够均匀分布在 **N 个缓存节点** 上
- 当节点 **增加或删除** 时，**仅影响少量数据** 的迁移（不是全量重哈希）
- 支持**虚拟节点**解决数据倾斜问题
- 实现 `getNode(key)` 和 `addNode(node)` / `removeNode(node)` 核心操作

### 核心场景
- 缓存集群有 3 个节点，100万条数据均匀分布
- 双11前扩容到 5 个节点，希望只迁移 ~40% 的数据，而不是 100%
- 某节点宕机，其数据自动漂移到相邻节点，避免缓存雪崩

---

## 2. 算法/设计思路

### 2.1 传统哈希的问题 ❌

普通取模哈希：`hash(key) % N`

**致命伤**：节点数 N 变化时，**几乎所有数据**的映射位置都会变。
- 3 个节点 → 4 个节点：`hash(key) % 3` → `hash(key) % 4`
- 迁移率 ≈ `1 - 1/N`（N=3 时迁移 67%，N=100 时迁移 99%）

**面试必说的一句话**："传统哈希把节点数量和哈希值强绑定，一致性哈希的核心是把节点和数据映射到同一个环上，用距离找归属。"

---

### 2.2 一致性哈希核心思想 ⭐

**三步构建**：
1. **哈希环**：将哈希空间 `[0, 2^32-1]` 首尾相接成一个环
2. **节点落环**：每个节点（物理节点或虚拟节点）计算哈希值，落在环上的某个位置
3. **数据归属**：数据 key 的哈希值顺时针找**第一个遇到的节点**，就是它归属的节点

```
        hash ring (0 ~ 2^32-1)

        0  ←━━━━━━━━━━━━━━━━━━━━ 2^32
           ┃                   ┃
    node-A(5)              node-C(2^31)
           ┃         key-X     ┃
           ┃    ╭──────╮       ┃
           ┃    ↓      │       ┃
           ┃  node-B(2^30)     ┃
           ┃                   ┃

key-X 顺时针找到的第一个节点是 node-C → 归属 node-C
```

---

### 2.3 完整实现

```python
import hashlib
import bisect

class ConsistentHashRing:
    def __init__(self, replicas: int = 150):
        """
        replicas: 每个物理节点的虚拟节点数（解决数据倾斜）
        """
        self.replicas = replicas      # 虚拟节点数
        self.ring = {}                # hash -> node 映射
        self.sorted_keys = []         # 排序后的哈希值数组（用于二分查找）
        self.nodes = set()            # 物理节点集合
    
    def _hash(self, key: str) -> int:
        """MD5 转 32位整数，均匀分布"""
        return int(hashlib.md5(key.encode()).hexdigest(), 16) % (2**32)
    
    def add_node(self, node: str):
        """添加物理节点，同时添加 replicas 个虚拟节点到环上"""
        if node in self.nodes:
            return
        self.nodes.add(node)
        for i in range(self.replicas):
            # 虚拟节点 key: "node:0", "node:1", ...
            virtual_key = f"{node}:{i}"
            h = self._hash(virtual_key)
            self.ring[h] = node
            bisect.insort(self.sorted_keys, h)
    
    def remove_node(self, node: str):
        """移除物理节点，同时清理其所有虚拟节点"""
        if node not in self.nodes:
            return
        self.nodes.discard(node)
        for i in range(self.replicas):
            virtual_key = f"{node}:{i}"
            h = self._hash(virtual_key)
            del self.ring[h]
            idx = bisect.bisect_left(self.sorted_keys, h)
            self.sorted_keys.pop(idx)
    
    def get_node(self, key: str) -> str:
        """为 key 找到顺时针最近的节点"""
        if not self.ring:
            return None
        
        h = self._hash(key)
        idx = bisect.bisect_right(self.sorted_keys, h)
        
        # 如果超出环末尾，回到起点（环形）
        if idx == len(self.sorted_keys):
            idx = 0
        
        node_hash = self.sorted_keys[idx]
        return self.ring[node_hash]


# ===== 使用示例 =====
ring = ConsistentHashRing(replicas=150)

# 初始 3 节点
for node in ["node-A", "node-B", "node-C"]:
    ring.add_node(node)

# 查询 key 归属
print(ring.get_node("user:1001"))   # → node-B（假设）
print(ring.get_node("user:1002"))   # → node-A（假设）

# 扩容：添加 node-D，只影响 ~25% 的数据
ring.add_node("node-D")
print("扩容后:", ring.get_node("user:1001"))  # 可能不变，可能变

# 缩容：移除 node-A，其数据顺时针漂移到下一个节点
ring.remove_node("node-A")
```

---

### 2.4 复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|-----------|------|
| `get_node` | **O(log V)** | V = 虚拟节点总数，二分查找 |
| `add_node` | **O(V log V)** | 插入 V 个虚拟节点，每次 bisect O(log V) |
| `remove_node` | **O(V log V)** | 删除 V 个虚拟节点 |
| 数据迁移率 | **~1/N** | N 个节点时，增删节点只影响 ~1/N 的数据 |

---

### 2.5 为什么需要虚拟节点？

**问题**：如果物理节点很少（如 3 个），节点哈希值可能分布不均匀，导致某些节点负载远高于其他节点。

**解决方案**：每个物理节点映射为 **replicas 个虚拟节点**（通常 100~200 个），均匀撒在环上。

```
无虚拟节点（3 个节点）:          有虚拟节点（每个 150 个）:

  node-A(10)                        A0  A1  A2 ... A149
       ╲                           ╱  ╲  ╱  ╲
        ╲    node-C(2^31)         B0  C7  A3  B50 ...
         ╲  ╱                    ╱ ╲ ╱ ╲ ╱ ╲
          ╲╱                    均匀铺满整个环
       node-B(2^30)
    负载可能严重倾斜              负载天然均衡
```

**工程经验值**：虚拟节点数 `replicas = 100~200`，在均衡性和内存开销间取平衡。

---

### 2.6 经典应用场景

| 系统 | 用法 |
|------|------|
| **Redis Cluster** | 无中心架构，节点间用一致性哈希分片（实际用 16384 个 slot，是改进版） |
| **Memcached** | 客户端实现一致性哈希（如 libmemcached） |
| **Dynamo (AWS)** | 论文中提出，结合虚拟节点 + 复制因子 N |
| **Cassandra** | 分区键 → token → 节点，一致性哈希的变体 |
| **CDN / 负载均衡** | 请求路由到最近的缓存节点 |

---

## 3. 面试技巧

### 3.1 开场白（30 秒讲清楚）

> "一致性哈希解决的是分布式系统中节点动态扩缩容时的数据迁移问题。传统取模哈希在节点数变化时需要迁移几乎全部数据，而一致性哈希通过把节点和数据映射到同一个环上，让数据只找顺时针最近的节点，增删节点时只影响相邻区间，迁移率降到约 1/N。"

### 3.2 必画的图

面试时手画这个，胜率 +30%：

```
    哈希环 (0 ~ 2^32-1)

         0
         │
    ┌────┴────┐
    │         │
  node-A   node-C
  (h=5)   (h=2^31)
    │         │
    │  key-X  │    ← key-X 顺时针找到 node-C
    │    ↓    │
    └────┬────┘
         │
      node-B
      (h=2^30)
         │
       2^32
```

### 3.3 高频追问与回答

**Q: 虚拟节点数怎么选？**
> "通常 100~200 个。太少负载不均衡，太多内存开销大。看集群规模——节点少选大值（200），节点多选小值（100）。"

**Q: 节点宕机后数据怎么处理？**
> "相邻节点接管，但需要配合复制机制。Dynamo 的做法是每个数据存 N 份（复制因子），顺时针找 N 个节点存储。这样单节点宕机，其数据在其他节点还有副本。"

**Q: 一致性哈希和数据迁移的具体比例？**
> "理论上是 1/N。3 个节点扩容到 4 个，迁移约 25% 的数据；100 个节点扩容到 101 个，迁移约 1%。"

**Q: Redis Cluster 为什么不用纯一致性哈希？**
> "Redis Cluster 用的是 **Hash Slot（16384 个槽）**，是一致性哈希的改进版。每个槽固定映射到节点，数据先算槽再算节点。好处是槽可以批量迁移，管理更灵活。"

### 3.4 常见踩坑

- ❌ 只说"用哈希环"，不提虚拟节点 → 会被追问数据倾斜怎么解决
- ❌ 混淆一致性哈希和普通哈希 → 要讲清楚**迁移率**的对比
- ❌ 只讲理论不写代码 → 面试官可能让你手写 `get_node`
- ✅ 主动提"复制因子 N"→ 展示你对 Dynamo 论文的了解，加分

---

## 4. 今日总结

| 要点 | 一句话 |
|------|--------|
| 核心思想 | 节点和数据在同一个环上，顺时针找归属 |
| 解决的问题 | 节点扩缩容时数据全量迁移 |
| 虚拟节点 | 解决数据倾斜，每个物理节点映射为 100~200 个虚拟节点 |
| 时间复杂度 | 查询 O(log V)，增删节点 O(V log V) |
| 迁移率 | ~1/N，远优于传统哈希的 ~100% |
| 经典应用 | Redis Cluster(slot)、Memcached、Dynamo、Cassandra |

> **面试金句**："一致性哈希的价值不是让哈希更快，而是让扩容时少加班。"

---

*Day 51 / Week 8 — 系统设计基础：一致性哈希*  
*明日预告：短链服务（URL Shortener）设计*
