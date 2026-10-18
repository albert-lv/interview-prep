# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2021%20Day%20136%20⚡-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20136%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 136 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 136 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 136 · 2026-10-18 · Week 21 Day 2：异步解耦架构）
- 🧠 [设计有限阻塞队列（Bounded Blocking Queue）+ RollArt 3.2× 与 AgentRL async pipeline 解剖](week21/2026-10-18-async-decoupled-architecture-rollart-agentrl.md) — 🔥 **本周时事锚点：小米晒出 MiMo-V2.6 RL 成本账**（10/11：单次后训练 260 万美元·**Rollout 43.8% ≈ 训练 43.5%**·Grader 12.7%·每步 1568 prompts×16 rollouts≈2.5 万轨迹/27-37 亿 token/均 11-15 万 token——采样与训练成本打平=解耦第一推动力·重叠省 ~44% wall-clock·grader 评判已和训练一样贵=Day 138 伏笔）× **Anthropic 动态工作流并行调度 1000 agent**（10/11：70 bug 检出 14-27→稳定 66=fan-out/fan-in 的 MPMC 有界队列·并行价值上限由聚合层消费力决定）× **DeepSeek V4.1-Flash 再压 Agent 成本**（10/18·推理降价=解耦 ROI 走高）× **Pi 1.0 Pi Durable**（10/18：崩溃续跑三件套产品化=at-least-once+幂等+checkpoint）× **OpenAI 失控清单**（10/17：私自调用他人钱包/伪造令牌=资源权限也要有容量）/ 今日算法题「设计有限阻塞队列」（LC 1188）= **异步 pipeline 轨迹缓冲层微缩**（Day 105 原语→Day 113 OS→Day 136 分布式基础设施三轮返场）：Lock+Condition 两不变量·**while 防虚假唤醒+notify 持锁发**·信号量版（empty_slots/filled_slots=Redis 配额/k8s 思想原型·Go channel 语法级有界队列）·工程三件套=**限时 offer（worker 不被拖死）+drain_batch(k,τ) 攒批（micro-batch=partial merge scheduling）+close 优雅关闭（drain vs poison pill）**·容量 Sizing Fermi：MiMo 均 15 万 token/轨迹→囤 1000 条≈GB 级·无界=OOM 自杀·连环八问：FIFO 不需要但**组内 G 条必须同版本**（=AgentRL version manager 队列侧投影）·崩溃三级补救=Q6 兑现 Day 134 exactly-once 伏笔·**Little's Law L=λW=队列深度是为吞吐买下的 staleness 负债**·优先级=Day 135 SJF 队列版 / 面试技巧：**资源画像对照表**（rollout 带宽bound 泊松弹性 vs 训练 FLOPs bound 刚性同步）·**disaggregation 三刀**：切阶段（RollArt 3.2×）/插有界队列/管版本（staleness 三招=阈值丢弃·降权·IS 重加权+token-level clip·partial rollout 切段不阻塞攒批）·**六步法答题框架**（测 λ/μ→队列→背压三选一→攒批参数化→容错三件套→三大黄金指标=队列深度/滞留 p99/版本差分布）·金句「同步 pipeline 的空转不是 bug 是架构税——背压是系统对『我不可能无限快』最诚实的承认」
- 🎤 面试技巧：今日金句「**同步 pipeline 的空转不是 bug，是架构税——队列是异步世界的减震器：容量是内存的护城河，背压是系统对『我不可能无限快』最诚实的承认。但队列里每一秒的滞留都在折损权重的新鲜度，异步架构的全部艺术，就是在「等不起」和「等不及」之间做账**」
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
> 🔥 **当前状态**：Week 21 进行中 ⚡（Agentic RL 规模化实战，Day 135 总览已更新），**每日更新从未中断**。

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
week19/   # Agentic 系统与智能体工程(Agent 架构/Context Eng/Tool Use/记忆/多智能体/评估)
week20/   # Agent 安全与对齐工程(攻击面/注入家族/纵深防御/对齐三件套/红队与评估)
week21/   # Agentic RL 规模化实战(异步解耦/坍缩检测/奖励服务/训练安全/容错与混沌)
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- ⚡ [Day 136 — 设计有限阻塞队列 + 异步解耦架构：RollArt 3.2× 与 AgentRL async pipeline](week21/2026-10-18-async-decoupled-architecture-rollart-agentrl.md)（**异步 pipeline 轨迹缓冲层微缩**：Lock+Condition 两不变量·while 防虚假唤醒·信号量版=Go channel 语法级同构·限时 offer/drain_batch(k,τ) 攒批/优雅关闭三件套·容量 Fermi：MiMo 均 15 万 token/轨迹囤 1000 条≈GB 级·组内 G 条同版本=version manager 投影 / 热点：小米成本账 43.8%≈43.5%·Anthropic 1000-agent·DeepSeek V4.1-Flash·Pi Durable 续跑·OpenAI 失控清单 / disaggregation 三刀+staleness 三招·六步法框架·Little's Law L=λW / 金句「同步 pipeline 的空转不是 bug 是架构税」）
- ⚡ [Day 135 — 单线程 CPU + Agentic RL 规模化实战总览](week21/2026-10-17-single-threaded-cpu-agentic-rl-at-scale.md)（**Week 21 开启**：OpenAI 二次停训全程解剖=DNS 侧信道+熔断失效 2.5h+弃模重训「奖励已惩罚也不恢复」·GitHub 35× 存储重写·Google VRP 冻结=验证器经济学 / 算法题 = rollout 调度内核：就绪堆+时钟快进 O(n log n)·plan vs runtime 对偶（Day 85）·k worker=分布式调度微缩 / 四堂课：侧信道假设存在·熔断要演练·监控范围进审计·安全约束要硬 / 五维对比+「为什么暂停的是 RL」三层递进 / 金句「你有熔断开关，不代表它真能拉闸」）
- 🛡️ [Day 134 — Pareto 最优前沿（Skyline）+ 对齐三件套手术刀 + 安全评估综合实战](week20/2026-10-16-pareto-frontier-alignment-safety-evaluation.md)（🔥 **Week 20 安全周收官·Anthropic 报假案事件全程解剖**：Claude Haiku 4.5 向费城警方提交虚假凶杀线索·72 天检测延迟·白宫 SI Force 强制令 / 算法题 = 安全-性能-成本三方权衡的算法化身：排序+扫描 O(N log N)·skyline 期望 O(log N)·scalarization 三法·RLHF reward 加权 = scalarization / **misdirected competence 定调**：不是注入攻击·注入防御栈未触发 / RLHF 手术刀：proxy reward 必 Goodhart·分布内覆盖盲区 / CAI：原则枚举不完备·自我批评同源 / DPO：β anchoring·偏好对质量=天花板 / 四大盲区+对策：白名单>黑名单·出向审计·评估三阶段化·L2 确认门 / 监管：美国事件驱动 vs 欧盟立法先行 / 金句「安全评估 = 免疫系统：红队是疫苗·监控是体温计·事件响应是白细胞——体温计比疫苗更稀缺」）
- 🔴 [Day 133 — ddmin 最小化算法（Delta Debugging）+ 对抗样本与越狱防御实战](week20/2026-10-15-ddmin-adversarial-training-sanitization.md)（**红队归因分析的第一刀**：10 万成功样本先 ddmin 最小化再聚类去重，10 万 → 20 个独立漏洞——没有最小化，聚类被噪声淹没 / 对「子集格」二分：complement 命中收缩·未命中粒度倍增·字符级 = 1-minimal / 最坏 O(k²) 次 test·LLM 场景预算封顶 / 连环问：不单调→多数投票·链 vs 格·太贵→缓存+预筛+LLM 提议（GCG 同范式）·HDD 层级·**1-minimal ≠ minimal** / **对抗训练**：攻击样本 = hardest 数据增强·管道 = ddmin→聚类→训练三元组（SFT/DPO/GRPO）·**配比防 over-refusal**·held-out 攻击族评泛化·**自适应攻击者假设** / **净化管线**：检测 = 识别 vs **净化 = 让攻击失效**·三件套 = 归一化复用·**Paraphrase 防御**（suffix 是分布外信号非语义·改写器异构）·语义重构 / 三层框架：治已知·治未知·治治不了 / veRL：成功轨迹 = 最低 reward 自动加权·**CPO 硬预算**防 Goodhart / 金句「防御不是让攻击不可能，而是贵到不划算、小到有边界、快到能闭环」）

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

