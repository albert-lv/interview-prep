# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2017%20Day%20114%20🏆-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20114%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 114 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 114 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 114 · 2026-09-26 · Week 17 Day 7 🏆）
- 🏆 [预测赢家 + Week 17 强化学习与 RL 训练工程综合复习](week17/2026-09-26-predict-winner-week17-review.md) — Week 17 收官日：算法题「预测赢家」minimax/negamax 区间 DP（`dp[i][j]=max(nums[i]−dp[i+1][j], nums[j]−dp[i][j−1])`，零和博弈单 dp 值、min 端=−max 端），偶数长度先手必胜配对论证，Alpha-Beta 剪枝与 AlphaZero 的连接；面试技巧综合复习：六天内容一张总表（MDP/Bellman→REINFORCE/AC→PPO→DPO/RLHF→GRPO→veRL）、两条主线（算法演进每步死穴与新坑 / 工程落地公式一页纸系统一箩筐）、10 道连环问通关速答（SARSA vs Q-Learning·baseline 无偏·clip 逐符号·DPO 三步推导·GRPO 删 Critic·k3 KL·R1-Zero·colocate·agentic 三大痛点·20 行 GRPO 循环）、收官加菜自博弈 Self-Play（policy+value 双头·self-play 数据·胜率替换，"minimax 是对手全知的 DP，self-play 是对手也在学的 RL"）
- 🎤 面试技巧：Q-Learning→GRPO 算法演进主线串讲、veRL 工程问答速查、RLHF/推理 RL 理解收尾话术一段话版

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
> 🔥 **当前状态**：Week 17 已完结（强化学习与 RL 训练工程 🏆），Week 18 主题筹备中，**每日更新从未中断**。

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
- 🏆 [Day 114 — 预测赢家（Predict the Winner）+ Week 17 强化学习与 RL 训练工程综合复习](week17/2026-09-26-predict-winner-week17-review.md)（minimax/negamax 区间 DP：`dp[i][j]=max(nums[i]−dp[i+1][j], nums[j]−dp[i][j−1])`·零和单 dp 值·min=−max / 偶数长度先手必胜配对论证·Alpha-Beta 剪枝 O(b^(d/2)) / Week 17 总表六天串讲：MDP·Bellman→REINFORCE·AC→PPO→DPO·RLHF→GRPO→veRL / 两条主线：算法演进死穴链 + 工程落地 rollout 70~90% / 10 道连环问通关速答 / 收官加菜自博弈 Self-Play：AlphaZero 三件套·MCTS·对手池防坍缩，"minimax 是对手全知的 DP，self-play 是对手也在学的 RL"）
- 🏭 [Day 113 — 设计阻塞队列（Bounded Blocking Queue）+ veRL：Rollout 基础设施与 Agentic RL 工程](week17/2026-09-25-blocking-queue-verl-rollout.md)（一把锁+两个条件变量：`while` 防虚假唤醒·Signal vs Broadcast·环形缓冲·双锁演进 / 背压 = 队列容量 / veRL=HybridFlow 单控制器 Ray driver / GRPO step 数据流七步 / colocate sleep/wake + NCCL 权重同步 / 吞吐四板斧：continuous batching·prefix caching 组内共享前缀·chunked prefill·partial rollout / agentic 三大痛点：长度爆炸·环境异构·信用分配 / 失败轨迹不能丢=防数据分布 bias / 选型：TRL·veRL·NeMo·OpenRLHF / 监控六指标）
- 🎰 [Day 112 — 打乱数组（Fisher-Yates 洗牌）+ GRPO 算法详解](week17/2026-09-24-shuffle-grpo.md)（Fisher-Yates 从后往前 `[0,i]` 取 j·每个排列 1/n! 归纳证明 / `rand() % n` 取模偏差与拒绝采样修复·rand.IntN·CSPRNG 场景 / 测试均匀性：卡方检验+seed 确定性 / 流式抽样：蓄水池·随机键排序·partial Fisher-Yates / 承诺方案可验证洗牌 / GRPO 全流程：每 prompt 采 G 条·可验证奖励·组内均值基线难度自校准 / A_i=(r_i−mean)/std 与 R1 去 std 版 / k3 KL 估计器 e^δ−δ−1 非负+无偏 / token 均值化长度偏置（Dr.GRPO）/ R1-Zero：base 纯 RL·AIME 15.6%→71.0%·aha moment / R1 配方：冷启动→GRPO→拒绝采样→SFT→GRPO / GRPO vs PPO vs DPO vs REINFORCE 总表 / rollout 三板斧：分离式推理引擎·partial rollout·长度控制）
- 🚤 [Day 111 — 救生艇 + DPO 与 RLHF 全流程](week17/2026-09-23-boats-dpo-rlhf.md)（排序+双指针贪心：最重的人要么独占要么带最轻的·交换论证·k 人座退化为装箱问题 / RLHF 三步流水线：SFT→RM→PPO / Bradley-Terry 成对比较→标量奖励·标度只需序 / PPO 阶段四模型在线的工程地狱 / DPO 三步推导：闭式解→反解 r→代入 BT 消 Z(x) / 隐式奖励 β·log π_θ/π_ref·模型自己就是 RM / loss 逐符号+PyTorch 最小实现·completion-only logps / β=0.1~0.5·只训 1 epoch / length bias·degeneration 两大病理 / IPO·KTO·ORPO·SimPO 变体 / veRL 视角：DPO 打底 GRPO 冲顶）
- 🎬 [Day 110 — 按权重随机选择 + PPO 算法详解](week17/2026-09-22-weighted-random-pick-ppo.md)（前缀和 + 二分 CDF 采样 O(log n) / 动态加权：树状数组 / 分布式：Efraimidis-Spirakis / 裸策略梯度三大痛点：on-policy 浪费·步长玄学·更新无约束 / 重要性采样 r_t(θ)=π_θ/π_old 无偏方差炸 / TRPO 硬 KL 约束二阶 → PPO clip 软信赖域一阶 / L^CLIP 逐符号解读 + 悲观界 / 完整流程：采样→GAE→K epoch 复用 / GAE λ 旋钮：γ 管多远 λ 信多少 / RLHF 映射：RM·KL 惩罚防 reward hacking·Critic 难训 / PPO vs REINFORCE·AC·DQN 总表）

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

当前进度：**Week 17 / Day 114**（Week 17 主题：强化学习与 RL 训练工程 🧠 — **已完结 🎉** Day 114 综合复习收官：minimax/negamax 博弈 DP + 全周串讲（MDP/Bellman → REINFORCE/AC → PPO → DPO/RLHF → GRPO → veRL rollout）+ 10 道连环问通关 + 自博弈 Self-Play 收官加菜；Week 17 从 Bellman 方程打到 veRL 流水线，算法与系统双通关）

**更新记录**：已连续更新 **114** 天，每日 20:42 自动推送。

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
