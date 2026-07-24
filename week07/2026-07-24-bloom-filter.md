# Day 48 — 实现布隆过滤器（Bloom Filter）

**日期：** 2026-07-24  
**主题：** Week 7 — 高级数据结构  
**难度：** 🟡 中等偏上（设计题）  
**预估时间：** 25–40 分钟

---

## 🎯 今日算法题

### 题目：实现布隆过滤器

**描述：**

布隆过滤器（Bloom Filter）是一种**空间效率极高**的概率型数据结构，用于判断一个元素**是否可能存在于集合中**。

你需要实现 `BloomFilter` 类：

- `BloomFilter(int size, int hashCount)` — 初始化布隆过滤器，`size` 为位数组长度，`hashCount` 为哈希函数个数
- `void add(string item)` — 将元素加入集合
- `bool contains(string item)` — 判断元素是否可能在集合中。返回 `true` 表示"可能存在"，返回 `false` 表示"肯定不存在"

**关键特性：**
- **无假阴性**：如果 `contains` 返回 `false`，元素**一定不在**集合中
- **有假阳性**：如果 `contains` 返回 `true`，元素**可能不在**集合中（概率可控）
- **不支持删除**（标准版本）

**约束：**
- 假设已有 `hash(i, item)` 函数可用，返回第 `i` 个哈希值（对 `size` 取模后作为位数组下标）
- 用位数组（bit array）实现，不能用布尔数组浪费空间

---

### 💡 核心思路

布隆过滤器的本质思想是 **"用多个哈希函数投票，用位数组存痕迹"**：

```
位数组:  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]  (size=10)

add("apple"):
  hash0("apple") % 10 = 2  → 位数组[2] = 1
  hash1("apple") % 10 = 5  → 位数组[5] = 1
  hash2("apple") % 10 = 8  → 位数组[8] = 1

位数组变为: [0, 0, 1, 0, 0, 1, 0, 0, 1, 0]

contains("apple"):
  检查位数组[2]、位数组[5]、位数组[8] 是否都为 1 → 是，返回 true

contains("banana"):
  hash0("banana") % 10 = 2  → 1 ✓
  hash1("banana") % 10 = 7  → 0 ✗
  有一个位是 0 → 肯定不存在，返回 false
```

**假阳性是怎么产生的？**

假设 `add("apple")` 把位 2, 5, 8 设为 1。  
`add("grape")` 把位 5, 8, 9 设为 1。  
现在检查 `contains("peach")`，如果它的三个哈希值恰好命中 2, 5, 8 —— 这三个位都是 1，但 `peach` 从没被加入过。**这就是假阳性。**

---

### 📝 参考代码（Python）

```python
import hashlib

class BloomFilter:
    def __init__(self, size: int, hash_count: int):
        """
        size: 位数组长度（决定空间占用和误判率）
        hash_count: 哈希函数个数（一般取最优值 ≈ (size/n) * ln(2)，n 为预期元素数）
        """
        self.size = size
        self.hash_count = hash_count
        # Python 里用 bytearray 模拟位数组，每个 byte 存 8 个位
        # 实际项目中可用 bitarray 库或自己封装位运算
        self.bit_array = bytearray((size + 7) // 8)

    def _hashes(self, item: str):
        """生成 hash_count 个哈希值。技巧：用双哈希组合出多个哈希函数。"""
        # 用两个独立的哈希值，线性组合出第 i 个哈希函数
        # 这是 Kirsch-Mitzenmacher 优化，避免计算多个独立哈希的开销
        hash1 = int(hashlib.md5((item + "salt1").encode()).hexdigest(), 16)
        hash2 = int(hashlib.md5((item + "salt2").encode()).hexdigest(), 16)
        
        for i in range(self.hash_count):
            # 组合哈希: (hash1 + i * hash2) % size
            yield (hash1 + i * hash2) % self.size

    def _set_bit(self, index: int):
        """将位数组第 index 位设为 1"""
        byte_index = index // 8
        bit_index = index % 8
        self.bit_array[byte_index] |= (1 << bit_index)

    def _get_bit(self, index: int) -> bool:
        """读取位数组第 index 位"""
        byte_index = index // 8
        bit_index = index % 8
        return (self.bit_array[byte_index] >> bit_index) & 1

    def add(self, item: str):
        """将元素加入布隆过滤器"""
        for hash_val in self._hashes(item):
            self._set_bit(hash_val)

    def contains(self, item: str) -> bool:
        """判断元素是否可能存在。返回 False 则一定不存在。"""
        for hash_val in self._hashes(item):
            if not self._get_bit(hash_val):
                return False  # 只要有 1 个位是 0，肯定不存在
        return True  # 所有位都是 1，可能存在（也可能是假阳性）


# ===== 使用示例 =====
bf = BloomFilter(size=1000, hash_count=3)

# 加入一些元素
for word in ["apple", "banana", "cherry", "date", "elderberry"]:
    bf.add(word)

# 测试存在的元素 —— 一定返回 True
print(bf.contains("apple"))     # True ✓
print(bf.contains("banana"))    # True ✓

# 测试不存在的元素 —— 大概率返回 False，小概率假阳性
print(bf.contains("grape"))     # False ✓（大概率）
print(bf.contains("watermelon")) # False ✓（大概率）
```

---

### ⏱️ 复杂度分析