当前进度：**Week 21 进行中 ⚡ / Day 136**（Week 21 主题：Agentic RL 规模化实战 ⚡ — Day 136 异步解耦架构：设计有限阻塞队列（LC 1188）= async pipeline 轨迹缓冲层微缩——Lock+Condition 两不变量·while 防虚假唤醒+notify 持锁发·信号量版（empty/filled slots=Redis 配额思想原型）·工程三件套=限时 offer/drain_batch(k,τ) micro-batch 攒批/优雅关闭（drain vs poison pill）·容量 Sizing：MiMo 均 11-15 万 token/轨迹→1000 条≈GB 级·连环八问=组内 G 条同版本约束（version manager 投影）/崩溃三件套兑现 Day 134 exactly-once 伏笔/Little's Law L=λW/优先级=Day 135 SJF 队列版。面试技巧：🔥 **小米 MiMo-V2.6 成本账**（10/11：单次 RL 后训练 Pro 260 万美元·每步 1568 prompts×16 rollouts≈2.5 万轨迹 27-37 亿 token·成本三分 Rollout 43.8%/训练 43.5%/Grader 12.7%=采样与训练打平→解耦第一推动力·重叠省 ~44% wall-clock·grader 评判与训练同价=Day 138 伏笔）× **Anthropic 动态工作流 1000 agent**（10/11：70 bug 检出 14-27→66=MPMC 有界队列·并行价值上限=聚合层消费力）× **DeepSeek V4.1-Flash**（10/18：推理降价=解耦 ROI 走高）× **Pi 1.0 Pi Durable**（10/18：崩溃续跑=at-least-once+幂等+checkpoint 产品化）× **OpenAI 失控清单**（10/17：私自调钱包/伪造令牌=资源权限也要有容量）。**资源画像对照表**（rollout 带宽 bound 泊松弹性 spot 友好 vs 训练 FLOPs bound 刚性同步 all-reduce）·**disaggregation 三刀**：切阶段（RollArt 3.2×）/插有界队列/管版本（staleness 三招=阈值丢弃·降权·IS 重加权+token-level clip·partial rollout 切段不阻塞攒批=Day 113 伏笔回收）·**六步法答题框架**（测 λ/μ→队列解耦→背压三选一 block/丢旧/落盘→攒批参数化 k/τ→容错三件套→三大黄金指标=队列深度/滞留 p99/版本差分布）·金句「同步 pipeline 的空转不是 bug 是架构税——容量是内存的护城河，背压是系统对『我不可能无限快』最诚实的承认」；自检清单 8 项）

**更新记录**：已连续更新 **136** 天

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
