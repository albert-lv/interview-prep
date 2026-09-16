# Day 104 — 01 矩阵 + Gossip 协议与成员发现

> 📅 2026-09-16 | Week 16 Day 4 | 主题：分布式系统核心协议 🔗

---

## 今日算法题

### 01 矩阵（01 Matrix）

**题目来源**：[LeetCode 542](https://leetcode.cn/problems/01-matrix/)

**描述**：给定一个由 `0` 和 `1` 组成的二维矩阵 `mat`，返回一个同尺寸的距离矩阵：每个位置 `dist[i][j]` 表示原位置 `(i, j)` 到**最近的 0** 的曼哈顿距离（只能上下左右移动）。

**示例**：
```
输入: mat = [[0,0,0],
             [0,1,0],
             [0,0,0]]
输出: [[0,0,0],
       [0,1,0],
       [0,0,0]]

输入: mat = [[0,0,0],
             [0,1,0],
             [1,1,1]]
输出: [[0,0,0],
       [0,1,0],
       [1,2,1]]
```

**约束**：`m × n ≤ 10⁴`，`mat[i][j]` 只可能是 0 或 1，矩阵中至少有一个 0。

---

### 解题思路：多源 BFS

#### 先想朴素解：从每个 1 出发找最近的 0

对每个 1 做一次 BFS → O((mn)²)，10⁴ 个格子直接爆炸。

反过来想：与其让 1 去找 0，不如让 **0 主动去找 1**。

#### 核心洞察：把所有 0 同时入队

BFS 的性质是**按层扩展，距离递增**。"腐烂的橘子"里所有烂橘子同时开始腐烂——今天的 01 矩阵是同款：**所有 0 同时入队作为源点，逐层向外感染**。

- 每个 1 第一次被访问到时，**感染它的那个 0 就是离它最近的 0**（BFS 保证先到者最近）
- 等价于建一个虚拟"超级源点"连向所有 0，跑单源 BFS

**这正好就是 Gossip 协议的传播模型**：多个节点同时开始散发消息，每个节点被"离它最近的消息源"感染，整体 O(log 层数) 收敛。

#### 算法步骤

```
1. 初始化 dist 矩阵：
   - mat[i][j] == 0 → dist[i][j] = 0，入队
   - mat[i][j] == 1 → dist[i][j] = -1（未访问标记）

2. BFS 逐层扩散：
   while 队列非空:
     (r, c) = 出队
     for (r, c) 的四个邻居 (nr, nc):
       if dist[nr][nc] == -1:        // 还没被感染
         dist[nr][nc] = dist[r][c] + 1
         (nr, nc) 入队

3. 返回 dist
```

> 💡 **关键细节**：用 `-1` 做未访问标记，省掉额外的 `visited` 数组；距离在入队时就写入，不是出队时——保证每个节点只入队一次，O(1) 均摊。

---

### 代码实现

```go
package main

import "fmt"

func updateMatrix(mat [][]int) [][]int {
    m, n := len(mat), len(mat[0])
    dist := make([][]int, m)
    for i := range dist {
        dist[i] = make([]int, n)
        for j := range dist[i] {
            dist[i][j] = -1 // -1 = 未访问
        }
    }
    var q [][2]int
    // 所有 0 同时入队 —— 多源 BFS 的起点
    for i := 0; i < m; i++ {
        for j := 0; j < n; j++ {
            if mat[i][j] == 0 {
                dist[i][j] = 0
                q = append(q, [2]int{i, j})
            }
        }
    }
    dirs := [4][2]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}
    for len(q) > 0 {
        cur := q[0]
        q = q[1:]
        r, c := cur[0], cur[1]
        for _, d := range dirs {
            nr, nc := r+d[0], c+d[1]
            if nr >= 0 && nr < m && nc >= 0 && nc < n && dist[nr][nc] == -1 {
                dist[nr][nc] = dist[r][c] + 1 // 入队时写入距离
                q = append(q, [2]int{nr, nc})
            }
        }
    }
    return dist
}

func main() {
    fmt.Println(updateMatrix([][]int{{0, 0, 0}, {0, 1, 0}, {0, 0, 0}})) // [[0 0 0] [0 1 0] [0 0 0]]
    fmt.Println(updateMatrix([][]int{{0, 0, 0}, {0, 1, 0}, {1, 1, 1}})) // [[0 0 0] [0 1 0] [1 2 1]]
    fmt.Println(updateMatrix([][]int{{0}, {1}}))                        // [[0] [1]]
}
```

```python
from collections import deque
from typing import List

def updateMatrix(mat: List[List[int]]) -> List[List[int]]:
    m, n = len(mat), len(mat[0])
    dist = [[-1] * n for _ in range(m)]
    q = deque()
    for i in range(m):
        for j in range(n):
            if mat[i][j] == 0:
                dist[i][j] = 0
                q.append((i, j))          # 所有 0 同时入队：多源 BFS
    while q:
        r, c = q.popleft()
        for dr, dc in ((-1, 0), (1, 0), (0, -1), (0, 1)):
            nr, nc = r + dr, c + dc
            if 0 <= nr < m and 0 <= nc < n and dist[nr][nc] == -1:
                dist[nr][nc] = dist[r][c] + 1
                q.append((nr, nc))
    return dist
```

---

### 复杂度分析

| 维度 | 结果 | 说明 |
|---|---|---|
| 时间复杂度 | **O(m × n)** | 每个格子最多入队一次、出队一次 |
| 空间复杂度 | **O(m × n)** | dist 矩阵 + 队列最坏装一层格子 |

> 💡 对比 DP 解法（左上→右下、右下→左上两遍扫描取 min）也是 O(mn)，但**多源 BFS 是图上的通用解法**——换成"每个点找最近的加油站/医院"这类带权变体，BFS 思路直接升级到 Dijkstra，DP 就不好用了。面试时优先讲 BFS。

---

### 面试官会怎么追问

> 💬 **面试官**：如果网格有 10⁹ 个格子（内存装不下）怎么办？

🎯 **你**：三个方向：
> 1. **外存 BFS / 分块计算**：把网格切成块，块间用磁盘 spill，类似外存图算法
> 2. **稀疏表示**：实际数据中 0 往往很稀疏，只存 0 的坐标，用 KD-Tree / Ball-Tree 做最近邻查询，每个 1 查最近 0
> 3. **近似算法**：采样子网格精确算，其余插值
> 工程上选哪个看精度要求和 0 的密度。

> 💬 **面试官**：路径权重不同（走一格代价不同，还有障碍物）怎么改？

🎯 **你**：权重非负 → **Dijkstra**（多源 Dijkstra，把所有 0 压入优先队列）；带负权？网格场景基本不会，真遇到了换 Bellman-Ford / SPFA。有障碍物就是普通的 0-1 BFS（边权 0/1 时用双端队列优化）或 A*。

> 💬 **面试官**：怎么找出每个 1 具体被哪个 0 感染（不只距离，还要源点编号）？

🎯 **你**：入队时带上"源点坐标"（`node = (r, c, src_r, src_c)`），BFS 第一次访问即最近源。或者并查集做法：多源 BFS 过程中做 union，最后同源的连通块就是感染域——两个说法都对，前者是面试最优解。

> 💬 **面试官**：多源 BFS 和 Dijkstra 什么关系？

🎯 **你**：**多源 BFS 是边权全为 1 的多源 Dijkstra**。Dijkstra 用优先队列按距离排序，BFS 用普通队列——因为边权相同时队列天然有序。边权非负不等 → 必须 Dijkstra。这个层级关系讲清楚很加分。

> 💬 **面试官**：如果矩阵代表疫情传播，每个 0 是初始感染者，问所有人被感染的时间线？（把题变体成建模题）

🎯 **你**：就是本题的输出——`dist[i][j]` 就是感染时刻。追问"第 t 天有多少感染者"→ 统计 dist 中等于 t 的个数；追问"最晚多久全覆盖"→ max(dist)。建模面试里 BFS 层数就是时间轴，这个映射要能脱口而出。

---

## 面试技巧

### Gossip 协议与成员发现

> 前两天学完 Raft 和 Paxos——**强一致、有 Leader、多数派投票**。但有一类系统不想付这个代价：几千上万节点、跨机房、允许最终一致。这时就该 Gossip 登场了。

---

### 1. Gossip 是什么？一句话说清

**Gossip = 流行病协议（Epidemic Protocol）：节点像传八卦一样，每轮随机挑几个邻居同步状态。**

没有中心节点，没有全局视图，每个节点只和少数几个同伴交换信息——但数学保证：**经过 O(log n) 轮，消息传遍全网**，且任何节点宕机都不影响整体存活。

直觉：一个人告诉 2 个人，这 2 个人再各告诉 2 个人……指数扩散，`log₂(10000) ≈ 14` 轮就全覆盖。

---

### 2. 两种传播模式

| 模式 | 别名 | 工作方式 | 适用场景 |
|---|---|---|---|
| **反熵（Anti-Entropy）** | 蠕虫模式 | 节点定期随机选一个邻居，**全量比对/摘要比对**（Merkle Tree）修复不一致 | 长期一致性修复，Cassandra 节点修复就靠它 |
| **谣传传播（Rumor Mongering）** | 流行病模式 | 节点有**新消息**时主动传播；收到反馈"大家都知道"后转为免疫，停止传播 | 新数据/新事件快速扩散 |

> 💡 一个管**存量对齐**（反熵慢慢对齐），一个管**增量广播**（谣传快速扩散）。两者配合：新消息用谣传快速传遍，遗留不一致用反熵兜底修复。

---

### 3. 流行病模型 SIR：Gossip 的数学根基

Gossip 的收敛性直接来自流行病学的 SIR 模型：

- **S（Susceptible，易感）**：还没收到消息的节点
- **I（Infected，感染）**：持有消息正在传播的节点
- **R（Removed，移除/免疫）**：已收到并停止传播的节点

**关键参数**：每个感染节点每轮接触 `k` 个邻居。当 `k·p > 1`（p 是传播成功率），消息指数扩散，约 **O(log n) 轮**传遍全网后自然消亡。

> 🔥 **面试一句话**："Gossip 的传播动力学就是 SIR 模型——临界条件 `k·p > 1` 时指数扩散，`log n` 轮收敛；消息传遍全网后所有节点进入免疫态，协议自然终止，不需要任何停止信号。"

---

### 4. 为什么需要 Gossip？中心化注册中心的痛

| 维度 | 中心化（ZK / Eureka / Nacos） | Gossip 去中心化 |
|---|---|---|
| 架构 | 中心集群 + 客户端注册 | 无中心，人人平等 |
| 单点/集群故障 | 中心挂了全网受影响 | 任意节点宕机无感 |
| 扩展性 | 中心集群吞吐是瓶颈 | 每节点只跟少数人说话，水平扩展 |
| 一致性 | 强一致（ZK）/ AP（Eureka） | **最终一致** |
| 跨机房 | 中心写跨机房延迟高 | 局域网内传播，天然分区友好 |
| 典型系统 | 服务注册发现、分布式锁 | Cassandra、Consul、Redis Cluster |

> 💡 **选型决策树**：节点数 < 百级、要强一致（选主、锁）→ 中心化；节点数百到万级、要最终一致、跨机房 → Gossip。

---

### 5. 成员发现与故障检测：SWIM 协议

Gossip 解决"消息怎么传"，**SWIM** 解决"集群里都有谁、谁还活着"。

**SWIM = Scalable Weakly-consistent Infection-style Process Group Membership Protocol**（2002 年发表，名字即设计哲学）。

#### 三大组件

**① 故障检测（Failure Detection）—— 心跳太粗暴，SWIM 用探测**

```
每轮，节点 A 随机选一个节点 B 发 ping：
- B 正常回复 ack → B 存活，本轮结束
- 超时未回复 → A 随机选 k 个节点，让它们间接探测 B
  （A 说："你们帮我 ping B，告诉我结果"）
- 间接探测也全部超时 → 判定 B 故障，广播"怀疑 B 挂了"
```

**为什么用间接探测？** A 到 B 网络抖动 ≠ B 宕机。让 k 个中继节点分别探 B，多数超时才是真挂了——**排除单条链路抖动造成的误判**。

**② 成员变更广播（Membership Dissemination）—— 故障/加入事件用 Gossip 扩散**

判定 B 故障后，A 把这个事件像八卦一样随机传播。整个集群最终一致地知道 B 离开了。新节点加入同理广播。

**③ 怀疑机制（Suspicion）—— 给"可能没死"的节点留机会**

判定流程：`Suspect（怀疑）` → 等待 → 要么收到 B 的辟谣（"我还活着"）→ 恢复；要么超时 → `Confirm（确认故障）` 移除。

> 💡 这就是 SWIM 的优雅之处：**不直接宣布死亡，先怀疑、再确认**。网络分区恢复后，"假死"节点还能归队。

**④ Incarnation Number（化身号）—— 防止假死复活污染**

每个节点维护一个逻辑时钟（incarnation number）：
- 节点每次"重生"（重启/辟谣）时 +1
- 收到关于自己的怀疑 → 立即广播带新版本号的心跳辟谣
- **版本号大的声明覆盖小的** → 旧网络的过期信息不会把活节点误判成死

> 🔥 **面试高频**：incarnation number 是 SWIM 的灵魂，答出来秒杀 90% 的候选人。

---

### 6. Gossip / SWIM 全家桶应用

| 系统 | 协议 | 用在哪 |
|---|---|---|
| **Cassandra** | Gossip（反熵） | 节点间交换集群状态、token 分布 |
| **Redis Cluster** | Gossip | 节点间传播槽位分布、节点状态 |
| **Consul（Serf）** | SWIM + Gossip | 成员发现 + 故障检测 + 事件广播 |
| **DynamoDB** | Gossip | 成员关系与故障检测 |
| **CockroachDB** | Gossip | 集群配置传播 |
| **比特币 / P2P 网络** | Gossip 变种 | 交易和区块广播 |

> 💡 **Serf 是 SWIM 的生产级实现**（HashiCorp 出品），Consul 底层就是它。面试提一句"Consul 用的是 SWIM 而非 Paxos/Raft 做成员发现"是加分细节。

---

### 7. Gossip vs Raft/Paxos：别搞混

| 维度 | Gossip | Raft / Paxos |
|---|---|---|
| **解决什么** | 消息扩散 + 成员发现（who is alive） | 共识（对**一个值**达成一致） |
| **一致性** | 最终一致 | 强一致 |
| **有 Leader 吗** | 没有 | 有（Raft）/ 可竞争（Paxos） |
| **每轮通信** | O(k) 条消息（k=常数） | 至少 2 次 RPC 多数派 |
| **收敛时间** | O(log n) 轮 | 1 轮（但只能管一个值） |
| **典型用途** | 集群拓扑、配置下发、故障检测 | 选主、元数据、日志复制 |

> 🔥 **一句话速答**："Gossip 管**成员**（谁活着、新配置），Raft 管**数据**（日志复制、元数据）。大系统两者都用：Cassandra 用 Gossip 管集群拓扑，但写入一致性靠 Quorum/读修复。"

---

### 8. 高频面试连环问

> 💬 **面试官**：Gossip 会不会消息风暴？怎么收敛？

🎯 **你**：谣传模式下，收到消息的节点变"感染"，继续传播；但收到足够多的"已免疫"反馈后，自己转为免疫（Removed）停止传播。**没有新消息注入时，全网免疫，协议自然终止**——不需要停止信号，这是 SIR 模型的数学性质，不是工程 hack。

> 💬 **面试官**：Gossip 有缺点吗？

🎯 **你**：三个：
> 1. **收敛延迟**：最终一致意味着中间态不一致，CAP 里的 A 换来的就是 C 的延迟
> 2. **消息冗余**：同一条消息可能被重复传播，带宽浪费（缓解：摘要/哈希去重）
> 3. **不保证顺序**：后发的消息可能先到，需要版本号/时间戳兜底

> 💬 **面试官**：节点数很大（万级）时 Gossip 还靠谱吗？

🎯 **你**：靠谱，这正是 Gossip 的主场。O(log n) 轮收敛意味着万级节点也就 14 轮左右。但工程上要做**分层 Gossip / 分层 SWIM**（数据中心内部一层、跨 DC 一层），避免跨机房带宽被打爆。Cassandra 的多 DC 部署就是这么干的。

> 💬 **面试官**：SWIM 的间接探测为什么是 k 个节点？k 怎么选？

🎯 **你**：k 是**误判率与开销的权衡**：k 越大误判率指数下降（k 个独立链路同时抖动的概率 ≈ 单链路抖动的 k 次方），但探测延迟也增加。论文默认 k=3，工业界一般 2-5。配合超时可调，形成"快速检测 + 低误判"的折中。

> 💬 **面试官**：脑裂（网络分区）时 Gossip 集群会怎样？

🎯 **你**：分区两边各自继续 Gossip，**两边都以为对方死了**（各自的怀疑机制分别确认）。恢复通信后：
> - 两边的 incarnation number 大的胜出
> - 成员列表做 merge，分区期间离开/加入的节点重新对齐
> - SWIM 的"先怀疑后确认"机制让短暂分区不至于永久移除节点
> 但**应用层数据的一致性 Gossip 不管**——那得靠版本向量、CRDT 或应用侧冲突解决（Day 106 会展开）。

> 💬 **面试官**：设计一个服务注册中心，选 Gossip 还是选 Raft？

🎯 **你**：**看规模和一致性要求**：
> - 注册中心节点几十上百、要求强一致（不能注册两个主）→ Raft（etcd/Consul 服务端模式）
> - 节点上千、跨机房、最终一致可接受（AP）→ Gossip/SWIM（Consul 客户端模式、Eureka peer 复制）
> - 混合架构也常见：控制面 Raft 存元数据，数据面 Gossip 做成员健康检查——Consul 就是这个架构。

---

### 9. 自检验查清单一分钟版

面试前快速过一遍，能答上 6/8 就稳了：

- [ ] Gossip 两种传播模式？（反熵对齐存量 / 谣传广播增量）
- [ ] SIR 三态？（Susceptible / Infected / Removed）
- [ ] 传遍全网要几轮？（O(log n)，万级节点约 14 轮）
- [ ] SWIM 故障检测流程？（ping → 超时 → k 个间接探测 → 怀疑 → 确认）
- [ ] incarnation number 干嘛的？（防假死污染，版本大者胜）
- [ ] Gossip vs Raft 分工？（Gossip 管成员，Raft 管数据）
- [ ] Redis Cluster / Cassandra / Consul 分别怎么用 Gossip？
- [ ] Gossip 三大缺点？（延迟 / 冗余 / 无序）

---

## 参考资源

- [SWIM: Scalable Weakly-consistent Infection-style Process Group Membership Protocol (2002)](https://www.cs.cornell.edu/projects/Quicksilver/public_pdfs/SWIM.pdf) — 成员发现协议经典论文
- [Cassandra Gossip 官方文档](https://cassandra.apache.org/doc/latest/cassandra/architecture/gossip.html)
- [Serf（SWIM 生产实现）](https://www.serf.io/) — HashiCorp 出品，Consul 底层
- [Redis Cluster 官方文档 — Gossip 部分](https://redis.io/docs/management/scaling/)
- [Epidemic Algorithms for Replicated Database Maintenance (1988)](https://dl.acm.org/doi/10.1145/42282.42284) — Gossip 开山论文
- 昨日内容：[Day 103 — 只出现一次的数字 II + Paxos 算法与 Multi-Paxos](./2026-09-15-single-number-ii-paxos.md)
- 明日预告：**分布式锁设计与实现** 🔜

---

> 🎯 **今日金句**：*"强一致协议像开会：人人表态，慢但确定；Gossip 像传八卦：随口一说，快但可能添油加醋——而传到最后，大家知道的都一样。"*
