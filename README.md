# 🎯 Interview Prep — 每天一道题，六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2016%20Day%20106%20🔥-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20106%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰，是真的每天都在更新。**
>
> 每天 20:42 自动推送：一道算法题 + 一页面试速查。跟着走，6 周后你会感谢自己。
>
> 🎉 **Day 100 达成！连续更新 106 天！**

---

## 📌 这是什么？

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 106 天** |
| 📅 **6 周系统计划** | 不是零散刷题，按主题递进（DP → 数据结构 → 网络 → 系统设计） |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码，是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 106 · 2026-09-18 · Week 16 进行中 🔗）
- 🔥 [基于时间的键值存储 + 副本一致性与冲突解决（CRDT / OT）](week16/2026-09-18-timebased-kv-crdt-ot.md) — 多版本存储 + 二分查找 O(log n) 快照读：这就是 MVCC 读取路径和 LWW 冲突解决的骨架；三条冲突解决路线（强一致避免冲突 / 向量时钟检测+LWW 仲裁 / CRDT·OT 结构收敛）；向量时钟偏序与并发判定，Lamport 时钟为什么不行；LWW 三大坑（丢更新·时钟漂移·语义丢失）与 HLC 改进；CRDT 三定律（交换·结合·幂等）与 G-Counter / PN-Counter / G-Set / 2P-Set / OR-Set 手写级实现（OR-Set 为什么"加赢过没看到的删"）；OT 操作变换与 CCI 一致性模型（收敛·因果·意图保持）；OT vs CRDT 终极对比（Google Docs 为什么选 OT）；Redis CRDB / DynamoDB / Figma / Cosmos DB 工业界全景速查；Multi-Region 同步决策树 + 高频连环问速答
- 🎤 面试技巧：冲突解决三路线框架、向量时钟手写、CRDT 三定律默写、OT transform 坐标变换现场演算、LWW 坑与改进、"本质是在 CAP 的 A 和 C 之间选位置"串线收尾话术

**想看今天的内容？直接点上面 👆**

---

## 🗓 周计划（穿插式，不累死）

| 周 | 周一 | 周三 | 周五 | 周末 |
|---|---|---|---|---|
| **Week 1** | DP 入门 | 数组/字符串 | DP 进阶 | 复盘 |
| **Week 2** | 线性 DP | 链表/栈/队列 | 线性 DP 进阶 | 复盘 |
| **Week 3** | 树形 DP | 树/BFS/DFS | 区间 DP | 复盘 |
| **Week 4** | 状态压缩 DP | 图论/二分/滑动窗口 | DP 优化 | 复盘 |
| **Week 5** | DP 高频 | 贪心/回溯 | 模拟实战 | 复盘 |
| **Week 6** | 综合真题 | 系统设计/场景题 | 模拟面试 | 🎉 毕业 |
| **Week 7+** | 操作系统 | 网络协议 | 网络安全 | ...**持续更新中** |

> 💡 **节奏设计**：周一/周五主菜（重难点），周三换口味（数据结构/算法），周六复盘，周日彻底休息。
>
> 🔥 **当前状态**：Week 10 已完结，**每日更新从未中断**。

---

---

## 📂 内容目录

