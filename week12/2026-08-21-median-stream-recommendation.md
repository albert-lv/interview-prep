# Day 78 · 数据流的中位数 + 推荐系统设计与实时计算

> 🎯 **主题**：大数据处理与搜索引擎 — Week 12 Day 78
> 📅 **日期**：2026-08-21

---

## 一、今日算法题：数据流的中位数

### 题目描述（LeetCode 295）

设计一个数据结构，支持以下操作：
- `void addNum(int num)`：从数据流中添加一个整数。
- `double findMedian()`：返回目前所有元素的中位数。

**示例**：
```
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
输出：[null, null, null, 1.5, null, 2.0]
```

### 核心思路：双堆平衡

数据流中位数 = **实时维护有序序列的中间值**。关键点：

- **大顶堆 `maxHeap`**：存储较小的一半数字，堆顶是这部分的最大值
- **小顶堆 `minHeap`**：存储较大的一半数字，堆顶是这部分的最小值
- **平衡不变式**：`maxHeap.size() == minHeap.size()` 或 `maxHeap.size() == minHeap.size() + 1`

这样中位数：
- 总数为奇数：`maxHeap.top()`（较大的一半多一个，堆顶就是中位数）
- 总数为偶数：`(maxHeap.top() + minHeap.top()) / 2.0`

### 代码实现

```python
import heapq

class MedianFinder:
    def __init__(self):
        # 大顶堆：存储较小的一半（Python 用负数模拟）
        self.small = []
        # 小顶堆：存储较大的一半
        self.large = []

    def addNum(self, num: int) -> None:
        # 先放入大顶堆（small），再将堆顶元素移入小顶堆（large）
        # 这样保证 large 中的所有元素 >= small 中的所有元素
        heapq.heappush(self.small, -num)
        
        # 把 small 中最大的移到 large
        val = -heapq.heappop(self.small)
        heapq.heappush(self.large, val)
        
        # 平衡：small 的大小 >= large（或相等）
        if len(self.large) > len(self.small):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)

    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return float(-self.small[0])
        return (-self.small[0] + self.large[0]) / 2.0
```

### 复杂度分析

| 操作 | 时间复杂度 | 空间复杂度 |
|---|---|---|
| `addNum` | O(log n) — 堆的插入/删除 | O(n) — 存储所有元素 |
| `findMedian` | O(1) — 直接取堆顶 | O(1) |

> 💡 **面试金句**："数据流中位数的本质是维护一个动态有序序列的中间点。双堆通过'小的一半放大顶堆、大的一半放小顶堆'，让堆顶天然就是中位数候选。每次插入最多两次堆操作，均摊 O(log n)。"

### 变种 & Follow-up

1. **如果数据量极大（亿级），内存放不下？**
   - 分段统计 + 桶排序思想：将数值范围分桶，维护每个桶的计数，中位数通过前缀和定位到具体桶，再桶内精确计算
   - 或近似算法：Count-Min Sketch / T-Digest / HyperLogLog

2. **如果要求滑动窗口中位数？**（LeetCode 480）
   - 双堆 + 延迟删除：用哈希表记录待删除元素，堆顶过期时惰性弹出
   - 时间复杂度：O(n log k)，k 为窗口大小

3. **分布式场景？**
   - 各节点本地维护中位数摘要 → 汇聚到中心节点合并
   - 或采用可合并的近似数据结构（如 T-Digest）

---

## 二、面试技巧

### 1. 推荐系统设计（Recommendation System）

推荐系统是大数据+搜索引擎的自然延伸。面试常要求设计"抖音短视频推荐""淘宝商品推荐"等。

#### 核心架构：召回 → 粗排 → 精排 → 重排

```
用户请求
   ↓
┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│   召回层     │ → │   粗排层     │ → │   精排层     │ → │   重排层     │
│  (Recall)   │   │ (Pre-rank)  │   │  (Ranking)  │   │  (Re-rank)  │
└─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘
  千级 → 百级        百级 → 十级       十级 → 个级       多样性/ freshness
```

| 层级 | 职责 | 常用技术 |
|---|---|---|
| **召回** | 从海量物品中快速筛选候选集（千级） | 协同过滤、向量检索（ANN）、兴趣标签、热门兜底 |
| **粗排** | 快速打分排序，筛选更精简候选（百级） | 轻量级模型（LR、GBDT）、双塔模型预打分 |
| **精排** | 精细预估 CTR/CVR，产出排序分 | 深度模型（Wide&Deep、DeepFM、DIN） |
| **重排** | 业务规则介入：多样性、新鲜度、疲劳度 | MMR、规则过滤、A/B test 分流 |

#### 召回策略详解

**协同过滤（Collaborative Filtering）**：
- **User-CF**：相似用户喜欢的物品推荐给你。适合发现惊喜，计算量大
- **Item-CF**：用户历史喜欢的物品的相似物品。稳定、可解释性强，工业界主流
- **矩阵分解（MF/SVD++）**：将用户-物品矩阵分解为低维隐向量，缓解稀疏性

**向量召回（Embedding + ANN）**：
- 用户和物品都编码为向量，用近似最近邻（ANN）快速检索
- 工具：Faiss、Milvus、Elasticsearch（dense_vector）
- 双塔模型：用户塔 + 物品塔，分别产出向量，内积/余弦相似度衡量匹配度

