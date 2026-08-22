# Day 79 — 区间和的个数 + OLAP 与实时数仓架构

> 日期：2026-08-22  
> 主题：大数据处理与搜索引擎 🔍（Week 12 收官日）  
> 难度：🔴 Hard + 🏗️ System Design

---

## 1. 今日算法题：区间和的个数（Count of Range Sum）

**LeetCode 327** — 给定一个整数数组 `nums` 和两个整数 `lower`、`upper`，求数组中所有子数组和落在 `[lower, upper]` 范围内的个数。

### 题目理解

```
输入: nums = [-2, 5, -1], lower = -2, upper = 2
输出: 3
解释: 子数组和满足条件的有:
      [-2]        → -2 ✓
      [-2, 5, -1] → 2  ✓
      [-1]        → -1 ✓
```

> 💡 **为什么是大数据题？** 前缀和是时间序列分析的基石，归并排序的分治思想与 MapReduce 一脉相承。数据量达到 10^5 时，暴力 O(n²) 会超时，必须用分治或树状数组优化到 O(n log n)。

### 核心思路：前缀和 + 归并排序分治

子数组和 `sum(i, j) = prefix[j+1] - prefix[i]`。问题转化为：

> **找满足 `lower ≤ prefix[j] - prefix[i] ≤ upper` 且 `i < j` 的数对个数**

**归并排序分治框架**：
1. 先递归处理左右两半，各自统计区间内的合法数对
2. **关键**：统计左半的 `prefix[i]` 和右半的 `prefix[j]`（`i` 在左，`j` 在右）组成的跨区合法数对
3. 合并两个有序数组（经典归并）

**跨区统计技巧**：
- 右半部分已排序，对于左半每个 `prefix[i]`，找右半中满足 `prefix[i] + lower ≤ prefix[j] ≤ prefix[i] + upper` 的 `j` 个数
- 用双指针/二分，因为数组有序，滑动窗口即可

```python
def countRangeSum(nums, lower, upper):
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    def mergeSort(left, right):
        if left >= right:
            return 0
        mid = (left + right) // 2
        count = mergeSort(left, mid) + mergeSort(mid + 1, right)
        
        # 统计跨区数对: i in [left, mid], j in [mid+1, right]
        # 找满足 lower <= prefix[j] - prefix[i] <= upper 的个数
        # 即 prefix[i] + lower <= prefix[j] <= prefix[i] + upper
        j_low = mid + 1
        j_high = mid + 1
        for i in range(left, mid + 1):
            # 找到右半中 >= prefix[i] + lower 的起始位置
            while j_low <= right and prefix[j_low] < prefix[i] + lower:
                j_low += 1
            # 找到右半中 <= prefix[i] + upper 的结束位置
            while j_high <= right and prefix[j_high] <= prefix[i] + upper:
                j_high += 1
            count += j_high - j_low
        
        # 经典归并排序合并
        sorted_prefix = []
        p1, p2 = left, mid + 1
        while p1 <= mid and p2 <= right:
            if prefix[p1] <= prefix[p2]:
                sorted_prefix.append(prefix[p1]); p1 += 1
            else:
                sorted_prefix.append(prefix[p2]); p2 += 1
        sorted_prefix.extend(prefix[p1:mid+1])
        sorted_prefix.extend(prefix[p2:right+1])
        for i, val in enumerate(sorted_prefix):
            prefix[left + i] = val
        
        return count
    
    return mergeSort(0, n)
```

### 复杂度分析

| 指标 | 值 | 说明 |
|------|-----|------|
| 时间 | **O(n log n)** | 归并排序框架，每层 O(n)，共 log n 层 |
| 空间 | **O(n)** | 归并时的临时数组 |

> ⚠️ **常见坑**：
> - `j_low` 和 `j_high` 不会回退，因为 `prefix[i]` 递增，窗口只会右移
> - 前缀和数组长度是 `n+1`，`prefix[0] = 0` 表示空前缀
> - 如果用树状数组 + 离散化，代码更短但常数更大