| 操作 | 时间复杂度 | 空间复杂度 |
|------|-----------|-----------|
| `add` | **O(k)** — k 为哈希函数个数 | O(m/8) bytes（m 为位数组长度） |
| `contains` | **O(k)** | O(1) |

**假阳性概率公式：**

$$p \approx \left(1 - e^{-kn/m}\right)^k$$

- `n` = 已插入元素个数
- `m` = 位数组长度
- `k` = 哈希函数个数

**最优哈希函数个数**（给定 m 和 n）：

$$k_{opt} = \frac{m}{n} \ln 2 \approx 0.7 \times \frac{m}{n}$$

**实际例子**：如果预期插入 100 万元素，允许 1% 误判率：
- 需要位数组大小 `m ≈ 1.44 MB`
- 哈希函数个数 `k ≈ 7`
- 对比：直接存 100 万个字符串（假设平均 10 字节）需要 **10 MB+**
- 布隆过滤器用 **~1.4 MB** 实现，空间省了 **7 倍+**

---

### 🔗 举一反三

| 延伸方向 | 内容 |
|---------|------|
| **计数布隆过滤器（Counting Bloom Filter）** | 每个位改成计数器（如 4-bit），支持删除操作。空间变大，但功能更强 |
| **布谷鸟过滤器（Cuckoo Filter）** | 替代方案，支持删除、查找更快、空间效率相近，但实现更复杂 |
| **Redis 的 Bloom Filter 模块** | RedisBloom 模块原生支持，可以直接 `BF.ADD` / `BF.EXISTS` |
| **Google Guava 的 BloomFilter** | Java 世界的工业级实现，源码值得精读 |

---

## 🎤 面试技巧

### 1. 开场：先讲场景，再讲原理

**千万别上来就背公式。** 面试官想听到的是：

> "布隆过滤器的核心价值是**用极小的空间，快速排除绝对不存在的情况**。它的经典场景是 Redis 缓存穿透防护 —— 先查布隆过滤器，如果 key 肯定不存在，直接返回空，避免打到数据库。"

然后再展开原理：位数组 + 多个哈希 + 假阳性的 trade-off。

### 2. 面试官追问："你刚才说不能删除，那如果我要删怎么办？"

**答题路径：**

1. **先说问题**：标准布隆过滤器把一个位设为 1 后，不知道这个 1 是哪些元素贡献的，直接清 0 会影响其他元素
2. **给方案**：计数布隆过滤器（Counting Bloom Filter）— 每个位存一个小的计数器（如 4-bit），add 时计数器 +1，delete 时 -1
3. **提 trade-off**：空间变成原来的 4 倍，计数器可能溢出（计数器值不够大时）
4. **进阶选项**：布谷鸟过滤器（Cuckoo Filter）— 另一种支持删除的方案，每个元素存指纹（fingerprint）而非完整哈希

### 3. 面试官追问："怎么计算需要多大的位数组？"

**公式背不住没关系，说思路：**

> "假阳性率 p 和三个变量有关：位数组大小 m、哈希函数个数 k、插入元素数 n。  
> 给定允许的误判率 p 和预期元素数 n，可以解出最优的 m 和 k。  
> 工程上有一个近似口诀：想做到 1% 误判率，每个元素需要约 10 个位；k 取 7 个左右。"

### 4. 手写代码的加分细节

| 细节 | 为什么加分 |
|------|-----------|
| 用 `bytearray` 而不是 `list[bool]` | 展示你对内存的敏感，bool 在 Python 里是一个对象引用，超浪费 |
| 双哈希组合（Kirsch-Mitzenmacher） | 说明你知道优化技巧，不需要真的算 k 次独立哈希 |
| 提到 "实际项目中用现成的库" | 体现工程思维 —— 面试考的是理解，生产中别自己造轮子 |

---

### 5. 一个杀手级回答：布隆过滤器的经典应用场景

如果被问到"你在什么场景下用过"，可以这么答：

> "我做过一个爬虫 URL 去重系统，每天爬取上亿个链接。用 Redis Set 存已爬 URL 内存爆炸，用数据库查又太慢。  
> 最后方案是：**布隆过滤器做第一层筛选** —— 大部分已爬过的 URL 在这里被快速排除（O(k) + 内存极小）；  
> 布隆过滤器说'可能存在'的，再查一次 Redis Set 确认。  
> 这样 95% 的请求在布隆过滤器层就返回了，数据库/Redis 压力降了一个数量级。"

---

## ✅ 今日自检清单

- [ ] 能手写布隆过滤器的 add 和 contains 逻辑
- [ ] 能解释假阳性的成因，以及为什么"无假阴性"
- [ ] 能说出至少 3 个真实应用场景（缓存穿透、爬虫去重、HBase、Chrome 恶意 URL）
- [ ] 知道计数布隆过滤器和布谷鸟过滤器是支持删除的替代方案
- [ ] 能估算给定误判率和数据量下，位数组大概需要多大

---

> 💡 **Week 7 收官总结：** 这一周我们覆盖了并查集、Trie、线段树、树状数组、LCA、单调栈/队列、LRU Cache、跳表、布隆过滤器 —— 高级数据结构的"全明星阵容"。下一周（Week 8）将进入**系统设计基础**：分布式 ID、限流器、一致性哈希、短链服务。

> "布隆过滤器教会你最重要的一课：有时候，'大概知道'比'精确知道'更有价值 —— 尤其是在空间和时间的战场上。"
