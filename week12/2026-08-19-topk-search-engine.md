# Day 76 · 数组中的第K个最大元素 + 搜索引擎设计基础

> **Week 12 主题：大数据处理与搜索引擎 🔍**
> 
> 日期：2026-08-19 | 进度：Week 12 Day 1

---

## 🧩 今日算法题：数组中的第K个最大元素

### 题目描述

给定整数数组 `nums` 和整数 `k`，请返回数组中第 `k` 个最大的元素。

请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。

**示例：**
```
输入: nums = [3,2,1,5,6,4], k = 2
输出: 5

输入: nums = [3,2,3,1,2,4,5,5,6], k = 4
输出: 4
```

**约束：**
- `1 <= k <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

---

### 解题思路

这道题是面试中**手撕代码的最高频题之一**，考察点非常明确：

| 方法 | 时间复杂度 | 空间复杂度 | 适用场景 |
|---|---|---|---|
| **排序** | O(n log n) | O(1) / O(log n) | 简单直接，面试保底写法 |
| **最小堆** | O(n log k) | O(k) | 数据流场景、K 相对较小 |
| **快速选择** | O(n) 平均 / O(n²) 最坏 | O(1) | **面试最优解**，原地 partition |

**面试时推荐的回答顺序：**
1. 先说排序法（保底，证明你会）
2. 再说最小堆（展示你知道优化方向）
3. 最后讲快速选择（展示你追求最优）

---

### 方法一：快速选择（Quick Select）⭐ 面试最优解

核心思想：利用快速排序的 partition 操作，每次将数组分为「大于 pivot」和「小于 pivot」两部分，然后只递归处理包含目标的那一半。

```python
import random

class Solution:
    def findKthLargest(self, nums: list[int], k: int) -> int:
        """
        快速选择：第 k 大 = 升序排列下标为 n-k 的元素
        """
        n = len(nums)
        target_idx = n - k  # 第 k 大在升序中的下标
        
        return self.quickSelect(nums, 0, n - 1, target_idx)
    
    def quickSelect(self, nums, left, right, target_idx):
        if left == right:
            return nums[left]
        
        # 随机选 pivot，避免最坏情况
        pivot_idx = random.randint(left, right)
        pivot_idx = self.partition(nums, left, right, pivot_idx)
        
        if pivot_idx == target_idx:
            return nums[target_idx]
        elif pivot_idx < target_idx:
            return self.quickSelect(nums, pivot_idx + 1, right, target_idx)
        else:
            return self.quickSelect(nums, left, pivot_idx - 1, target_idx)
    
    def partition(self, nums, left, right, pivot_idx):
        """三路划分 / Lomuto 分区"""
        pivot = nums[pivot_idx]
        # 把 pivot 移到末尾
        nums[pivot_idx], nums[right] = nums[right], nums[pivot_idx]
        
        store_idx = left
        for i in range(left, right):
            if nums[i] < pivot:
                nums[store_idx], nums[i] = nums[i], nums[store_idx]
                store_idx += 1
        
        # pivot 归位
        nums[right], nums[store_idx] = nums[store_idx], nums[right]
        return store_idx
```

**Go 版本（面试手撕推荐）：**

```go
package main

import "math/rand"

func findKthLargest(nums []int, k int) int {
    n := len(nums)
    targetIdx := n - k
    return quickSelect(nums, 0, n-1, targetIdx)
}

func quickSelect(nums []int, left, right, targetIdx int) int {
    if left == right {
        return nums[left]
    }
    
    pivotIdx := left + rand.Intn(right-left+1)
    pivotIdx = partition(nums, left, right, pivotIdx)
    
    if pivotIdx == targetIdx {
        return nums[targetIdx]
    } else if pivotIdx < targetIdx {
        return quickSelect(nums, pivotIdx+1, right, targetIdx)
    } else {
        return quickSelect(nums, left, pivotIdx-1, targetIdx)
    }
}

func partition(nums []int, left, right, pivotIdx int) int {
    pivot := nums[pivotIdx]
    nums[pivotIdx], nums[right] = nums[right], nums[pivotIdx]
    
    storeIdx := left
    for i := left; i < right; i++ {
        if nums[i] < pivot {
            nums[storeIdx], nums[i] = nums[i], nums[storeIdx]
            storeIdx++
        }
    }
    
    nums[right], nums[storeIdx] = nums[storeIdx], nums[right]
    return storeIdx
}
```

---

### 方法二：最小堆（Priority Queue）

适合数据流场景（元素逐个到来，无法一次性拿到全部数据）。

```python
import heapq

class Solution:
    def findKthLargest(self, nums: list[int], k: int) -> int:
        """维护大小为 k 的最小堆，堆顶就是第 k 大"""
        min_heap = []
        for num in nums:
            heapq.heappush(min_heap, num)
            if len(min_heap) > k:
                heapq.heappop(min_heap)
        return min_heap[0]  # 堆顶
