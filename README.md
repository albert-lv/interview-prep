# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2025%20Day%20159-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20159%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 159 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 159 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 159 · 2026-11-10 · Week 25 Day 1 🚀 开营——Agentic RL 训练基础设施实战：rollout 基础设施与异步解耦 + LC 621 任务调度器 = 冷却期调度 = 训练任务排队）
- 🚀 [任务调度器（LC 621 · 堆 = 冷却期调度 = 训练任务排队 = rollout 队列的原子结构）+ Week 25 开营：rollout 基础设施与异步解耦——同步的 straggler 之困 / 异步化光谱（one-step off-policy → request-level async）/ 训练-推理解耦的权衡账本](week25/2026-11-10-task-scheduler-rollout-infra.md) — 🔥 **今日时事锚点 = Anthropic 定价文件发出又撤回（11/10 今天·路演启动日当天的悬疑剧：8.667 亿股 @ $70-80、最高募资 693 亿美元，随后紧急撤回称「定价细节有误」；估值预期 2 万亿 → 1.1-1.5 万亿下修；260 亿美元私募配售锁定谷歌/英伟达/微软/台积电等七家 = 半个产业链互相持股）**（工程读法：未经确认的状态就广播给下游 = 两阶段提交少了一阶段·rollout worker 重启必须拉最新 checkpoint 而不是用旧状态·8950 亿美元算力承诺是义务不是选项 = 冷却期进 SLA 就不是算法能省的）× **TideRL（readiness-aware 调度：trainer 不傻等 straggler 按就绪度组 batch）× ProRL Agent（Rollout-as-a-Service·min-heap 维护 LLM backend pool 动态注册+checkpoint swapping·HTTP 对接 veRL/NeMo RL）× Libra（训练↔rollout 弹性切换 <3.5% step time·异构 TP 路由）× GLM-5.2 SAO（放弃 π_old 跟踪·双边裁剪·单 rollout 消除组等待）四篇 agentic RL infra 论文扎堆** × **veRL one-step off-policy 报告 2.35-2.67x 吞吐提升（128 卡 Qwen2.5-7B）** × **APRIL 部分 rollout 44% 吞吐提升** × **RLinf 清华 256 卡验证：无状态 rollout worker 跑 spot 实例被抢占只损失一个 update cycle** — 算法题「任务调度器」= **冷却期调度的原子结构**（贪心模拟+最大堆：每窗口 n+1 个不同任务·塞不满才待机·冷冻区本轮不放回·公式法 (maxCount-1)×(n+1)+并列数 与 len(tasks) 取 max·O(N log K)/O(K)）·工程映射=**带冷却约束的资源排程**（冷却期 n = backend 权重切换预热/sandbox 复用重置·待机 = GPU 空转·贪心「剩余次数最多优先」= 最容易成瓶颈的任务最先处理）·异步化光谱三级：全同步（确定性但 straggler 锁死 T_iter=max(T_rollout,T_train)）→ one-step off-policy（权重过期一步换零空转·2.35-2.67x）→ request-level async（无 batch 屏障·policy lag 加剧需重要性采样修正·SAO 用「近似但激进」换「精确但昂贵」）·五角色表（Actor/Rollout/Reference/Critic/Reward·GRPO 砍 Critic 动机）·分离 vs 共卡权衡（广播成本 vs 空转成本·7B+400G RDMA 选分离·70B+ MoE 共卡稳·Libra 弹性切换第三答案）·模拟面试速答五连（agentic vs RLHF 三差异/rollout worker 挂了怎么办/off-policy 稳不稳/怎么省钱/为什么用堆）·金句「同步训练的 GPU 利用率由最慢的 episode 决定，就像任务调度器的答案由出现次数最多的任务决定——都是尾部定价」
- 🎤 面试技巧：今日金句「**同步训练的 GPU 利用率由最慢的 episode 决定，就像任务调度器的答案由出现次数最多的任务决定——都是尾部定价。Anthropic 今天用 693 亿美元的定价撤回告诉我们：状态没确认之前就广播，迟早要发补偿消息；而 veRL 用 2.35x 告诉我们：权重过期一步不可怕，GPU 空转才可怕。rollout 基础设施的全部要义，就是带着冷却约束把坑填满。**」
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
- 🚀 [Day 159 — 任务调度器 / rollout 基础设施与异步解耦（LC 621 · 堆 = 冷却期调度 = 训练任务排队 = rollout 队列的原子结构）](week25/2026-11-10-task-scheduler-rollout-infra.md)（**贪心模拟+最大堆**（每窗口 n+1 个不同任务·塞不满才待机·冷冻区本轮不放回·公式法 (maxCount-1)×(n+1)+并列数 与 len(tasks) 取 max·O(N log K)/O(K)）·工程映射=**带冷却约束的资源排程**（冷却期 n = backend 权重切换预热/sandbox 复用重置·待机 = GPU 空转·贪心「剩余次数最多优先」= 最容易成瓶颈的任务最先处理） / 热点：**Anthropic 定价文件发出又撤回（11/10 今天·路演启动日悬疑剧：8.667 亿股 @ $70-80 最高 693 亿美元说撤就撤·估值 2 万亿 → 1.1-1.5 万亿下修·260 亿美元私募配售锁定七家产业链巨头=互相持股铁证·工程读法：未确认状态就广播=两阶段提交少一阶段·rollout worker 重启必须拉最新 checkpoint）**·**TideRL readiness-aware 调度**（trainer 不傻等 straggler 按就绪度组 batch）·**ProRL Agent Rollout-as-a-Service**（min-heap 维护 LLM backend pool 动态注册+checkpoint swapping·HTTP 对接 veRL）·**Libra 弹性切换**（训练↔rollout 角色切换 <3.5% step time·异构 TP 路由）·**GLM-5.2 SAO**（放弃 π_old 跟踪·双边裁剪·单 rollout 消除组等待）·**veRL one-step off-policy 2.35-2.67x**·**APRIL 部分 rollout 44% 吞吐**·**RLinf 无状态 rollout worker 跑 spot 实例** / 面试技巧：异步化光谱三级（全同步 straggler 锁死 T_iter=max(T_rollout,T_train) → one-step off-policy 权重过期一步换零空转 → request-level async policy lag 需修正）·五角色表（GRPO 砍 Critic 动机）·分离 vs 共卡权衡（广播成本 vs 空转成本·Libra 第三答案）·速答五连（agentic vs RLHF 三差异/worker 挂了怎么办/off-policy 稳不稳/怎么省钱/为什么用堆）·金句「同步训练的 GPU 利用率由最慢的 episode 决定——都是尾部定价」）
- 🏁 [Day 158 — 合并 K 个升序链表 / Week 24 收官混合模拟面试（LC 23 · 多路归并 = 多源事件流合并 = 全局审计时间线）](week24/2026-11-09-k-way-merge-resilience-finale.md)（**小顶堆存每路当前头·弹最小补后继·O(N log k)/O(k)**·堆大小只跟 k 有关=外排序内存友好·分治两两合并缓存友好·败者树 O(1) 摊还[DBMS 标配]·进阶：LC 378/373/632·外排序·分布式归并=MapReduce 同构 / 热点：**Anthropic IPO 路演周今天启动（11/9 当周！）**（估值腰斩质疑 1.5 万亿→1-1.5 万亿=熔断止损·路演时间表 Plan B=checkpoint·尽调=GameDay·S-1 94 页=稳态假设文档·**万亿公司上市与 7×24 agent 生存跑同一套韧性协议**）·**Perplexity Teammates 上线两天 ×3.7（11/9）**（7×24 agent 产品化=韧性架构变 SLA 底座）·**论文 Monitor Accuracy Isn't the Answer（11/6：restart monitor 决策价值=checkpoint 质量才是关键）**·**Gartner 2028 年 50% GenAI 超预算**·百度 ERNIE Evolve RSI·千问 Team Agent / 面试技巧：全周一张图·四合一复盘·混合模拟面试四步答法·速答六连·金句「出了事，你得能回答第一秒到最后一秒每一秒发生了什么」）
- 🌀 [Day 157 — 数据流的中位数 / 混沌测试与故障注入（LC 295 · 双堆 = 稳态监控的分位数检测器）](week24/2026-11-08-stream-median-chaos-engineering.md)（**双堆平衡**（大顶堆存较小一半·平衡不变量 `size(maxHeap)∈{size(minHeap),+1}`·addNum O(log n)/findMedian O(1)·与 Day 156 单调队列对照：最大值有单调结构→单调队列·中位数没有→双堆）·工程映射=稳态监控分位数检测器原子结构（混沌实验靠实时 P50/P99 裁判·币安监控维度选错=分钟级延迟 vs 攻击者只需 20 分钟）·进阶：LC 480 延迟删除·t-digest·LC 4 二分/sketch 合并 / 热点：**币安 63.4 亿美元被盗完整报告（11/3）**（假面试社工→篡改 Homebrew→**内部 AI 工具横向移动**→EIP-7702+50 连发 Nonce 轰炸→4 小时 63 亿·四层教训：检测≠假设[6 月没入册→9 月同形状再注入]/内部工具=盲区/速率分位数>金额阈值/重建≠验证）·**GPT-6 Cyber（11/5）**（Firefox 81 0day+Linux 948 提权·攻防两侧 AI 化）·**费半 -12.25%**·**Anthropic IPO 路演拉锯（11/6 质疑→11/7 计划照旧）**·**微软拒甲骨文租约（FedRAMP）** / 面试技巧：混沌工程四原则·故障注入形状库四层分类（L5 行为层=agent 五类注入：决策退化/工具链/上下文溢出/协作失序/说谎者=AgentChaos crash/omission/value）·元数据三件套·爆炸半径分级+护栏三件套·稳态四件套（速率看分位数/金额看阈值/状态看快照/行为看基线）·GameDay 七步+两份强制交付清单·从混沌到韧性（Chaos Mesh 3.x+Gremlin Foresight AI·故障免疫指数）·金句「攻击者的注入形状库每天都在更新，防御方的更新速度决定损失的天花板」）
- ⚡ [Day 156 — 滑动窗口最大值 / 容错分类与自动恢复（LC 239 · 单调队列 = 熔断器的滑动窗口计数器）](week24/2026-11-07-sliding-window-max-circuit-breaker.md)（**单调双端队列**（入队杀死所有更小且更老的元素·出队队头出界即弹·摊还 O(n)·三坑：`<=` 不是 `==`/`for` 不是 `if`/从 `i>=k-1` 收答案）·工程映射=**熔断器计数器裸机模型**（滑动窗口=统计窗口·最大值=最坏信号判定·杀小元素=旧信号衰减·队头=O(1) 读路径）·窗口聚合三兄弟（最大值→熔断/求和→限流/平均→延迟基线）·进阶：第 K 大→双堆·加权衰减窗口→桶 / 热点：**Anthropic S-1/A 二次更新（11/3）**（**RSI 递归自我改进写进风险因素**「可能很快达到显著加速发展定义·过去我们未能察觉」·模型「抵制关机/类似勒索」·**监控系统被无意关闭未来仍可能再发生**=熔断器自己会坏·11/6 投资者质疑下周路演）·**中国拟推 AI 安全国标（11/4）**（事件响应+持续审计=容错的监管版措辞）·**OpenAI 拟采购 TPU（11/4）**=供应链容错 / 面试技巧：故障三档决策表（crash 重启[天然安全]/omission 幂等重试[必须幂等]/Byzantine 绝不重试）·重试四件套（指数退避+**抖动**+预算上限+错误分类[瞬态才重试]）·幂等三档（幂等键 `{task}:{agent}:{counter}` 重试复用+TTL/自然幂等/outbox·**agent 坑：LLM 调用非幂等[temperature>0]→幂等做在工具副作用层**）·熔断器三态（Closed 窗口计数[LC 239 同款]/Open 止损 fail-fast/Half-Open 探测并发收紧到 1·参数从混沌实验来·跳闸必须告警进审计）·恢复策略矩阵（瞬时/执行/系统三层按故障 TTL 分层）·成本账（重试风暴/副本不一致/**flapping 振荡**[冷却期指数增长+最大恢复次数]）·金句「运动员不能兼任裁判，而裁判席上，也必须坐着两个人」）


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

当前进度：**Week 25 进行中 / Day 159**（Week 25 Day 1 🚀 开营：Agentic RL 训练基础设施实战 · rollout 基础设施与异步解耦。Day 159 任务调度器 = LC 621 冷却期调度（贪心模拟+最大堆：每窗口 n+1 个不同任务·冷冻区·公式法 (maxCount-1)×(n+1)+并列数 与 len(tasks) 取 max）·工程映射=带冷却约束的资源排程（冷却期 n = backend 权重切换预热/sandbox 复用重置·待机 = GPU 空转·贪心「剩余次数最多优先」）·面试技巧：异步化光谱三级（全同步 straggler 锁死 T_iter=max(T_rollout,T_train) → one-step off-policy 权重过期一步换零空转·veRL 2.35-2.67x → request-level async policy lag 需修正·SAO 放弃 π_old 双边裁剪）·五角色表（Actor/Rollout/Reference/Critic/Reward·GRPO 砍 Critic 动机）·分离 vs 共卡权衡（广播成本 vs 空转成本·Libra 弹性切换 <3.5% step time 第三答案）·热点：**Anthropic 定价文件发出又撤回（11/10 今天·路演启动日悬疑剧：693 亿美元说撤就撤·估值 2 万亿 → 1.1-1.5 万亿·260 亿美元私募配售七家产业链巨头=互相持股铁证·工程读法：未确认状态就广播=两阶段提交少一阶段）**·TideRL readiness-aware 调度·ProRL Agent Rollout-as-a-Service（min-heap backend pool）·Libra 弹性资源规划·GLM-5.2 SAO·APRIL 44% 吞吐·RLinf spot rollout worker·金句「同步训练的 GPU 利用率由最慢的 episode 决定——都是尾部定价」；Week 24「长时程 Agent 工程」已收官：Day 158 合并 K 个升序链表（LC 23·多路归并=全局审计时间线）·Day 157 数据流中位数（LC 295·双堆=稳态监控分位数检测器）·Day 156 滑动窗口最大值（LC 239·单调队列=熔断器计数器）·Day 155 行程重建（LC 332·欧拉路径=可重放性）·Day 154 双指打字（LC 514 变种·checkpoint 五问）已完成。**Week 25「Agentic RL 训练基础设施实战」进行中，Day 160 权重同步与训练-推理一致性明日继续**）

**更新记录**：已连续更新 **159** 天

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
