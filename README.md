# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2018%20Day%20119%20🔬-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20119%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 119 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 119 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 119 · 2026-10-01 · Week 18 Day 5 🔬）
- 🔬 [最长重复子数组 + 长上下文与推理系统工程（KV Cache · Prefix Caching · 延迟-质量权衡）](week18/2026-10-01-maximum-length-of-repeated-subarray-inference-systems.md) — 算法题「最长重复子数组」是 LCS 的连续版对偶：`dp[i][j]` 定义为"以 nums1[i-1]、nums2[j-1] 结尾的最长公共**后缀**长度"（不匹配**清零**而非继承历史最优——这是与子序列的唯一差别），滚动数组压缩时 j 必须**倒序**（与 0-1 背包同因），进阶 = 二分长度 + 滚动哈希 O((n+m)·log min(n,m))；核心隐喻：**DP 折叠重叠子结构 ↔ Prefix Caching 折叠共享前缀**——Day 1 的「重叠子结构→折叠计算」与今天的 serving「共享前缀→折叠计算」隔 118 天闭环。面试技巧推理系统工程四大账本：**KV cache 显存公式** `2×层数×KV头数×头维×字节×长度`（Llama-7B fp16 ≈ 512KB/token，4K ≈ 2GB 超权重 15%，GQA 砍头数就是砍这项）；**PagedAttention** = OS 虚拟内存思路（16-token 块 + block table + 按需分配 + Copy-on-Write，碎片 <4%、吞吐 2~4×）；**Prefix caching** = 块级内容哈希（父哈希+token），命中跳过 prefill，GRPO 组共享前缀 prefill O(G·L)→O(L) 省 30~60%（Day 112 隐性红利的原理），多轮对话/系统提示是天然高命中资产；**PD 分离** = prefill 算力型 ∥ decode 带宽型资源画像相反，KV 按层 RDMA 传输，TTFT/TPOT 各有 SLO；长上下文三座大山（prefill O(L²)/显存 O(L)/decode 带宽 O(L)）与登山杖（chunked prefill · KV 量化 · RAG 减输入）；**延迟-质量交换所**：serving 层买延迟（PagedAttention/prefix cache/PD 分离纯赚、speculative decoding 花算力买、KV 量化轻微掉点），test-time 层买质量（长 CoT 核心汇率、自适应预算、budget forcing）
- 🎤 面试技巧：「大模型推理优化」总-分答题模板（模型层→**服务层主战场**→系统层）；TTFT vs TPOT/ITL 指标定义；prefill（并行·compute-bound）vs decode（自回归·memory-bound）两种负载的 batch 策略；KV 显存口算（7B/70B 现场估算）；PagedAttention 三步讲法（问题→机制→收益）；prefix caching 哈希链构造与块大小权衡；GRPO 收益口算题；PD 分离取舍一句话；长上下文优化三件套；与 veRL rollout 的一体两面（Day 113 衔接）

**想看今天的内容?直接点上面 👆**

---

## 🗓 周计划(穿插式,不累死)

| 周 | 周一 | 周三 | 周五 | 周末 |
|---|---|---|---|---|
| **Week 1** | DP 入门 | 数组/字符串 | DP 进阶 | 复盘 |
| **Week 2** | 线性 DP | 链表/栈/队列 | 线性 DP 进阶 | 复盘 |
| **Week 3** | 树形 DP | 树/BFS/DFS | 区间 DP | 复盘 |
| **Week 4** | 状态压缩 DP | 图论/二分/滑动窗口 | DP 优化 | 复盘 |
| **Week 5** | DP 高频 | 贪心/回溯 | 模拟实战 | 复盘 |
| **Week 6** | 综合真题 | 系统设计/场景题 | 模拟面试 | 🎉 毕业 |
| **Week 7+** | 操作系统 | 网络协议 | 网络安全 | ...**持续更新中** |

