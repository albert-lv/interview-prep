# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2025%20Day%20160-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20160%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 160 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 160 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 160 · 2026-11-11 · Week 25 Day 2 ⚖️ 一致性日——权重同步与训练-推理一致性 + LC 295 数据流中位数回归 = 双堆 = 权重版本流的一致性水位线）
- ⚖️ [数据流中位数回归（LC 295 · 双堆 = 权重版本流的一致性水位线）+ 权重同步与训练-推理一致性——不一致四层来源 / 对齐三流派 / top-p logprob 定义陷阱 / vLLM Weight Transfer 四阶段 / 百万 worker 权重分发设计题](week25/2026-11-11-stream-median-weight-sync-consistency.md) — 🔥 **今日时事锚点 = Anthropic 更新招股书、路演正式启动（11/10-11：定价 $70-78、估值 1-1.4 万亿、904 亿总盘超 SpaceX 纪录、足额认购、11/19 挂牌——昨天撤回的定价文件今天正式"同步"）** × **Claude Fable 5.2 / Mythos 5.2 发布（11/9-10：路演当天发新权重版本·提速 30%·API 降价 20%）** × **vLLM 官方博客 11/6《Zero Mismatch, Simple Async RL》（REINFORCE Zero·DeepSeek-V4.1·Kimi K3.1·bitwise 对齐 + unclipped IS）** — 算法题「数据流中位数」= **双堆 = 权重版本流的一致性水位线**（LC 295 三天后回归·数据流=worker 心跳版本号·中位数=P50 一致性水位线·addNum O(log n)/findMedian O(1)=调度器每步免费体检·进阶六连：延迟删除双堆=LC 480·t-digest P90·加权中位数·分布式 sketch 可合并性·LC 4 二分·向量时钟偏序）·面试技巧：**不一致四层来源**（L1 数值 kernel/reduction order/精度·L2 语义 top-p 重归一化定义·L3 tokenization BOS/chat template·L4 版本漂移·排查二分法：step-0 大=定义问题·step-0 小但爬升=漂移问题）·**对齐三流派**（同引擎派 Slime/Miles：同一 SGLang server 生成+logprob 打分·Truly On-Pop bitwise identical·GLM-5.3 logprob diff 1e-7 降 99.99% ‖ 解耦但 token 一致派 MiniMax Forge：推理引擎持久化全部 log+Data Coordinator 抽取精确 token 序列·**FP32 LM head fix**[M1 教训：LM head 精度→相关性 0.9x→0.99x·一个层停掉整个训练]·CISPO clip IS 权重保梯度 ‖ 显式分离派 OpenRLHF：vLLM 生成+HF 打分·接受小不一致·agentic 长轨迹第一个撞墙）·**军备赛三数字**（vLLM 10/14：DeepSeek-V4-Flash 100 步 diff=0@91.3% 吞吐·KL 2e-3→29.9 reward 回落教训·Nemotron SWE-bench 0.56→0.22 vs 守住 0.56 ‖ ROCm vime×RL-Kernel 200 步零 mismatch·vime 对齐时间线+RL-Kernel 对齐数值执行 ‖ vLLM 11/6：数值一致性和策略滞后是两个独立问题·前者归零后者才能被 IS 干净度量）·**top-p 定义陷阱**（processed_logprobs=截断重归一化后 vs raw=全词表·gap 跳 0.053·"减常量"错因=kept mass 74-79% token<0.99 逐 token 不同向·正解=raw 模式或 replay 模式统一比较对象·**两个系统比较一个数之前必须先同意它的定义**）·**权重广播五板斧**（量化传输 bf16→fp8·QATFactory inference-ready 打包·RDMA/GPU-direct 400G<2s vs 100G 8-12s·分块流水·树形环形广播·MoE 热 expert 增量·AReaL #1560 PP size 限制 bug）·**vLLM Weight Transfer 四阶段**（init/start/update/finish·NCCL+CUDA IPC·pause mode 解 DPEP 死锁）·**设计题：百万 worker 权重分发**（三澄清[权重大小/时效/网络]→三层分发[权威源→区域树→P2P swarming=CDN 同构]→版本号+Merkle 哈希先校验后切换=两阶段提交→版本水位线 t-digest P50/P90 O(1) 读→灰度 1% 观察 5 分钟·主线=不追强一致·追求可度量可告警最终一致）·金句「RL 的 importance ratio 是两个系统对同一个 token 的证词——权重版本不一致是时间差·数值不一致是口径差·token 不一致是对象差」
- 🎤 面试技巧：今日金句「**RL 训练的 importance ratio 是两个系统对同一个 token 的"证词"——权重版本不一致是"时间差"，数值不一致是"口径差"，token 不一致是"对象差"。Anthropic 昨天把错误的定价广播给了 22 家银行，今天更新招股书重新同步——撤回邮件就是它的重要性采样修正；而它路演当天发布 Fable 5.2、API 降价 20%，提醒我们权重版本从来不只是技术对象，它同一天改写了所有人的账本。所以训练-推理一致性的全部要义是三句话：比较之前先同意定义（top-p 陷阱），广播之前先确认状态（两阶段提交），优化之前先对齐口径（bitwise 或至少同 sketch）。**」
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
> 🔥 **当前状态**：Week 25「Agentic RL 训练基础设施实战」**进行中（Day 159/Week 25 Day 1 🚀）**（Day 159 rollout 基础设施与异步解耦 ✓ → Day 160 权重同步与训练-推理一致性 → Day 161 奖励服务与 rollout worker 容错 → Day 162 训练安全与成本工程 → Day 163 收官复盘），**每日更新从未中断**。上一周：Week 24「长时程 Agent 工程」已收官（Day 154 checkpoint 双指打字 ✓ → Day 155 心跳与健康检查×可观测性 ✓ → Day 156 容错分类与自动恢复 ✓ → Day 157 混沌测试与故障注入 ✓ → Day 158 收官混合模拟面试 ✓）。

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
week22/   # Inference 系统工程(serving 架构/KV cache 与 prefix caching/模型路由与语义缓存/网关预算护栏/推理成本工程)
week23/   # Multi-Agent 工程实战(协调协议与共识/agent 间消息与黑板/多 agent 预算与结算/群体容错与拜占庭行为/评测与红队规模化)
week24/   # 长时程 Agent 工程(状态持久化与 checkpoint/心跳与健康检查/可观测性/容错分类与自动恢复/混沌测试与故障注入)
week25/   # Agentic RL 训练基础设施实战(rollout 与异步解耦/权重同步与一致性/奖励服务与容错/训练安全与成本/收官复盘)
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- ⚖️ [Day 160 — 数据流中位数回归 / 权重同步与训练-推理一致性（LC 295 · 双堆 = 权重版本流的一致性水位线）](week25/2026-11-11-stream-median-weight-sync-consistency.md)（**双堆三天后回归**：数据流=worker 心跳版本号·中位数=P50 一致性水位线·addNum O(log n)/findMedian O(1)=调度器每步免费体检·进阶：延迟删除=LC 480·t-digest P90·加权中位数·分布式 sketch 可合并性·向量时钟偏序 / 热点：**Anthropic 更新招股书路演正式启动（11/10-11：定价 $70-78·估值 1-1.4 万亿·904 亿超 SpaceX 纪录·足额认购·11/19 挂牌·昨天撤回的定价文件今天正式同步=现实版两阶段提交 commit）**·**Fable 5.2/Mythos 5.2 发布（11/9-10：路演当天发新权重版本·API 降价 20%）**·**vLLM 博客 11/6《Zero Mismatch, Simple Async RL》（REINFORCE Zero·DeepSeek-V4.1·Kimi K3.1·bitwise 对齐+unclipped IS）** / 面试技巧：**不一致四层来源**（L1 数值·L2 语义 top-p 定义·L3 tokenization·L4 版本·排查二分法：step-0 大=定义问题·小但爬升=漂移）·**对齐三流派**（同引擎 Slime/Miles bitwise identical·GLM-5.3 diff 1e-7 降 99.99% ‖ 解耦但 token 一致 MiniMax Forge·FP32 LM head fix[M1 教训：一个层停掉整个训练]·CISPO ‖ 显式分离 OpenRLHF·agentic 第一个撞墙）·军备赛三数字（vLLM 100 步 diff=0@91.3%·ROCm 200 步零 mismatch·11/6 数值一致性与策略滞后是两个独立问题）·**top-p 定义陷阱**（processed vs raw logprob·gap 跳 0.053·比较前先同意定义·减常量=逐 token 留 bias）·**权重广播五板斧**（量化 fp8·QAT inference-ready 打包·RDMA 400G<2s·分块流水·P2P swarming·AReaL #1560）·**vLLM Weight Transfer 四阶段**（init/start/update/finish）·**百万 worker 权重分发设计题**（三澄清→三层分发→Merkle 先校验后切换→版本水位线→灰度 1%·主线=可度量可告警最终一致）·金句「权重版本不一致是时间差·数值不一致是口径差·token 不一致是对象差」）
- 🚀 [Day 159 — 任务调度器 / rollout 基础设施与异步解耦（LC 621 · 堆 = 冷却期调度 = 训练任务排队 = rollout 队列的原子结构）](week25/2026-11-10-task-scheduler-rollout-infra.md)（**贪心模拟+最大堆**（每窗口 n+1 个不同任务·塞不满才待机·冷冻区本轮不放回·公式法 (maxCount-1)×(n+1)+并列数 与 len(tasks) 取 max·O(N log K)/O(K)）·工程映射=**带冷却约束的资源排程**（冷却期 n = backend 权重切换预热/sandbox 复用重置·待机 = GPU 空转·贪心「剩余次数最多优先」= 最容易成瓶颈的任务最先处理） / 热点：**Anthropic 定价文件发出又撤回（11/10 今天·路演启动日悬疑剧：8.667 亿股 @ $70-80 最高 693 亿美元说撤就撤·估值 2 万亿 → 1.1-1.5 万亿下修·260 亿美元私募配售锁定七家产业链巨头=互相持股铁证·工程读法：未确认状态就广播=两阶段提交少一阶段·rollout worker 重启必须拉最新 checkpoint）**·**TideRL readiness-aware 调度**（trainer 不傻等 straggler 按就绪度组 batch）·**ProRL Agent Rollout-as-a-Service**（min-heap 维护 LLM backend pool 动态注册+checkpoint swapping·HTTP 对接 veRL）·**Libra 弹性切换**（训练↔rollout 角色切换 <3.5% step time·异构 TP 路由）·**GLM-5.2 SAO**（放弃 π_old 跟踪·双边裁剪·单 rollout 消除组等待）·**veRL one-step off-policy 2.35-2.67x**·**APRIL 部分 rollout 44% 吞吐**·**RLinf 无状态 rollout worker 跑 spot 实例** / 面试技巧：异步化光谱三级（全同步 straggler 锁死 T_iter=max(T_rollout,T_train) → one-step off-policy 权重过期一步换零空转 → request-level async policy lag 需修正）·五角色表（GRPO 砍 Critic 动机）·分离 vs 共卡权衡（广播成本 vs 空转成本·Libra 第三答案）·速答五连（agentic vs RLHF 三差异/worker 挂了怎么办/off-policy 稳不稳/怎么省钱/为什么用堆）·金句「同步训练的 GPU 利用率由最慢的 episode 决定——都是尾部定价」）
- 🏁 [Day 158 — 合并 K 个升序链表 / Week 24 收官混合模拟面试（LC 23 · 多路归并 = 多源事件流合并 = 全局审计时间线）](week24/2026-11-09-k-way-merge-resilience-finale.md)（**小顶堆存每路当前头·弹最小补后继·O(N log k)/O(k)**·堆大小只跟 k 有关=外排序内存友好·分治两两合并缓存友好·败者树 O(1) 摊还[DBMS 标配]·进阶：LC 378/373/632·外排序·分布式归并=MapReduce 同构 / 热点：**Anthropic IPO 路演周今天启动（11/9 当周！）**（估值腰斩质疑 1.5 万亿→1-1.5 万亿=熔断止损·路演时间表 Plan B=checkpoint·尽调=GameDay·S-1 94 页=稳态假设文档·**万亿公司上市与 7×24 agent 生存跑同一套韧性协议**）·**Perplexity Teammates 上线两天 ×3.7（11/9）**（7×24 agent 产品化=韧性架构变 SLA 底座）·**论文 Monitor Accuracy Isn't the Answer（11/6：restart monitor 决策价值=checkpoint 质量才是关键）**·**Gartner 2028 年 50% GenAI 超预算**·百度 ERNIE Evolve RSI·千问 Team Agent / 面试技巧：全周一张图·四合一复盘·混合模拟面试四步答法·速答六连·金句「出了事，你得能回答第一秒到最后一秒每一秒发生了什么」）
- 🌀 [Day 157 — 数据流的中位数 / 混沌测试与故障注入（LC 295 · 双堆 = 稳态监控的分位数检测器）](week24/2026-11-08-stream-median-chaos-engineering.md)（**双堆平衡**（大顶堆存较小一半·平衡不变量 `size(maxHeap)∈{size(minHeap),+1}`·addNum O(log n)/findMedian O(1)·与 Day 156 单调队列对照：最大值有单调结构→单调队列·中位数没有→双堆）·工程映射=稳态监控分位数检测器原子结构（混沌实验靠实时 P50/P99 裁判·币安监控维度选错=分钟级延迟 vs 攻击者只需 20 分钟）·进阶：LC 480 延迟删除·t-digest·LC 4 二分/sketch 合并 / 热点：**币安 63.4 亿美元被盗完整报告（11/3）**（假面试社工→篡改 Homebrew→**内部 AI 工具横向移动**→EIP-7702+50 连发 Nonce 轰炸→4 小时 63 亿·四层教训：检测≠假设[6 月没入册→9 月同形状再注入]/内部工具=盲区/速率分位数>金额阈值/重建≠验证）·**GPT-6 Cyber（11/5）**（Firefox 81 0day+Linux 948 提权·攻防两侧 AI 化）·**费半 -12.25%**·**Anthropic IPO 路演拉锯（11/6 质疑→11/7 计划照旧）**·**微软拒甲骨文租约（FedRAMP）** / 面试技巧：混沌工程四原则·故障注入形状库四层分类（L5 行为层=agent 五类注入：决策退化/工具链/上下文溢出/协作失序/说谎者=AgentChaos crash/omission/value）·元数据三件套·爆炸半径分级+护栏三件套·稳态四件套（速率看分位数/金额看阈值/状态看快照/行为看基线）·GameDay 七步+两份强制交付清单·从混沌到韧性（Chaos Mesh 3.x+Gremlin Foresight AI·故障免疫指数）·金句「攻击者的注入形状库每天都在更新，防御方的更新速度决定损失的天花板」）


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