```

---

### 复杂度分析

| 方法 | 平均时间 | 最坏时间 | 空间 | 稳定性 |
|---|---|---|---|---|
| 快速选择 | O(n) | O(n²) | O(1) | ❌ 不稳定 |
| 最小堆 | O(n log k) | O(n log k) | O(k) | ✅ 稳定 |
| 排序 | O(n log n) | O(n log n) | O(1)~O(log n) | 取决于排序算法 |

**为什么是 O(n) 平均？**

每次 partition 后只处理一半数组，形成等比数列：
```
n + n/2 + n/4 + n/8 + ... < 2n = O(n)
```

---

### 🔥 Follow-up 追问

> **面试官：如果数据量是 10 亿级别，内存放不下怎么办？**

**你：** 这是典型的 **外部排序 + 堆** 问题：

1. **分块处理**：将 10 亿数据分成 1000 个文件，每个 100MB（内存能放下）
2. **局部 Top K**：每个文件用最小堆求出局部 Top K
3. **全局归并**：将 1000 个局部 Top K（共 1000×K 个元素）再用一次堆归并，得到全局 Top K

> **面试官：如果是找第 K 大，而不是 Top K 个元素呢？**

**你：** 可以用 **计数排序思想**（如果数据范围有限）：
- 维护一个频率数组，统计每个值出现的次数
- 从大到小累加频率，找到第 K 个位置
- 时间 O(n)，空间 O(V)（V 是值域大小）

> **面试官：快速选择的最坏情况怎么避免？**

**你：** 
- **随机 pivot**：概率上避免最坏情况，面试写这个就够了
- **Median-of-Medians**：理论上保证 O(n)，但常数太大，工程不实用
- **三路划分**：处理大量重复元素时避免退化

---

## 🎤 面试技巧：搜索引擎设计基础

### 核心问题

设计一个搜索引擎（如 Google、百度、Elasticsearch），支持：
1. 海量网页的抓取与存储
2. 用户查询的快速响应
3. 结果按相关性排序

---

### 系统架构总览

```
┌─────────────────────────────────────────────────────────────┐
│                        用户查询层                            │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐        │
│  │ Query   │  │ 拼写纠错 │  │ 自动补全 │  │ 查询改写 │        │
│  │ Parser  │  │         │  │         │  │         │        │
│  └────┬────┘  └─────────┘  └─────────┘  └─────────┘        │
│       │                                                    │
│       ▼                                                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              检索服务（Search Service）               │   │
│  │  ┌─────────────┐      ┌─────────────────────────┐   │   │
│  │  │ 倒排索引查询 │  ──▶ │  相关性排序 + 过滤聚合   │   │   │
│  │  │ (Index Node)│      │  (Ranker / Aggregator)  │   │   │
│  │  └─────────────┘      └─────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────┘   │
│       │                                                    │
│       ▼                                                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              索引层（Index Layer）                    │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │   │
│  │  │ 倒排索引     │  │ 正排索引     │  │ 文档向量     │  │   │
│  │  │ Inverted    │  │ Forward     │  │ (语义搜索)   │  │   │
│  │  │ Index       │  │ Index       │  │             │  │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
│       ▲                                                    │
│       │                                                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              爬虫与索引构建（Crawler & Indexer）       │   │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌────────┐  │   │
│  │  │ 分布式   │  │ 内容提取 │  │ 分词     │  │ 索引构建│  │   │
│  │  │ 爬虫     │  │         │  │         │  │         │  │   │
│  │  └─────────┘  └─────────┘  └─────────┘  └────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

### 1. 倒排索引（Inverted Index）— 搜索引擎的心脏

**核心思想**：从「文档 → 词」的映射，反转为「词 → 文档列表」的映射。

```
文档集合:
  Doc1: "OpenClaw is an AI agent platform"
  Doc2: "AI agents are changing how we work"
  Doc3: "OpenClaw enables powerful AI workflows"

倒排索引:
  "openclaw"   → [Doc1, Doc3]
  "ai"         → [Doc1, Doc2, Doc3]
  "agent"      → [Doc1, Doc2]
  "platform"   → [Doc1]
  ...
```

**索引结构优化**：

| 结构 | 说明 | 适用 |
|---|---|---|
| **Posting List** | 文档 ID 的有序列表 | 基础查询 |
| **Skip List** | 在 Posting List 上加跳表指针 | 加速倒排求交 |
| **Frame of Reference** | 文档 ID 差值编码（Delta Encoding） | 压缩存储 |
| **Roaring Bitmap** | 用容器数组/位图混合存储文档集合 | 快速集合运算 |

---

### 2. 查询处理流程