> 💡 **节奏设计**:周一/周五主菜(重难点),周三换口味(数据结构/算法),周六复盘,周日彻底休息。
>
> 🔥 **当前状态**：Week 18 进行中（推理模型与 Test-Time Compute 🔬，Day 115 解数独 + Test-Time Compute 总览 ✅，Day 116 单词搜索 + PRM ✅，Day 117 N 皇后 + ToT/MCTS/rStar ✅，Day 118 爬楼梯最少成本 + 自我验证与反思 ✅，Day 119 最长重复子数组 + 长上下文与推理系统工程 ✅），Week 17 已完结，**每日更新从未中断**。

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
week07/   # 操作系统(进程/线程/内存/IO)
week08/   # 操作系统进阶(锁/调度/文件系统)
week09/   # 网络基础(TCP/IP/HTTP/DNS)
week10/   # 网络进阶(IO 模型/TCP 拥塞/HTTPS/TLS)
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- 🔬 [Day 119 — 最长重复子数组（Maximum Length of Repeated Subarray）+ 长上下文与推理系统工程（KV Cache · Prefix Caching · 延迟-质量权衡）](week18/2026-10-01-maximum-length-of-repeated-subarray-inference-systems.md)（LCS 连续版对偶：`dp[i][j]` = 以 i-1/j-1 结尾的公共**后缀**长度，不匹配**清零**而非继承 = 与子序列唯一差别 / 滚动数组 j 倒序（与 0-1 背包同因）/ 进阶：二分长度+滚动哈希 O((n+m)log min) / 核心隐喻：DP 折叠重叠子结构 ↔ Prefix Caching 折叠共享前缀·Day 1 闭环 / KV 显存公式 2×层×KV头×头维×字节×长度（7B fp16≈512KB/token·4K≈2GB·GQA 砍头）/ PagedAttention = OS 虚拟内存：16-token 块+block table+按需+COW 碎片<4% / Prefix caching 块级内容哈希命中跳过 prefill·GRPO prefill O(G·L)→O(L) 省 30~60%·多轮对话白嫖 / PD 分离：prefill 算力型 ∥ decode 带宽型·KV 按层 RDMA / 长上下文三座大山 O(L²)·O(L)·O(L) 与登山杖 / 延迟-质量交换所：serving 买延迟（纯赚三件套）↔ test-time 买质量（核心汇率））
- 🔬 [Day 118 — 爬楼梯的最少成本（Min Cost Climbing Stairs）+ 自我验证与反思（Self-Consistency / Self-Refine / 验证者悖论）](week18/2026-09-30-min-cost-climbing-stairs-self-verification.md)（Day 1 对偶回归：同一状态机计数加法 vs 最优取 min / `f(i)=cost[i]+min(f(i-1),f(i-2))` 初值踏上付费答案登顶免费 / k 级扩展 = 滑动窗口最小值+单调队列 O(n) / 暴力枚举 2ⁿ 爬法 = 采样全集投票·DP 折叠成 O(n) 精确计算「能 DP 别投票」 / Self-Consistency 独立性假设+1−(1−p)^N+系统性错误一致地错 / 改进：verifier 加权·Early-Stopping 省 ~40% / Kamoi 2024 冷水：intrinsic self-correction GSM8K 95%→82.9% / 验证者悖论三段式+三出路：外包解耦·RL 涌现 R1 实证·rStar 互证 / Reflexion verbal RL·HumanEval 91% 档 / CoVe 四步独立作答防锚定 / Self-Rewarding 自评→DPO 迭代·分布坍缩风险）
- 🔬 [Day 117 — N 皇后（N-Queens）+ 推理树搜索（ToT / MCTS / rStar）](week18/2026-09-29-n-queens-tree-search.md)（按行建模决策变量 n²→n（2^(n²)→n!）/ 三向冲突 O(1)：cols·diag1[r-c+n-1]·diag2[r+c] 先剪枝后扩展 / 位运算版 avail=full&~(cols|d1|d2)·diag1<<1·diag2>>1 三行核心 / 冲突检查=树搜索廉价评估函数·评估越便宜越准树越小 / CoT 单链贪心 vs 树搜索中途纠偏总表 / ToT 四操作：分解·生成 k 候选·评估·搜索（评估器是命门·Goodhart）/ MCTS 四步循环+UCB1 默写·LLM 化四问·Simulation 三档成本-质量权衡 / rStar 判别式互证免训练 verifier·小模型边际收益更大 / 三范式对比表·搜索↔RL 飞轮·o1 内化路线之争：外挂搜索 vs 焊进权重）
- 🔬 [Day 116 — 单词搜索（Word Search）+ 过程奖励模型（PRM）](week18/2026-09-28-word-search-process-reward-model.md)（回溯三件套：原地标记+四方向 DFS+退出撤销 / 前缀验证剪枝，Trie 前缀检查 = 算法版过程验证器 / Follow-up Word Search II：Trie 建图+共享前缀剪枝，10⁵ 词场景，进阶 AC 自动机 / credit assignment 归因黑洞 → 稠密过程信号 / PRM800K 人工标注 → MATH-Shepherd MCTS rollout 自动标注 / ORM vs PRM 七维对照表 / PRM 三用途：RL shaping·推理搜索引导·数据筛选 / 四坑：装模作样 hack·step 边界·OOD·验证成本 / veRL 落地：per-turn reward + 轨迹过滤）
- 🧩 [Day 115 — 解数独（Sudoku Solver）+ Week 18 开启：推理模型与 Test-Time Compute](week18/2026-09-27-sudoku-solver-test-time-compute-intro.md)（位运算三 bitmap 压缩行/列/宫约束 + `&^=` 撤销 + MRV 最少候选优先 + 前向检查 / 唯一解挖洞生成 · 一般化数独 NP-complete · 并行化求解 / Scaling Law 两根轴：训练 FLOPs ↔ 推理 FLOPs / o1 配方 = RL + 长 CoT + 可验证奖励（GRPO 引擎）/ 三类 test-time 策略表：Best-of-N·Self-Consistency · Self-Refine · ToT·MCTS·rStar / "验证比生成容易"= P vs NP 直觉 → verifier 训练推理两用 / compute-optimal 按难度分配算力 / verifier 四类成本谱：规则·执行·PRM·LLM-Judge / test-time compute ↔ veRL rollout 一体两面）

