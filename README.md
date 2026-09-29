# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2018%20Day%20117%20🔬-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20117%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 117 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 117 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 117 · 2026-09-29 · Week 18 Day 3 🔬）
- 🔬 [N 皇后 + 推理树搜索（ToT / MCTS / rStar）](week18/2026-09-29-n-queens-tree-search.md) — 算法题「N 皇后」按行建模把决策变量从 n² 压到 n（搜索空间 2^(n²) → n!），三向冲突 `cols[c]` / `diag1[r-c+n-1]` / `diag2[r+c]` O(1) 评估先剪枝后扩展，位运算版 `diag1<<1 / diag2>>1` 平移三行核心代码（N 皇后 II 竞赛级解法）；隐喻：冲突检查 = 树搜索的廉价评估函数，评估越便宜越准树越小。面试技巧推理树搜索三件套：**ToT**（分解/生成 k 候选/评估/搜索 BFS+beam 或 DFS+回溯，评估器是命门），**MCTS** 四步循环默写（Selection UCB1 → Expansion → Simulation 三档：完整 rollout 贵准 / value model 单点便宜有偏 / 短程 rollout+PRM 折中 → Backpropagation），LLM 化改造四问（动作离散化采样 k 个 thought / pUCT 加 policy 先验 / 奖励=verifier+PRM 塑形），**rStar** 判别式互证免训练 verifier（Generator+Discriminator 两角色互问互答，小模型边际收益更大）；三范式对比表（评估器来源×训练成本×开销×致命伤），搜索↔RL 飞轮（AlphaZero 循环 + o1 内化路线之争：外挂搜索 vs 焊进权重）
- 🎤 面试技巧：ToT vs CoT 本质（生成重构为状态空间搜索）、ToT 评估器三条路与统一死穴 Goodhart、UCB1 公式各项含义、LLM 场景 Simulation 成本-质量三档、o1 为什么不外挂 MCTS（TPOT 爆炸+任务绑定，长 CoT 隐式搜索内化）、rStar 小模型起飞逻辑、搜索 vs 采样判断口诀「中途有可评估的岔路才值得建树」

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
> 🔥 **当前状态**：Week 18 进行中（推理模型与 Test-Time Compute 🔬，Day 115 开题解数独 + Test-Time Compute 总览，Day 116 单词搜索 + PRM 过程奖励模型 ✅，Day 117 进行中：N 皇后 + 推理树搜索 ToT/MCTS/rStar），Week 17 已完结，**每日更新从未中断**。

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
- 🔬 [Day 117 — N 皇后（N-Queens）+ 推理树搜索（ToT / MCTS / rStar）](week18/2026-09-29-n-queens-tree-search.md)（按行建模决策变量 n²→n（2^(n²)→n!）/ 三向冲突 O(1)：cols·diag1[r-c+n-1]·diag2[r+c] 先剪枝后扩展 / 位运算版 avail=full&~(cols|d1|d2)·diag1<<1·diag2>>1 三行核心 / 冲突检查=树搜索廉价评估函数·评估越便宜越准树越小 / CoT 单链贪心 vs 树搜索中途纠偏总表 / ToT 四操作：分解·生成 k 候选·评估·搜索（评估器是命门·Goodhart）/ MCTS 四步循环+UCB1 默写·LLM 化四问·Simulation 三档成本-质量权衡 / rStar 判别式互证免训练 verifier·小模型边际收益更大 / 三范式对比表·搜索↔RL 飞轮·o1 内化路线之争：外挂搜索 vs 焊进权重）
- 🔬 [Day 116 — 单词搜索（Word Search）+ 过程奖励模型（PRM）](week18/2026-09-28-word-search-process-reward-model.md)（回溯三件套：原地标记+四方向 DFS+退出撤销 / 前缀验证剪枝，Trie 前缀检查 = 算法版过程验证器 / Follow-up Word Search II：Trie 建图+共享前缀剪枝，10⁵ 词场景，进阶 AC 自动机 / credit assignment 归因黑洞 → 稠密过程信号 / PRM800K 人工标注 → MATH-Shepherd MCTS rollout 自动标注 / ORM vs PRM 七维对照表 / PRM 三用途：RL shaping·推理搜索引导·数据筛选 / 四坑：装模作样 hack·step 边界·OOD·验证成本 / veRL 落地：per-turn reward + 轨迹过滤）
- 🧩 [Day 115 — 解数独（Sudoku Solver）+ Week 18 开启：推理模型与 Test-Time Compute](week18/2026-09-27-sudoku-solver-test-time-compute-intro.md)（位运算三 bitmap 压缩行/列/宫约束 + `&^=` 撤销 + MRV 最少候选优先 + 前向检查 / 唯一解挖洞生成 · 一般化数独 NP-complete · 并行化求解 / Scaling Law 两根轴：训练 FLOPs ↔ 推理 FLOPs / o1 配方 = RL + 长 CoT + 可验证奖励（GRPO 引擎）/ 三类 test-time 策略表：Best-of-N·Self-Consistency · Self-Refine · ToT·MCTS·rStar / "验证比生成容易"= P vs NP 直觉 → verifier 训练推理两用 / compute-optimal 按难度分配算力 / verifier 四类成本谱：规则·执行·PRM·LLM-Judge / test-time compute ↔ veRL rollout 一体两面）
- 🏆 [Day 114 — 预测赢家（Predict the Winner）+ Week 17 强化学习与 RL 训练工程综合复习](week17/2026-09-26-predict-winner-week17-review.md)（minimax/negamax 区间 DP：`dp[i][j]=max(nums[i]−dp[i+1][j], nums[j]−dp[i][j−1])`·零和单 dp 值·min=−max / 偶数长度先手必胜配对论证·Alpha-Beta 剪枝 O(b^(d/2)) / Week 17 总表六天串讲：MDP·Bellman→REINFORCE·AC→PPO→DPO·RLHF→GRPO→veRL / 两条主线：算法演进死穴链 + 工程落地 rollout 70~90% / 10 道连环问通关速答 / 收官加菜自博弈 Self-Play：AlphaZero 三件套·MCTS·对手池防坍缩，"minimax 是对手全知的 DP，self-play 是对手也在学的 RL"）

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

当前进度：**Week 18 / Day 117**（Week 18 主题：推理模型与 Test-Time Compute 🔬 — Day 117 进行中：N 皇后按行建模+三向冲突 O(1) 评估先剪枝后扩展（冲突检查=树搜索廉价评估函数，评估越便宜越准树越小）+ 推理树搜索三件套 ToT/MCTS/rStar（ToT 四操作·评估器命门，MCTS 四步循环+UCB1 默写·Simulation 三档成本-质量权衡·pUCT policy 先验，rStar 判别式互证免训练 verifier·小模型边际收益更大；三范式对比表+搜索↔RL 飞轮+o1 内化路线之争：外挂搜索 vs 焊进权重）；本周从 verifier 核心（Day 116 PRM）进入搜索整机，明天 Day 118 自我验证与反思 Self-Consistency/Self-Refine 🔜）

**更新记录**：已连续更新 **117** 天，每日 20:42 自动推送。

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