**Step 1: 查询解析**
```
用户输入: "AI agent 工具"
         ↓
分词: ["ai", "agent", "工具"]
         ↓
查询意图识别: 信息型查询（不是导航型/交易型）
```

**Step 2: 倒排求交（AND/OR/NOT）**
```
查询 "AI agent":
  "ai"    → [Doc1, Doc2, Doc3]
  "agent" → [Doc1, Doc2]
  
  AND 求交 → [Doc1, Doc2]
```

**优化**：按 Posting List 长度从小到大求交，减少计算量。

**Step 3: 相关性排序（Ranking）**

经典 **TF-IDF + BM25**：

```
TF(t,d) = 词 t 在文档 d 中出现次数
IDF(t) = log(文档总数 / 包含词 t 的文档数)

BM25 更精细，考虑了文档长度归一化：
  score(d,q) = Σ IDF(qi) · (tf · (k1+1)) / (tf + k1 · (1-b+b·|d|/avgdl))
```

现代搜索引擎还会加入 **向量语义相似度**（如 BERT embedding 的 cosine similarity）。

---

### 3. 分布式搜索架构

**数据分片（Sharding）**：

```
总文档: 100 亿
分片数: 1000 个
每片:   1000 万文档

分片策略:
  - 按文档 ID 哈希（均匀分布）
  - 按时间范围（适合日志搜索）
  - 按主题/领域（减少跨片查询）
```

**查询路由**：

```
用户查询 "AI agent"
         ↓
    Coordinator Node
         ↓
  广播到所有 1000 个分片（或按路由键选择子集）
         ↓
  每个分片返回本地 Top N 结果
         ↓
    Coordinator 全局归并排序
         ↓
  返回最终 Top K 给用户
```

**关键优化**：
- **副本（Replica）**：每个分片 1 主 + 2 从，提升读吞吐
- **查询结果缓存**：热点查询直接返回缓存
- **路由裁剪**：如果查询包含特定标签/时间范围，只查相关分片

---

### 4. 面试高频追问

> **面试官：Elasticsearch 为什么比 MySQL 的全文搜索快？**

**三层回答：**
1. **索引结构**：ES 用倒排索引，直接词 → 文档映射；MySQL LIKE '%xxx%' 是逐行扫描
2. **分布式**：ES 天生分片+副本，水平扩展；MySQL 单节点瓶颈明显
3. **写入策略**：ES 近实时（refresh interval），牺牲强一致性换性能；MySQL 保证 ACID

> **面试官：10 亿文档，用户搜一个词，怎么保证 100ms 内返回？**

**你：**
1. **索引全内存**：倒排索引常驻内存，Posting List 用压缩 + 缓存
2. **查询剪枝**：先查最精确的词，再用 skip list 快速跳过无关文档
3. **并行查询**：1000 分片并行检索，Coordinator 异步收集结果
4. **结果缓存**：Top 100 热点查询结果缓存，命中率可达 80%+
5. **预排序**：文档离线计算好 quality score，查询时只需算 query 相关分

> **面试官：搜索引擎怎么保证新内容尽快被搜到？**

**你：** 近实时（Near Real-Time）架构：
- 新文档先写入 **内存缓冲（In-Memory Buffer）**
- 每 1 秒（可配置）refresh 一次，生成新的 **segment**
- segment 先对用户可见，后台异步 merge 到更大的 segment
- 牺牲秒级一致性，换取写入吞吐和查询实时性

---

### 5. 一句话速记

| 概念 | 一句话 |
|---|---|
| 倒排索引 | 词 → 文档列表，搜索引擎的心脏 |
| Posting List | 包含某词的所有文档 ID 有序列表 |
| BM25 | 比 TF-IDF 更精细的相关性打分算法 |
| 分片 | 数据水平拆分，每片独立索引和查询 |
| 近实时 | 写入后秒级可见，不是立即可见 |
| 向量搜索 | 用 embedding + 近似最近邻（ANN）实现语义搜索 |

---

## 📝 今日 Checklist

- [ ] 能手写快速选择（Quick Select）的 partition 逻辑
- [ ] 能说出 Top K 问题的 3 种解法及适用场景
- [ ] 能画出搜索引擎的架构图（爬虫 → 索引 → 查询 → 排序）
- [ ] 能解释倒排索引的结构和查询流程
- [ ] 能回答「ES 为什么比 MySQL 搜索快」

---

## 🔗 相关阅读

- 上一篇：[Day 75 — 常数时间插入删除获取随机元素 + 存储引擎与缓存一致性](week11/2026-08-18-storage-engine-cache.md)
- 系统设计系列：[Day 52 — 设计短链服务](week08/2026-07-28-short-url.md) | [Day 53 — 设计 Feed 流](week08/2026-07-29-feed-system.md)

> **明日预告**：Day 77 — 大数据处理框架（MapReduce / Spark）+ 海量数据去重/排序
