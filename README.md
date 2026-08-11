# 🎯 Interview Prep — 6 周拿下大厂面试

[![Progress](https://img.shields.io/badge/进度-Week%2010%20Day%2068-blue)](./memory/interview-prep.md)
[![Auto Push](https://img.shields.io/badge/每日更新-08:42%20%7C%2020:42-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是题海战术，是「高频考点 + 面试话术」双管齐下。**
> 
> 每天晚上 20:42 自动更新：一道算法题 + 一页面试速查。跟着走，6 周后你会感谢自己。

---

## 📌 这是什么？

一个 **结构化、自动化、可跟随** 的面试准备仓库。

- ✅ **6 周系统计划** — 不是零散刷题，是按主题递进（DP → 数据结构 → 网络 → 系统设计）
- ✅ **每日双更** — 早上 Agent 工具技巧，晚上算法 + 面试考点
- ✅ **面试导向** — 每道题都带「面试官会怎么问」和「一句话速答」
- ✅ **完整可运行代码** — 不是伪代码，是能直接 `gcc` 或 `go run` 的

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
| **Week 7+** | 操作系统 | 网络协议 | 网络安全 | ...持续更新 |

**节奏设计**：周一/周五主菜（重难点），周三换口味（数据结构/算法），周六复盘，周日彻底休息。

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

**最新内容**：
- 🔥 [Day 67 — HTTP/HTTPS 核心原理](week10/2026-08-10-http-https.md)（TLS 1.3、缓存机制、Cookie 安全）
- 🔥 [Day 66 — TCP/UDP 深度解析](week10/2026-08-11-tcp-udp.md)（滑动窗口、拥塞控制、队头阻塞）
- 🔥 [Day 65 — IO 模型与 epoll](week10/2026-08-10-io-models.md)（Reactor 模式、ET vs LT、Redis 单线程）

---

## 🚀 怎么使用？

### 方式一：跟着走（推荐）

1. 点右上角 ⭐ Star 本仓库（给自己一点仪式感）
2. 每天 20:42 来看当日更新，或 Watch 仓库接收通知
3. 按周推进，周六复盘这周的内容
4. 面试前一周，快速过一遍 `memory/interview-prep.md` 的进度索引

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

当前进度：**Week 10 / Day 68**（操作系统 + 网络协议已进入深水区 🏊）

详细进度见 [`memory/interview-prep.md`](memory/interview-prep.md)。

---

## 🤝 欢迎加入

这个仓库最初是个人学习笔记，但好东西值得分享。

- 发现错误？提 Issue 或 PR
- 有面试真题想补充？欢迎贡献
- 单纯想一起打卡？点 Star 就是组队信号 ⭐

> 由 [向上小郎君](https://github.com/albert-lv) 维护，每日自动推送 + 不定期手动加餐。

---

## 📜 License

MIT — 内容可自由使用，转载请注明出处。