当前进度：**Week 25 进行中 / Day 160**（Week 25 Day 2 ⚖️ 一致性日：权重同步与训练-推理一致性。Day 160 数据流中位数回归 = LC 295 双堆 = 权重版本流的一致性水位线（worker 心跳版本号流·P50 水位线=调度器每 step 免费体检·addNum O(log n)/findMedian O(1)）·面试技巧：不一致四层来源（L1 数值 kernel/精度·L2 语义 top-p 定义·L3 tokenization·L4 版本·排查二分法 step-0 大=定义/小但爬升=漂移）·对齐三流派（同引擎 Slime/Miles bitwise·GLM-5.3 diff 1e-7 ‖ 解耦但 token 一致 MiniMax Forge FP32 LM head ‖ 显式分离 OpenRLHF）·军备赛三数字（vLLM 100 步 diff=0@91.3%·ROCm 200 步零 mismatch·vLLM 11/6 数值一致性与策略滞后是两个独立问题）·top-p 定义陷阱（processed vs raw·比较前先同意定义）·权重广播五板斧（量化 fp8·RDMA·分块流水·树形广播·MoE 热 expert·AReaL #1560）·vLLM Weight Transfer 四阶段（init/start/update/finish）·百万 worker 权重分发设计题（三层分发+Merkle 校验+版本水位线+灰度）·热点：**Anthropic 更新招股书路演正式启动（11/10-11：$70-78·1-1.4 万亿·904 亿超 SpaceX·足额认购·11/19 挂牌=昨天撤回的定价今天正式同步）**·Fable 5.2/Mythos 5.2（路演当天发新权重版本·API 降价 20%）·vLLM 博客 11/6 Zero Mismatch（REINFORCE Zero·DeepSeek-V4.1·Kimi K3.1）；Week 25 Day 1：Day 159 任务调度器（LC 621·堆=冷却期调度）已完成。**Day 161 奖励服务与 rollout worker 容错明日继续**）

**更新记录**：已连续更新 **160** 天

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