> 📅 **每天 20:42 自动更新**,[查看全部历史 →](https://github.com/albert-lv/interview-prep/commits/main)

---

## 🚀 怎么使用?

### 方式一:跟着走(推荐)

1. 点右上角 ⭐ **Star** 本仓库(给自己一点仪式感)
2. 点 👁️ **Watch** 接收每日更新通知(推荐选 "Releases only" 或 "All Activity")
3. 每天 20:42 来看当日更新,或等 GitHub 通知推送
4. 按周推进,周六复盘这周的内容
5. 面试前一周,快速过一遍 `memory/interview-prep.md` 的进度索引

> 💬 **真实的每日更新** - 不是一次性写完的题库,是真的每天都在 push 新内容。你可以看 [commit 历史](https://github.com/albert-lv/interview-prep/commits/main) 验证。

### 方式二:按需查阅

| 你想找 | 去哪看 |
|---|---|
| 某道算法题 | `weekXX/YYYY-MM-DD-topic.md` |
| 某个面试考点速查 | 当日的「面试技巧」章节 |
| 系统学习某主题 | 按周顺序阅读,主题集中 |
| 学习进度追踪 | `memory/interview-prep.md` |
| Agent 工具技巧 | `agent-tips/2026/MM/YYYY-MM-DD-agent-tips.md` |

### 方式三:本地跑代码

```bash
git clone https://github.com/albert-lv/interview-prep.git
cd interview-prep/week10
gcc 2026-08-10-io-models.c -o io_demo && ./io_demo
```

---

## 💡 内容特色

### 1. 算法题 = 面试现场复刻

不只是 LeetCode 题解,而是:
- **题目变种** - 面试官常问的 follow-up
- **边界 case** - 那些你面试时会漏掉的 corner case
- **复杂度分析** - 时间 + 空间,还要能说清楚为什么

示例(Day 66 - 滑动窗口最大值):
> 💬 **面试官**:单调队列还能优化吗?
>
> 🎯 **你**:可以,如果数据流是无限的,用双端队列维护窗口内递减序列,均摊 O(1)。如果要求第 k 大而不是最大,可以改用两个堆......

### 2. 面试技巧 = 话术模板

不是背书,是可复用的「回答框架」:

- **epoll 为什么比 select 快?** → 三层回答模板(数据结构 + 触发机制 + 无遍历)
- **TCP 为什么三次握手?** → 「信道可靠性 + 防止旧连接初始化」双角度
- **Redis 单线程为什么快?** → 「纯内存 + IO 多路复用 + 避免上下文切换」三件套

### 3. 代码 = 可运行 + 有注释

```c
// 边缘触发(ET)模式下必须循环读到 EAGAIN
while ((n = read(fd, buf, sizeof(buf))) > 0) {
    // 处理数据...
}
if (n == -1 && errno == EAGAIN) {
    // 正常读完,等待下一次 epoll_wait
}
// ❌ 常见坑:ET 模式只读一次,数据会粘在内核缓冲区!
```

---

## 📊 进度追踪

当前进度：**Week 18 / Day 119**（Week 18 主题：推理模型与 Test-Time Compute 🔬 — Day 119 进行中：最长重复子数组 = LCS 连续版对偶（不匹配清零而非继承历史最优，滚动数组倒序压缩），核心隐喻「DP 折叠重叠子结构 ↔ Prefix Caching 折叠共享前缀」与 Day 1 闭环；面试技巧推理系统工程四大账本——KV cache 显存公式（2×层数×KV头数×头维×字节×长度，Llama-7B fp16 ≈ 512KB/token）、PagedAttention（OS 虚拟内存思路：16-token 块+block table+按需+COW，碎片<4%）、prefix caching（块级内容哈希，GRPO 组共享前缀 prefill O(G·L)→O(L) 省 30~60%）、PD 分离（prefill 算力型 ∥ decode 带宽型，TTFT/TPOT 各有 SLO）；长上下文三座大山（O(L²) prefill / O(L) 显存 / O(L) 带宽）与登山杖；延迟-质量交换所：serving 层买延迟（PagedAttention/prefix cache/PD 分离纯赚、speculative decoding 花算力、KV 量化微掉点）、test-time 层买质量（长 CoT 核心汇率、自适应预算、budget forcing）；明天 Day 120 Verifier 生态与 LLM-as-Judge 🔜）

**更新记录**：已连续更新 **119** 天，每日 20:42 自动推送。

详细进度见 [`memory/interview-prep.md`](memory/interview-prep.md)。

---

---

## 🤝 欢迎加入 / 一起打卡

这个仓库最初是个人学习笔记,但好东西值得分享。

- ⭐ **Star = 组队信号** - 每天来看新内容的人不止你一个
- 🐛 **发现错误?** 提 Issue 或 PR
- 📝 **有面试真题想补充?** 欢迎贡献
- 👀 **想看看是不是真的每天更新?** [查看 commit 历史](https://github.com/albert-lv/interview-prep/commits/main)

> 由 [albert-lv](https://github.com/albert-lv) 维护,**每日自动推送** + 不定期手动加餐。
>
> **不是一次性项目,是每天都在长的活文档。** 🔥

---

## 📜 License

MIT - 内容可自由使用,转载请注明出处。
