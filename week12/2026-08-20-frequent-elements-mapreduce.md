# Day 77 · 前 K 个高频元素 + MapReduce 原理与分布式计算

> **Week 12 — 大数据处理与搜索引擎 🔍**
>
> 从一道「统计词频」的算法题，切入分布式计算的鼻祖框架 MapReduce。

---

## 一、今日算法题：前 K 个高频元素

### 📋 题目描述

**LeetCode 347. Top K Frequent Elements**

给定一个非空整数数组 `nums` 和一个整数 `k`，返回其中出现频率前 `k` 高的元素。可以按任意顺序返回。

**示例：**
```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
解释: 1 出现 3 次，2 出现 2 次，3 出现 1 次。前 2 高频是 1 和 2。
```

**进阶：** 要求算法的时间复杂度必须优于 O(n log n)，其中 n 是数组的大小。

---

### 🧠 解题思路

这道题是 **Word Count + Top K** 的组合，也是 MapReduce 经典示例的原型。三种解法层层递进：

#### 解法一：哈希表 + 最小堆（最通用，O(n log k)）

```python
import heapq
from collections import Counter

def topKFrequent(nums, k):
    # Step 1: 统计频率 —— 这就是 Map 阶段
    freq = Counter(nums)  # {num -> count}
    
    # Step 2: 用最小堆维护 Top K —— 这就是 Reduce 阶段的聚合
    min_heap = []
    for num, count in freq.items():
        heapq.heappush(min_heap, (count, num))
        if len(min_heap) > k:
            heapq.heappop(min_heap)  # 淘汰频率最低的
    
    # Step 3: 输出结果
    return [num for count, num in min_heap]
```

**复杂度：** 时间 O(n log k)，空间 O(n)

**核心思想：** 堆的大小始终 ≤ k，所以每次插入/弹出是 O(log k)，比全排序的 O(n log n) 更优。

---

#### 解法二：哈希表 + 桶排序（O(n)，面试加分项）

```python
def topKFrequent_bucket(nums, k):
    freq = Counter(nums)
    n = len(nums)
    
    # 桶数组：索引 = 频率，值 = 该频率下的所有数字列表
    buckets = [[] for _ in range(n + 1)]
    for num, count in freq.items():
        buckets[count].append(num)
    
    # 从高频率往低频率收集
    result = []
    for i in range(n, 0, -1):
        if buckets[i]:
            result.extend(buckets[i])
        if len(result) >= k:
            return result[:k]
    
    return result[:k]
```

**复杂度：** 时间 O(n)，空间 O(n)

**为什么能 O(n)？** 因为频率的范围被限制在 `[1, n]` 之间，用数组做桶，避免了排序的 log 因子。

---

#### 解法三：快速选择（Quickselect，平均 O(n)）

```python
def topKFrequent_quickselect(nums, k):
    freq = list(Counter(nums).items())  # [(num, count), ...]
    
    def partition(left, right, pivot_idx):
        pivot_freq = freq[pivot_idx][1]
        # 把 pivot 移到末尾
        freq[pivot_idx], freq[right] = freq[right], freq[pivot_idx]
        store_idx = left
        for i in range(left, right):
            if freq[i][1] > pivot_freq:  # 降序：大的放左边
                freq[store_idx], freq[i] = freq[i], freq[store_idx]
                store_idx += 1
        freq[right], freq[store_idx] = freq[store_idx], freq[right]
        return store_idx
    
    def quickselect(left, right, k_smallest):
        if left == right:
            return
        pivot_idx = random.randint(left, right)
        pivot_idx = partition(left, right, pivot_idx)
        if k_smallest == pivot_idx:
            return
        elif k_smallest < pivot_idx:
            quickselect(left, pivot_idx - 1, k_smallest)
        else:
            quickselect(pivot_idx + 1, right, k_smallest)
    
    n = len(freq)
    quickselect(0, n - 1, k - 1)
    return [num for num, count in freq[:k]]
```

**复杂度：** 平均 O(n)，最坏 O(n²)（可随机化 pivot 避免）

---

### 🔍 三种解法对比