### 🎤 面试官追问

> **Q: 数据流版本怎么做？**（数组变成实时到达的数据流）
> 
> A: 需要**有序数据结构**维护历史前缀和，新数据到达时查询 `current_prefix - upper` 到 `current_prefix - lower` 范围内的历史前缀和个数。用 **树状数组/线段树 + 动态离散化**，或 **Treap/SBT 平衡树**，单次操作 O(log n)。

> **Q: 如果 nums 长度 10^7，内存不够怎么办？**
> 
> A: 归并排序可以改外排序，但统计跨区数对需要随机访问。更实际的方案：分块处理，每块内用归并排序，块间用近似算法（如 Count-Min Sketch 做频率估计）。

---

## 2. 面试技巧：OLAP 引擎与实时数仓架构

Week 12 收官，今天把大数据处理的最后一块拼图补上：**OLAP（在线分析处理）与实时数仓**。这是电商、广告、金融等场景面试高频题。

### 2.1 架构演进：Lambda vs Kappa

```
┌─────────────────────────────────────────────────────────────┐
│                    Lambda 架构                               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   数据源 ──┬──> [批处理层] ──> 批处理视图 (Hive/Spark)       │
│           │           │                                     │
│           │           └──> 全量数据，T+1 更新                │
│           │                                                 │
│           └──> [流处理层] ──> 实时视图 (Flink/Kafka)         │
│                       │                                     │
│                       └──> 增量数据，毫秒级延迟              │
│                                                             │
│           [服务层] ──> 合并批处理视图 + 实时视图              │
│                                                             │
└─────────────────────────────────────────────────────────────┘

问题：两套代码、两套存储、维护成本高，批流结果可能不一致
```

```
┌─────────────────────────────────────────────────────────────┐
│                    Kappa 架构                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   数据源 ──> [Kafka 日志] ──> [流处理层: Flink] ──> 统一视图  │
│                                                             │
│   核心思想：批处理是流处理的特例（有限流），只用一套流引擎   │
│                                                             │
│   重放机制：需要重算历史数据时，从 Kafka 指定 offset 重消费  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**面试速答模板**：
> "Lambda 是批流分离，适合已有批处理基础设施的迁移场景；Kappa 是批流统一，维护成本更低，但对消息队列的持久性和重放能力要求高。现在工业界主流是 **Kappa + 增量批处理** 的混合模式。"

### 2.2 OLAP 引擎选型：ClickHouse vs Doris vs Druid

| 维度 | ClickHouse | Apache Doris | Apache Druid |
|------|-----------|-------------|--------------|
| 存储模型 | **列式存储**，MergeTree | MPP + 列式存储 | 列式 + 预聚合 |
| 写入性能 | 极高（批量写入百万行/秒） | 高，支持实时导入 | 高，专为实时设计 |
| 查询性能 | 单表查询王者 | 多表 Join 优秀 | 预聚合场景快 |
| 典型场景 | 日志分析、时序数据 | 统一数仓、BI报表 | 实时指标看板 |
| SQL 兼容 | 大部分兼容 | 高度兼容 MySQL 协议 | 有限，需学习 DSL |

**列式存储为什么快？**
1. **只读需要的列**：`SELECT city, COUNT(*) FROM logs` 只读两列，不需要加载整行
2. **更好的压缩**：同列数据类型相同，压缩比行式高 5-10 倍
3. **向量化执行**：SIMD 指令一次处理一批数据，而不是一行一行
4. **数据局部性友好**：CPU Cache 命中率高

### 2.3 实时数仓分层（经典五层）

```
数据源 (日志/DB/Binlog)
    │
    ▼
┌──────────┐   ODS (Operational Data Store)  原始数据贴源层
│ Kafka    │   格式: JSON/Protobuf, 保留 3-7 天
└────┬─────┘
     │
     ▼
┌──────────┐   DWD (Data Warehouse Detail)   明细数据层
│ Flink    │   清洗、标准化、维表关联、生成统一事件
└────┬─────┘
     │
     ▼
