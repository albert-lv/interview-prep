# 🎯 Interview Prep — 每天一道题，六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2014%20Day%2091-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%2091%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
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
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 84 天** |
| 📅 **6 周系统计划** | 不是零散刷题，按主题递进（DP → 数据结构 → 网络 → 系统设计） |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码，是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 91 · 2026-09-03）
- 🔥 [实现 Trie + RAG 与向量检索系统设计](week14/2026-09-03-rag-vector-retrieval.md) — Trie 前缀树实现 O(m) 插入/查找；RAG 完整架构流程（Query理解→向量检索→重排序→Prompt构建→生成）、Embedding 模型选型、ANN 算法对比（HNSW/IVF/PQ/LSH）、向量数据库选型（Milvus/Pinecone/Weaviate/pgvector）、文档切分策略、查询优化（HyDE/多路召回）、Cross-Encoder 重排序、高频面试连环问
- 🎤 面试技巧：RAG vs Fine-tuning 决策树、ANN 为什么比精确搜索快、HNSW 多层导航图原理、Embedding 维度选型、跨语言检索方案、召回率与准确率平衡

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
- 🔥 [Day 91 — 实现 Trie + RAG 与向量检索系统设计](week14/2026-09-03-rag-vector-retrieval.md)（Trie 前缀树 O(m) / RAG 架构完整流程 / Embedding 选型 / ANN 算法 HNSW·IVF·PQ / 向量数据库选型 / 文档切分策略 / HyDE 查询优化 / Cross-Encoder 重排序）
- [Day 90 — 最长递增子序列 + Transformer 与大模型架构](week14/2026-09-02-transformer-architecture.md)（LIS DP O(n²) / 贪心+二分 O(n log n) / Self-Attention QKV / Multi-Head / 位置编码演进 / Encoder-Decoder 架构 / GPT vs BERT vs T5 / KV Cache / FlashAttention / Scaling Laws）
- [Day 89 — 课程表 II + 模型训练与分布式训练](week14/2026-09-01-course-schedule-distributed-training.md)（拓扑排序 O(V+E) / DDP Ring AllReduce / 数据并行 vs 模型并行 vs 流水线并行 / ZeRO-1/2/3 分片策略 / 混合精度训练 FP16/BF16 / 梯度累积 / 3D 并行 / FSDP / DeepSpeed / 大模型 OOM 排查）
- [Day 88 — 三角形最小路径和 + 深度学习与神经网络核心原理](week14/2026-08-31-triangle-dl-basics.md)（自底向上 DP O(n²)/O(n) / 感知机→MLP→DNN 演进 / 激活函数全家桶选型决策树 / 反向传播链式法则 / 损失函数对比 / 优化器进化史 / 正则化四大金刚 / 梯度消失与梯度爆炸 / CNN/RNN/Transformer 速览 / Transformer 核心公式）
- [Day 87 — 字母异位词分组 + 机器学习基础与特征工程](week14/2026-08-30-group-anagrams-ml-basics.md)（哈希表+字符计数 O(N·K) / ML 三大范式 / 特征工程标准化编码 / 模型评估指标 / 过拟合处理 / L1 vs L2 正则化 / 偏差-方差分解）

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

当前进度：**Week 14 / Day 91**（Week 14 主题：人工智能与大模型工程 🧠 — **进行中**）

**更新记录**：已连续更新 **91** 天，每日 20:42 自动推送。

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