| 解法 | 时间复杂度 | 空间复杂度 | 适用场景 | 面试推荐度 |
|---|---|---|---|---|
| 最小堆 | O(n log k) | O(n) | 通用，k << n 时最优 | ⭐⭐⭐⭐⭐ |
| 桶排序 | O(n) | O(n) | 数据范围有限，追求极致性能 | ⭐⭐⭐⭐ |
| 快速选择 | 平均 O(n) | O(n) | 数组场景，不稳定的 O(n) | ⭐⭐⭐ |

**面试首选最小堆** —— 实现简洁，思路清晰，且能自然延伸到「大数据场景下怎么优化」。

---

### 💬 面试官连环追问

> **Q1: 如果数据量特别大，单机内存放不下怎么办？**
>
> 分治 + 归并：
> 1. 将数据分片到多台机器
> 2. 每台机器本地统计词频（Map）
> 3. 按 key 哈希分发到 reducer，汇总全局频率（Reduce）
> 4. 最后用堆或快速选择求 Top K
>
> 这就是 MapReduce 的 WordCount 模式。

> **Q2: 如果要求实时返回 Top K，数据流不断到来？**
>
> 维护一个大小为 k 的最小堆：
> - 新元素到来 → 更新计数 → 如果它在堆中或比堆顶大，调整堆
> - 均摊 O(log k) 每次更新
>
> 或者使用 Count-Min Sketch 做近似频率估计，空间更小。

> **Q3: 如果要求严格按频率排序输出呢？**
>
> 堆解法本身无序，最后需要 O(k log k) 排序。
> 或者直接用桶排序/快速选择，按频率分桶天然有序。

---

## 二、面试技巧：MapReduce 原理与分布式计算

### 🗺️ MapReduce 编程模型

MapReduce 是 Google 2004 年提出的分布式计算模型，核心思想：**先分治处理，再汇总结果**。

```
Input → Split → Map → Shuffle/Sort → Reduce → Output
              ↑                      ↑
           本地处理              网络传输 + 聚合
```

**WordCount 完整执行流程：**

```
输入文本分片:
  Split 1: "hello world hello"
  Split 2: "hello mapreduce"

Map 输出 (key=word, value=1):
  Split 1 → (hello,1), (world,1), (hello,1)
  Split 2 → (hello,1), (mapreduce,1)

Shuffle 按 key 排序分组:
  hello: [1, 1, 1]
  world: [1]
  mapreduce: [1]

Reduce 聚合 (sum):
  hello → 3
  world → 1
  mapreduce → 1
```

---

### 🔧 核心组件详解

#### 1. Combiner（本地预聚合）

在 Map 端先做一次局部 Reduce，减少网络传输量。

```
Without Combiner: 每个 (word,1) 都传到 Reduce → 网络爆炸
With Combiner:    Map 端先 sum → 只传 (word,local_sum) → 省带宽
```

**注意：** Combiner 只在满足交换律和结合律时可用（如 sum、count、max），求平均值不能用。

#### 2. Partitioner（分区策略）

决定 Map 的输出发送到哪个 Reduce：

```java
// 默认按 key 的 hashCode % numReducers
public int getPartition(K key, V value, int numReducers) {
    return (key.hashCode() & Integer.MAX_VALUE) % numReducers;
}
```

**自定义分区**场景：确保相同用户的数据到同一个 Reduce，避免全局排序时的跨节点合并。

#### 3. 容错机制

| 机制 | 作用 |
|---|---|
| **任务重试** | Task 失败自动调度到其他节点重跑 |
| **推测执行** | 慢任务启动备份任务，谁先完成用谁 |
| **数据本地化** | 优先在数据所在节点执行 Map，减少网络 I/O |
| **Checkpoint** | 定期保存中间状态，失败从 checkpoint 恢复 |

---

### ⚔️ Hadoop vs Spark vs Flink 对比

| 特性 | Hadoop MR | Spark | Flink |
|---|---|---|---|
| **处理模式** | 批处理 | 批处理 + 流处理（微批） | 真正的流处理 + 批处理 |
| **延迟** | 分钟级 | 秒级 ~ 分钟级 | 毫秒级 |
| **中间结果** | 写磁盘 | 内存缓存 | 内存状态 |
| **容错** | 任务重跑 | RDD 血统重算 | Checkpoint + 状态后端 |
| **API 友好度** | 低（写很多样板代码） | 高（Scala/Python/SQL） | 高（统一 DataStream API） |
| **适用场景** | 离线 ETL、日志分析 | 机器学习、交互式查询 | 实时风控、实时推荐、CEP |