┌──────────┐   DWS (Data Warehouse Summary)  轻度汇总层
│ Flink/   │   按时间窗口聚合: 1min/5min/1h/1d
│ Spark    │   输出: 各维度 UV/PV/订单量/GMV
└────┬─────┘
     │
     ▼
┌──────────┐   ADS (Application Data Service) 应用数据层
│ Doris/   │   面向业务场景的宽表，直接供给报表/接口
│ClickHouse│
└────┬─────┘
     │
     ▼
┌──────────┐   DIM (Dimension) 维表层
│ MySQL/   │   用户信息、商品信息、地理位置等缓慢变化维
│ HBase    │
└──────────┘
```

### 2.4 面试高频追问速查

**Q: 实时数仓如何保证 Exactly-Once？**
> Flink Checkpoint + Kafka 事务性 Producer + 幂等消费端。Checkpoint Barrier 对齐保证状态一致性，两阶段提交（2PC）保证输出端不重复。

**Q: ClickHouse 的 MergeTree 为什么适合时序数据？**
> 数据按主键排序写入，后台异步合并（Merge）小 part。查询时利用主键索引和稀疏索引（granularity）快速跳过无关数据块。分区按时间切分，方便 TTL 过期删除。

**Q: 预聚合 vs 物化视图，怎么选？**
> 预聚合（Druid/Rollup）适合维度少、指标固定的场景，牺牲灵活性换查询速度。物化视图是查询结果的缓存，适合查询模式相对固定但维度多的场景。ClickHouse 的 Projection 是两者的折中。

**Q: 数据倾斜在实时数仓里怎么解决？**
> - ODS 层：Kafka Partition Key 加盐打散热点 Key
> - DWD 层：Flink 两阶段聚合（LocalAgg + GlobalAgg）
> - DWS 层：预先把大 Key 拆分到多个子任务，或单独处理

### 2.5 大数据面试「一句话速答」金句

| 问题 | 一句话答案 |
|------|-----------|
| 为什么 Kafka 比 RabbitMQ 吞吐高？ | 顺序写磁盘 + 零拷贝 + 批量压缩 + 分区并行 |
| ES 比 MySQL 搜索快的原因？ | 倒排索引 + 分片并行 + 内存缓存 + 不需要回表 |
| Flink 比 Spark Streaming 延迟低？ | 纯流处理 vs 微批，事件时间语义 + 轻量 Checkpoint |
| ClickHouse 为什么单表查询快？ | 列式存储 + 向量化执行 + MergeTree 稀疏索引 |
| 数据湖 vs 数据仓库？ | 仓是结构化 schema-on-write，湖是原始存 schema-on-read |

---

## 3. 今日小结

| 项目 | 内容 |
|------|------|
| 🧮 算法 | 区间和的个数 — 前缀和 + 归并排序分治 O(n log n) |
| 🏗️ 系统设计 | OLAP 引擎选型、Lambda/Kappa 架构、实时数仓五层模型 |
| 🎯 面试要点 | 列式存储原理、预聚合策略、Exactly-Once 保障、数据倾斜处理 |

> 📌 **Week 12 完结撒花** 🎉
>
> 这四周我们走过了：数据库与存储深度（Week 11）→ 操作系统与网络底层（Week 10）→ 系统架构与工程实践（Week 9）→ 大数据处理与搜索引擎（Week 12）。
>
> 下一个阶段，准备进入 **机器学习系统与 AI 工程** 或 **综合实战模拟面试**。Stay tuned 🔥

---

## 参考

- [LeetCode 327. Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/)
- [ClickHouse 官方文档 — MergeTree](https://clickhouse.com/docs/en/engines/table-engines/mergetree-family/mergetree)
- [Flink 实时数仓实践](https://nightlies.apache.org/flink/flink-docs-stable/)
- [Kappa Architecture by Jay Kreps](https://www.oreilly.com/radar/questioning-the-lambda-architecture/)