**热门/趋势兜底**：
- 新用户冷启动时，用热门、趋势、编辑精选兜底

#### 冷启动问题

| 场景 | 解决方案 |
|---|---|
| **新用户** | 引导选择兴趣标签、人口统计推荐、热门兜底、社交关系导入 |
| **新物品** | 内容冷启动（基于内容相似度推给相似用户）、探索机制（EE 问题，ε-greedy/UCB/Thompson Sampling） |
| **新系统** | 众测、种子用户、运营内容填充 |

> 💬 **面试官问**："如何解决推荐系统的信息茧房问题？"
>
> 🎯 **答**：三管齐下：① 重排层引入多样性约束（MMR 算法，在相关性和多样性之间 trade-off）；② 引入探索机制（多臂老虎机，偶尔推一些"探索性"内容）；③ 加入人工编辑的"破圈"内容池，定期注入非个性化内容。

### 2. 实时计算/流处理架构

大数据不只是批处理，实时性要求催生了流处理框架。

#### Flink vs Spark Streaming vs Storm

| 维度 | Apache Flink | Spark Streaming | Apache Storm |
|---|---|---|---|
| **处理模型** | 原生流处理（Native Streaming） | 微批（Micro-batch） | 原生流处理 |
| **延迟** | 毫秒级 | 秒级（微批间隔） | 毫秒级 |
| **语义保证** | Exactly-Once | Exactly-Once（Checkpoint） | At-Least-Once / At-Most-Once |
| **状态管理** | 内置 State（Keyed State / Operator State） | 需借助外部存储 | 需自行实现 |
| **窗口支持** | 丰富（时间/会话/计数窗口） | 基于时间窗口 | 基础窗口 |
| **适用场景** | 实时风控、实时推荐、IoT | 准实时统计、日志分析 | 已逐渐被 Flink 替代 |

> 🔥 **结论**：新系统选型**优先 Flink**，生态成熟、语义强、延迟低。

#### 核心概念

**时间语义**：
- **Event Time**：事件产生的时间（业务时间，最准确）
- **Processing Time**：数据被处理的时间（机器时间，可能乱序）
- **Ingestion Time**：数据进入 Flink 的时间

> 乱序数据用 **Watermarks（水位线）** 处理：允许一定延迟到达的数据参与窗口计算。

**窗口类型**：
- 滚动窗口（Tumbling Window）：固定大小，不重叠
- 滑动窗口（Sliding Window）：固定大小，可重叠
- 会话窗口（Session Window）：根据活动间隙动态划分

**Exactly-Once 实现**：
- Checkpoint 机制：周期性快照（Barrier 对齐）
- 两阶段提交（2PC）：Sink 端配合（如 Kafka 事务）

#### 实时计算典型场景

```
用户行为日志 → Kafka → Flink 实时聚合（窗口统计）→ Redis → 实时推荐/实时大屏
                ↓
         Flink CEP（复杂事件处理）→ 实时风控（检测异常模式）
```

> 💬 **面试官问**："实时计算和批处理怎么统一？"
>
> 🎯 **答**：Apache Flink 的 **批流一体** 理念——底层用同一套引擎，批处理视为有界流（Bounded Stream）。或者 Lambda 架构（批层+速层，结果合并），但维护成本高，现在更推荐 Kappa 架构（纯流处理，用重放机制支持离线分析）。

### 3. 大数据面试高频速查

| 问题 | 一句话速答 |
|---|---|
| **Hive 和 MySQL 的区别？** | Hive 是基于 Hadoop 的数据仓库，用类 SQL（HQL）做离线批处理；MySQL 是 OLTP 数据库，面向事务 |
| **HDFS 为什么不适合小文件？** | NameNode 内存中每个文件/目录都是一个对象，小文件过多会撑爆 NameNode 内存，且 MapReduce 启动任务开销大于处理本身 |
| **数据倾斜怎么发现和解决？** | 发现：看任务日志，某些 Reducer 执行时间远大于其他。解决：加盐（随机前缀）打散热点 Key、局部聚合（Combiner）、过滤热点数据单独处理 |
| **Kafka 如何保证消息不丢失？** | Producer ack=all + 重试；Broker 多副本 ISR 机制；Consumer 手动提交 offset，处理完再 commit |
| **ClickHouse 为什么快？** | 列式存储（OLAP 友好）、向量化执行、MergeTree 引擎预排序 + 稀疏索引、数据压缩率高 |

---

## 三、今日小结

| 项目 | 要点 |
|---|---|
| **算法题** | 数据流中位数 = 双堆平衡（大顶堆+小顶堆），add O(log n)，query O(1)。变种：滑动窗口中位数、分布式近似中位数 |
| **系统设计** | 推荐系统四层架构（召回→粗排→精排→重排），冷启动三方案，信息茧房用多样性+探索破解 |
| **流处理** | Flink 原生流处理毫秒级延迟，Watermarks 处理乱序，Checkpoint 实现 Exactly-Once |
| **面试话术** | "双堆的本质是把有序序列从中间劈开，两边各用一个堆维护，堆顶就是中位数候选" |

---

> 📌 **明日预告**：Week 12 最后一天 — 大数据面试综合实战 + 经典系统设计追问速查表 🔥
