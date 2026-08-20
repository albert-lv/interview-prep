# 🎯 Interview Prep — 每天一道题，六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2012%20Day%2077-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%2077%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰，是真的每天都在更新。**
>
> 每天 20:42 自动推送：一道算法题 + 一页面试速查。跟着走，6 周后你会感谢自己。

---

## 📌 这是什么？

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 76 天** |
| 📅 **6 周系统计划** | 不是零散刷题，按主题递进（DP → 数据结构 → 网络 → 系统设计） |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码，是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 77 · 2026-08-20）
- 🔥 [前 K 个高频元素 + MapReduce 原理与分布式计算](week12/2026-08-20-frequent-elements-mapreduce.md) — 哈希表+最小堆 O(n log k) / 桶排序 O(n) 双解，MapReduce 编程模型（Map→Shuffle→Reduce），Combiner/Partitioner 优化，Hadoop/Spark/Flink 对比，数据倾斜解决方案
- 🎤 面试技巧：WordCount 完整执行流程、分布式计算框架选型、数据倾斜加盐策略、100TB 数据求 Top 100 工程方案

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
- 🔥 [Day 77 — 前 K 个高频元素 + MapReduce 原理与分布式计算](week12/2026-08-20-frequent-elements-mapreduce.md)（哈希表+最小堆 / 桶排序 O(n) / MapReduce 模型 / Combiner / 数据倾斜 / Hadoop vs Spark vs Flink）
- [Day 76 — 数组中的第K个最大元素 + 搜索引擎设计基础](week12/2026-08-19-topk-search-engine.md)（快速选择 O(n) / 最小堆 / 倒排索引 / 分布式搜索 / BM25 / ES vs MySQL）
- [Day 75 — 常数时间插入删除获取随机元素 + 存储引擎与缓存一致性](week11/2026-08-18-storage-engine-cache.md)（HashMap+ArrayList O(1) / B+Tree vs LSM-Tree / 缓存一致性四种策略 / 面试连环追问）
- [Day 74 — 设计点击量统计器 + 数据库分库分表策略](week11/2026-08-17-hit-counter.md)（滑动窗口循环数组 / 垂直水平拆分 / 分片键选择 / 跨片查询 / 扩容迁移）
- [Day 73 — LFU 缓存设计 + 数据库连接池与 SQL 优化](week11/2026-08-16-lfu-cache.md)（HashMap+频率链表+双向链表 / 连接池调优 / SQL 优化 / 慢查询排查）

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

当前进度：**Week 12 / Day 77**（Week 12 主题：大数据处理与搜索引擎 🔍）

**更新记录**：已连续更新 **77** 天，每日 20:42 自动推送。

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
