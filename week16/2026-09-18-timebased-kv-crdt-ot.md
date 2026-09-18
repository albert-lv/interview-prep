# Day 106 — 基于时间的键值存储 + 副本一致性与冲突解决（CRDT / OT）

> 📅 2026-09-18 | Week 16 Day 6 | 主题：分布式系统核心协议 🔗

---

## 今日算法题

### 基于时间的键值存储（Time Based Key-Value Store）

**题目来源**：[LeetCode 981](https://leetcode.cn/problems/time-based-key-value-store/)

**描述**：设计一个支持多版本的键值存储，支持以下两个操作：

- `set(key, value, timestamp)`：存储键 `key` 和值 `value`，并给定时间戳 `timestamp`（**严格递增**，即同一个 key 的 timestamp 单调上升）。
- `get(key, timestamp)`：返回之前调用 `set(key, value, timestamp_prev)` 所存储的值，其中 `timestamp_prev <= timestamp`。如果存在多个这样的值，返回 `timestamp_prev` 最大的那个；如果没有符合条件的值，返回空字符串 `""`。

**示例**：
```
TimeMap timeMap = new TimeMap();
timeMap.set("foo", "bar", 1);        // 存储 foo -> bar @t=1
timeMap.get("foo", 1);               // 返回 "bar"
timeMap.get("foo", 3);               // 返回 "bar"（t=1 是 <=3 的最大时间戳）
timeMap.set("foo", "bar2", 4);       // 存储 foo -> bar2 @t=4
timeMap.get("foo", 4);               // 返回 "bar2"
timeMap.get("foo", 5);               // 返回 "bar2"
```

**约束**：`1 <= set/get 调用次数 <= 10^5`，`1 <= timestamp <= 10^7`，key 由小写字母组成，value 由小写字母和数字组成。

---

### 解题思路：多版本存储 + 二分查找

#### 本质：这道题就是分布式存储的「多版本并发控制（MVCC）」骨架

每个 key 维护一条**按时间戳严格递增的有序版本链**：

```
foo → [ (t=1, "bar"), (t=4, "bar2"), (t=9, "bar3") ]
```

`get(key, t)` 的含义：**在版本链中找最后一个时间戳 `<= t` 的版本**——这正是数据库里「快照读」的语义：

> 在分布式副本冲突解决里，这就是 **LWW（Last-Write-Wins，最后写入获胜）** 的核心操作：**给定时间点，取最后一次写入的值**。理解了这道题，就理解了 DynamoDB/Cassandra 读取路径的一半。

#### 为什么能二分：timestamp 严格递增是关键

题目保证同一 key 的 `set` 时间戳严格递增，所以每个 key 的版本链**天然有序**，无需额外排序：

```
target = 最后一个 timestamp <= t 的版本
     = upper_bound(版本链, t) 的前一个元素
```

- 无版本 `<= t`（target 指向第一个元素之前）→ 返回 `""`。
- `get` 全是二分查找，最坏 `O(log n)`。

#### 两种数据结构的取舍

| 方案 | set | get | 评价 |
|---|---|---|---|
| `map[key] → list<(ts, val)>` + 二分 | O(1) 追加 | O(log n) | ✅ 最优，题目保证时间戳递增，尾部追加即可 |
| `map[key] → TreeMap<ts, val>` | O(log n) | O(log n) | 红黑树，时间戳不保证递增时的通用解 |

> 💡 **面试官 follow-up**：「如果时间戳不保证有序怎么办？」→ 用平衡树（TreeMap / `std::map`），或用 `map[key] → vector` + 插入时保持有序（O(n) 移动），再追问就是工程权衡。

---

### 代码实现

```go
package main

import "sort"

// ========== 核心结构：多版本键值存储 ==========

type pair struct {
	ts  int
	val string
}

type TimeMap struct {
	store map[string][]pair // key → 按 ts 严格递增的版本链
}

func Constructor() TimeMap {
	return TimeMap{store: make(map[string][]pair)}
}

// set: 题目保证同一 key 的 timestamp 严格递增 → 尾部追加 O(1)
func (tm *TimeMap) Set(key, value string, timestamp int) {
	tm.store[key] = append(tm.store[key], pair{timestamp, value})
}

// get: 二分找最后一个 ts <= timestamp 的版本 —— 快照读语义
func (tm *TimeMap) Get(key string, timestamp int) string {
	versions := tm.store[key]
	if len(versions) == 0 {
		return ""
	}
	// sort.Search: 找第一个 ts > timestamp 的下标
	idx := sort.Search(len(versions), func(i int) bool {
		return versions[i].ts > timestamp
	})
	// idx 的前一个元素就是最后一个 ts <= timestamp 的版本
	if idx == 0 {
		return "" // 没有任何版本 <= timestamp
	}
	return versions[idx-1].val
}
```

```cpp
// C++ 版本：一个 map + lower_bound，十行搞定
class TimeMap {
    unordered_map<string, vector<pair<int, string>>> store;
public:
    void set(string key, string value, int timestamp) {
        store[key].push_back({timestamp, value});   // 严格递增，直接追加
    }
    string get(string key, int timestamp) {
        auto& vs = store[key];
        // 找第一个 ts > timestamp，prev 就是目标
        auto it = upper_bound(vs.begin(), vs.end(), make_pair(timestamp, ""s));
        return it == vs.begin() ? "" : prev(it)->second;
    }
};
```

#### ⚠️ 常见坑

1. **二分边界写反**：找的是「最后一个小于等于」，不是「第一个大于等于」。口诀：`upper_bound` 之后 `prev`，判 `begin`。
2. **key 不存在**：`map` 访问不存在的 key 会返回空切片，`idx == 0` 直接返回 `""`，别忘了这步。
3. **同时间戳覆盖**：若 set 允许同 ts（题目不允许），要先检查尾部再决定是否追加。

---

### 复杂度分析

| 操作 | 时间复杂度 | 空间复杂度 |
|---|---|---|
| `set` | O(1) 均摊（尾部追加，哈希定位） | O(N)，N 为所有版本总数 |
| `get` | **O(log K)**，K 为该 key 的版本数 | O(1) 额外 |

---

### 题目变种（面试官大概率追问）

- **「`set` 的时间戳不再保证递增」** → TreeMap / 有序数组插入保持有序，O(log n) → O(n) 移动；或批量构建时先排序。
- **「如何支持删除某个版本？」** → 版本链上二分定位后删除；若是头部版本还要处理 get 语义。
- **「支撑 100 万 QPS 怎么设计？」** → 热 key 的版本链缓存、读多写少加 LRU、分片。
- **「分布式场景下，两个副本同时 set 同一个 key 怎么办？」** → 🎯 **这就是今天的面试主题：副本一致性与冲突解决（CRDT / OT）**，见下文。

---

## 面试技巧：副本一致性与冲突解决（CRDT / OT）

> 🎤 这是 Week 16 分布式协议的收官前两讲。前面五天讲了 Raft（共识）、CAP（权衡）、Paxos（理论）、Gossip（传播）、分布式锁（互斥）——今天回答最后一个问题：**当系统选择了 AP（可用性优先），副本之间数据冲突了怎么办？**

### 一、先立框架：冲突解决的三条路线

```
副本数据冲突
├── 1. 避免冲突：强一致协议（Raft/Paxos）——串行化，根本没冲突（Day 101-103）
├── 2. 检测 + 解决：版本向量检测并发写，业务层/策略层仲裁（DynamoDB 路线）
└── 3. 结构上无冲突：CRDT / OT ——数学保证合并结果确定（协同编辑、Cassandra 路线）
```

**一句话记住**：Raft 是"排队"（不让冲突发生），CRDT 是"随便写，反正最后一定收敛到同一个结果"。

### 二、为什么 AP 系统会产生冲突？—— 向量时钟（Vector Clock）

CAP 告诉我们在分区时只能选 CP 或 AP。选 AP 意味着两个副本都能接受写：

```
        写 x=1 (t=1)                    写 x=2 (t=2)
副本 A ───────────→        网络分区         ←─────────── 副本 B
                      ↓ 合并时 ↓
                 x 到底是 1 还是 2？—— 冲突！
```

**检测冲突靠向量时钟**：每个节点维护一个 `[A:1, B:1, C:0]` 形式的版本计数器：

- **偏序关系**：`V1 < V2` 当且仅当每个分量都 `<=` 且至少一个 `<` → V1 是 V2 的祖先（无冲突，V2 赢）。
- **并发关系**：两个向量互不大于（`[A:1,B:0]` vs `[A:0,B:1]`）→ **并发写，冲突了，需要仲裁**。

> 💬 **面试官**：Lamport 时钟行不行？
> 🎯 **你**：不行。Lamport 时钟是全序的，但它只能告诉你谁先谁后（或无法比较），**无法表达并发关系**——`L(a) < L(b)` 不代表 a happens-before b。向量时钟才能精确刻画因果。

### 三、冲突解决策略一：LWW（Last-Write-Wins，最后写入获胜）

最简单的仲裁：**给每次写打上 (timestamp, nodeId)，取最大的那个，平局用 nodeId 比**——全序化，永远有确定结果。

```
x 的两个冲突版本: (t=1, A: x=1) vs (t=2, B: x=2)
→ LWW 选 t=2 → x = 2，冲突"消失"了
```

**代价（必说）**：**可能丢数据**——t=1 的写入被静默丢弃，如果时钟漂移导致老数据时间戳更大，还会丢新数据。

- ✅ 优点：无冲突结构、实现极简、读取只需取最大版本（就是今天的算法题！）。
- ❌ 缺点：丢失更新、依赖时钟（NTP 漂移是隐形炸弹）。
- 📍 应用：**DynamoDB（默认）、Cassandra（可调）、Redis 主从异步复制**。

### 四、冲突解决策略二：CRDT（Conflict-free Replicated Data Type）

核心思想：**数据类型本身设计成"数学上必然收敛"**——任意顺序应用任意次更新，所有副本最终状态一致，且无需中心协调。

#### 两类 CRDT

| 类型 | 别名 | 原理 | 示例 |
|---|---|---|---|
| **State-based** | CvRDT | 节点间**全量合并状态**，合并函数满足交换律+结合律+幂等 | G-Counter、G-Set、OR-Set |
| **Op-based** | CmRDT | 只广播**操作**，要求底层消息层保证可靠 + 因果顺序投递 | 正数计数器加操作 |

#### 四个必背 CRDT（面试手写级）

**1. G-Counter（Grow-only Counter，只增计数器）**
```
每个节点维护数组 counts[N]：
- 本地 increment: counts[自己] += 1
- 查询: sum(counts)
- 合并: 逐分量取 max —— 交换/结合/幂等 ✅
分布式点赞数、PV 统计就用它。
```

**2. PN-Counter（支持递减）**
```
= 一个 G-Counter 记 P（增加）+ 一个 G-Counter 记 N（减少）
- increment → P.increment()，decrement → N.increment()
- value = P.sum() - N.sum()
- 合并：两个分量分别取 max 合并
```

**3. G-Set / 2P-Set（集合）**
```
G-Set: 只增不删，合并取并集 ✅
2P-Set: 加一个 tombstone（墓碑）集合记录删除，
        查询 = 主集 - 墓碑集 —— 但删除后不能再加回来
```

**4. OR-Set（Observed-Remove Set， observed-remove 集合）⭐ 最实用**
```
关键创新：每个元素带唯一 tag，add 生成新 tag，remove 删除"当前可见的所有 tag"
- 并发 add/remove：remove 只删它"看到"的 tag，新 add 的 tag 存活
- 效果：并发下"加"总能赢过"没看到的删"，符合用户直觉（购物车场景）
```

#### CRDT 什么时候不收敛？（反向考点）

- Op-based CRDT 底层消息层**乱序/丢消息** → 需要向量时钟 + 重传保证因果序。
- **register（寄存器）类**：纯 LWW-Register 并发写丢数据；Multi-Value Register（MV-Register）则并发写全保留，让客户端合并。
- 设计新 CRDT 必须验证**交换律、结合律、幂等性**三定律，缺一不收敛。

> 📍 应用：**Redis Enterprise CRDB（跨机房 Active-Active）、Akka Distributed Data、Riak、Azure Cosmos DB（部分类型）、Apple Notes / Figma 的多人协同底层思想**。

### 五、冲突解决策略三：OT（Operational Transformation，操作变换）——协同编辑的灵魂

CRDT 是"换数据结构"，OT 是"**变换操作本身**"：当别人的操作插入进来时，把你的操作"改写成等效形式"再应用。

#### 经典例子：两人同时编辑 "abc"

```
初始文档: "abc"
用户 A 在位置 1 插入 "X"：op_A = insert(1, "X")   → "aXbc"
用户 B 在位置 3 删除 1 个：op_B = delete(3, 1)      → "ab"

并发到达服务器，如果都直接按原坐标应用 → 结果不一致！
```

**OT 的做法**：A 的操作先应用，B 的操作针对变换后的坐标重新计算：

```
op_A 应用后文档 "aXbc"，B 原本想删原位置 3 的 'c'
→ 变换 op_B' = transform(op_B, op_A) = delete(4, 1)（因为前面多了 1 字符）
→ A 的最终文档 "aXb"，B 也是 "aXb" ✅ 收敛
```

#### OT 的核心公式

```
convergence = transformation functions T 满足:
  T(op_i, op_j) 把 op_i 变换成"在 op_j 已经生效的世界里"的等效操作

一致性模型 CCI：
- Convergence（收敛）
- Causality preservation（因果保持）
- Intention preservation（意图保持）—— 最难的
```

#### OT vs CRDT 终极对比（必考）

| 维度 | OT | CRDT |
|---|---|---|
| 核心思想 | 变换操作坐标，保持意图 | 数学结构天然收敛 |
| 收敛保证 | 需要中央/集中式转换点（Google Docs 模型）或复杂的去中心化算法 | 去中心化，任意拓扑 |
| 实现难度 | 😱 高（边界 case 爆炸：插入遇到删除、两个插入同一位置……） | 🙂 中（数据结构学现成） |
| 性能 | 操作转换 O(n·m)，长文档差 | 元数据可能膨胀（需要压缩/garbage collection） |
| 代表应用 | **Google Docs、微软 Office Online、Etherpad** | **Figma、Apple Notes、Roam Research、Redis CRDB** |
| 语义保留 | ✅ 强（意图保持） | ⚠️ 弱（只保证收敛，不保证意图） |

> 💬 **面试官**：Google Docs 为什么用 OT 不用 CRDT？
> 🎯 **你**：① OT 早于 CRDT 成熟，Google 2000s 就投产了；② OT 的**意图保持**对富文本（排版、格式属性）更强；③ Google Docs 本来就是中心服务器模型，OT 的"需要转换点"不是负担。但新系统很多转向 CRDT——去中心化、实现简单是趋势。

### 六、工业界全景速查

| 系统 | 一致性选择 | 冲突解决 |
|---|---|---|
| **DynamoDB / Cassandra** | AP + 可调 | 向量时钟检测 + LWW 或客户端合并（read-repair） |
| **Redis 主从 / Cluster** | AP（异步复制） | 没有解决！主从切换丢数据（psync 部分同步兜底），**所以 Redis 集群不适合强一致场景** |
| **Redis Enterprise CRDB** | AP（Active-Active） | **CRDT**（String→LWW-Register，Counter→PN-Counter，Set→OR-Set） |
| **Cosmos DB** | 五级可调 | 多模：LWW / CRDT（部分）/ 自定义 |
| **Figma / Notion / Apple Notes** | AP（本地优先） | CRDT |
| **Google Docs** | 中心式 | OT |
| **CouchDB / PouchDB** | AP（离线优先） | 多版本 + 应用层解决（TreeQL/自定义合并） |

### 七、高频连环问速答模板

**Q1：CRDT 三定律？**
> 交换律（a∨b = b∨a）+ 结合律（(a∨b)∨c = a∨(b∨c)）+ 幂等性（a∨a = a）。满足这三条，任何拓扑任何顺序合并都收敛。

**Q2：LWW 的坑？**
> ① 丢更新（并发写只留一个）；② 依赖物理时钟，NTP 跳变导致"未来数据被过去数据覆盖"；③ 无法表达"两个都想要"的业务语义。改进：逻辑时钟 / HLC（混合逻辑时钟）替代物理时钟。

**Q3：向量时钟的空间问题？**
> O(N)（N = 节点数）。节点多了用**带外压缩**（只保留活跃节点分量）、或换 **dotted version vector**（Riak 方案）、或上 **HLC** 折中。

**Q4：CRDT 元数据膨胀怎么办？**
> ① garbage collection（因果过去稳定后清理墓碑/tag）；② 定期压实（compaction）；③ 分层存储热数据全量 + 冷数据快照。

**Q5：领导Multi-Region 数据同步，你选什么？**
> 决策树：
> - 需要强一致、能容忍写延迟 → Raft/Paxos 跨区（CP，Day 101-103）
> - 写少读多、冲突罕见 → LWW + 向量时钟（Dynamo 模式）
> - 本地优先 / 离线可用 / 多写 → CRDT（Redis CRDB 模式）
> - 协同编辑保意图 → OT（中心服务器可接受时）
> 答完补一句：**"所有方案的本质都是在 CAP 的 A 和 C 之间选位置，冲突解决是给 A 打的补丁。"** —— 这就是 Week 16 五天内容的串线。

---

## ✅ 今日自检清单

- [ ] 能手写向量时钟，说出偏序和并发两种关系怎么判断
- [ ] 能写 G-Counter 和 PN-Counter，说出为什么逐分量取 max 满足三定律
- [ ] 能解释 OR-Set 为什么"加赢过没看到的删"
- [ ] 能写出 OT 的 transform 例子（插入 vs 删除的坐标变换）
- [ ] 能背 OT vs CRDT 对比表，说出 Google Docs 为什么选 OT
- [ ] 能说出 LWW 三个坑，并提出至少一个改进方案
- [ ] 能用决策树回答"Multi-Region 同步怎么选"

---

## 📚 延伸阅读

- Dynamo: Amazon's Highly Available Key-value Store（2007）— LWW + 向量时钟的祖师爷
- Shapiro et al., *Conflict-free Replicated Data Types*（2011）— CRDT 奠基论文
- Kleppmann et al., *Local-First Software*（2019）— 本地优先软件宣言
- Ellis & Gibbs, *Concurrency Control in Groupware Systems*（1989）— OT 开山之作