```
week01/   # 动态规划基础
week02/   # 线性 DP
week03/   # 树形 & 区间 DP
week04/   # 状态压缩 & 优化
week05/   # DP 高频 & 模拟
week06/   # 综合真题 & 系统设计
week07/   # 操作系统（进程/线程/内存/IO）
week08/   # 操作系统进阶（锁/调度/文件系统）
week09/   # 网络基础（TCP/IP/HTTP/DNS）
week10/   # 网络进阶（IO 模型/TCP 拥塞/HTTPS/TLS）
agent-tips/   # 每日 Agent 工具技巧（Claude Code / Kimi Code / Windsurf）
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- 🔥 [Day 106 — 基于时间的键值存储 + 副本一致性与冲突解决（CRDT / OT）](week16/2026-09-18-timebased-kv-crdt-ot.md)（多版本存储 + 二分 O(log n) 快照读 = MVCC / LWW 骨架 / 冲突解决三路线：避免·检测仲裁·结构收敛 / 向量时钟偏序与并发判定 / LWW 三坑与 HLC 改进 / CRDT 三定律与 G-Counter·PN-Counter·OR-Set 手写 / OT 操作变换与 CCI 模型 / OT vs CRDT：Google Docs 为什么选 OT / Redis CRDB·DynamoDB·Figma 全景 / Multi-Region 决策树）
- [Day 105 — 交替打印 FooBar + 分布式锁设计与实现](week16/2026-09-17-foobar-distributed-lock.md)（锁+条件变量 / 双信号量 / channel 三解：while 防虚假唤醒 + Broadcast 防丢失唤醒 / 分布式锁打分表：互斥·防死锁·容错·谁加谁删·看门狗·fencing / Redis 锁四代演进：SETNX 死锁 → SET NX EX+Lua 原子删 → 看门狗续约 → Redlock 多数派与争议 / ZK 临时顺序节点 + watch 前驱避羊群 / etcd lease+revision / fencing token 存储端校验 / 可重入与公平锁 / 跨机房锁设计）
- [Day 104 — 01 矩阵 + Gossip 协议与成员发现](week16/2026-09-16-01-matrix-gossip.md)（多源 BFS 求最近源距离 O(mn) / `-1` 未访问标记 / 入队写距离技巧 / 多源 BFS = 边权 1 的多源 Dijkstra / 网格装不下三解法 / Gossip 双模式：反熵+谣传 / SIR 三态与 k·p>1 指数扩散 / O(log n) 轮收敛 / SWIM 四件套：探测·间接探测·怀疑机制·incarnation number / 中心化 vs Gossip 决策树 / Cassandra·Redis Cluster·Consul·DynamoDB 应用 / Gossip 管成员 vs Raft 管数据 / 脑裂恢复）
- [Day 103 — 只出现一次的数字 II + Paxos 算法与 Multi-Paxos](week16/2026-09-15-single-number-ii-paxos.md)（位运算三进制计数器 O(n)/O(1) / ones·twos 状态机与真值表 / 模 k 推广 / Paxos 两阶段：Prepare+Accept / 三角色 / 多数派安全性 / 活锁与退避 / Multi-Paxos 跳过 Phase 1 / Paxos vs Raft 对比 / Chubby / 提案编号设计 / Paxos vs 2PC）

> 📅 **每天 20:42 自动更新**，[查看全部历史 →](https://github.com/albert-lv/interview-prep/commits/main)

---

## 🚀 怎么使用？

### 方式一：跟着走（推荐）

1. 点右上角 ⭐ **Star** 本仓库（给自己一点仪式感）
2. 点 👁️ **Watch** 接收每日更新通知（推荐选 "Releases only" 或 "All Activity"）
3. 每天 20:42 来看当日更新，或等 GitHub 通知推送
4. 按周推进，周六复盘这周的内容
5. 面试前一周，快速过一遍 `memory/interview-prep.md` 的进度索引

> 💬 **真实的每日更新** — 不是一次性写完的题库，是真的每天都在 push 新内容。你可以看 [commit 历史](https://github.com/albert-lv/interview-prep/commits/main) 验证。

### 方式二：按需查阅

| 你想找 | 去哪看 |
|---|---|
| 某道算法题 | `weekXX/YYYY-MM-DD-topic.md` |
| 某个面试考点速查 | 当日的「面试技巧」章节 |
| 系统学习某主题 | 按周顺序阅读，主题集中 |
| 学习进度追踪 | `memory/interview-prep.md` |
| Agent 工具技巧 | `agent-tips/2026/MM/YYYY-MM-DD-agent-tips.md` |

### 方式三：本地跑代码

```bash
git clone https://github.com/albert-lv/interview-prep.git
cd interview-prep/week10
gcc 2026-08-10-io-models.c -o io_demo && ./io_demo
```

---

## 💡 内容特色

### 1. 算法题 = 面试现场复刻

不只是 LeetCode 题解，而是：
- **题目变种** — 面试官常问的 follow-up
- **边界 case** — 那些你面试时会漏掉的 corner case
- **复杂度分析** — 时间 + 空间，还要能说清楚为什么

示例（Day 66 — 滑动窗口最大值）：
> 💬 **面试官**：单调队列还能优化吗？
> 
> 🎯 **你**：可以，如果数据流是无限的，用双端队列维护窗口内递减序列，均摊 O(1)。如果要求第 k 大而不是最大，可以改用两个堆……

### 2. 面试技巧 = 话术模板

不是背书，是可复用的「回答框架」：

- **epoll 为什么比 select 快？** → 三层回答模板（数据结构 + 触发机制 + 无遍历）
- **TCP 为什么三次握手？** → 「信道可靠性 + 防止旧连接初始化」双角度
- **Redis 单线程为什么快？** → 「纯内存 + IO 多路复用 + 避免上下文切换」三件套

### 3. 代码 = 可运行 + 有注释

```c
// 边缘触发(ET)模式下必须循环读到 EAGAIN
while ((n = read(fd, buf, sizeof(buf))) > 0) {
    // 处理数据...
}
if (n == -1 && errno == EAGAIN) {
    // 正常读完，等待下一次 epoll_wait
}
// ❌ 常见坑：ET 模式只读一次，数据会粘在内核缓冲区！
```

---

## 📊 进度追踪

当前进度：**Week 16 / Day 106**（Week 16 主题：分布式系统核心协议 🔗 — **进行中**，Day 107 综合复习即将到来）

**更新记录**：已连续更新 **106** 天，每日 20:42 自动推送。

详细进度见 [`memory/interview-prep.md`](memory/interview-prep.md)。

---

---

## 🤝 欢迎加入 / 一起打卡

这个仓库最初是个人学习笔记，但好东西值得分享。

- ⭐ **Star = 组队信号** — 每天来看新内容的人不止你一个
- 🐛 **发现错误？** 提 Issue 或 PR
- 📝 **有面试真题想补充？** 欢迎贡献
- 👀 **想看看是不是真的每天更新？** [查看 commit 历史](https://github.com/albert-lv/interview-prep/commits/main)

> 由 [albert-lv](https://github.com/albert-lv) 维护，**每日自动推送** + 不定期手动加餐。
>
> **不是一次性项目，是每天都在长的活文档。** 🔥

---

## 📜 License

MIT — 内容可自由使用，转载请注明出处。