**面试一句话总结：**
- **Hadoop** = 分布式存储 + 批计算，稳定但慢
- **Spark** = 内存计算，批为主流场景王者
- **Flink** = 真正的流处理，事件驱动，低延迟首选

---

### 🎯 面试高频问题速查

#### Q1: MapReduce 为什么慢？Spark 怎么优化的？

**MapReduce 慢的原因：**
1. 中间结果强制落盘（Map → 磁盘 → Reduce）
2. 每个 Job 启停开销大
3. 不适合迭代计算（每次迭代都读写 HDFS）

**Spark 优化：**
1. **内存计算**：中间结果缓存在内存，迭代快 10~100 倍
2. **DAG 调度**：整合作业为有向无环图，减少 Stage 数
3. **RDD 血统**：失败时根据血统重算，不用从头跑

#### Q2: 数据倾斜（Data Skew）怎么解决？

**现象：** 某个 key 的数据量远大于其他，导致某个 Reduce 任务特别慢。

**解决方案：**
1. **加盐（Salting）**：给热点 key 加随机前缀，分散到多个 Reduce，最后二次聚合
2. **自定义 Partitioner**：让数据更均匀分布
3. **Combiner 预聚合**：减少传输到 Reduce 的数据量
4. **两阶段聚合**：先局部聚合，再全局聚合

```python
# 加盐示例：给 "hello" 加随机前缀
random_prefix = random.randint(0, 9)
key = f"{random_prefix}_hello"  # 分散到 10 个 reducer
# Reduce 后再去掉前缀二次聚合
```

#### Q3: 100TB 数据求 Top 100，怎么做？

```
Step 1: 数据分片，每台机器本地用 HashMap 统计词频（Map）
Step 2: 按 key 范围或哈希分发到 Reduce 节点
Step 3: 每个 Reduce 维护一个大小 100 的最小堆
Step 4: 收集所有 Reduce 的 Top 100，全局再用一次堆排序

优化：Map 端加 Combiner 预聚合，减少 90%+ 传输量
```

#### Q4: MapReduce 适合什么场景？不适合什么？

**适合：**
- 离线日志分析、ETL 流水线
- 数据量巨大（TB~PB），容忍分钟级延迟
- 简单统计类任务（count、sum、group by）

**不适合：**
- 低延迟实时计算（用 Flink/Storm）
- 复杂迭代算法（图计算、机器学习训练，用 Spark）
- 事务型处理（用数据库）

---

### 📊 答题话术模板

**"请描述 MapReduce 的执行流程"**

> MapReduce 分为五个阶段：
> 1. **Input Split**：输入数据切分成若干分片
> 2. **Map**：每个分片在本地并行处理，输出 <key, value> 对
> 3. **Shuffle**：框架按 key 排序、分组，分发到 Reduce 节点
> 4. **Reduce**：对相同 key 的 values 做聚合计算
> 5. **Output**：结果写入 HDFS
>
> 其中 Combiner 可在 Map 端做局部聚合减少网络传输，Partitioner 控制数据分发策略。

**"Spark 比 MapReduce 快在哪里？"**

> 核心差异在**中间结果存储**：MapReduce 每次 Shuffle 都落盘，Spark 尽量缓存在内存。
> 加上 Spark 的 DAG 调度引擎会把多个操作合并成 Stage，减少作业启停开销。
> 迭代场景下，Spark 比 MapReduce 快 10~100 倍。

---

## 三、今日速记卡

| 考点 | 一句话 |
|---|---|
| MapReduce 三阶段 | Map → Shuffle → Reduce |
| Combiner 作用 | Map 端局部聚合，减少网络传输 |
| 数据倾斜解决 | 加盐分散 + 自定义分区 + Combiner |
| Spark 核心优势 | 内存计算 + DAG 调度 + RDD 血统容错 |
| Flink 核心优势 | 真正的流处理，毫秒延迟，事件时间语义 |
| Top K 大数据 | 分片统计 → 局部 Top K → 全局堆排序 |
| WordCount MR 流程 | Split → Map(emit 1) → Shuffle(group by key) → Reduce(sum) |

---

> 💡 **明日预告：** Day 78 — 推荐系统设计（Recommendation System）+ 协同过滤算法
>
> 从「猜你喜欢」到「工业级推荐架构」，明天见。
