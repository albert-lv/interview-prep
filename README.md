# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2025%20Day%20161-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20161%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 161 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 161 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 161 · 2026-11-12 · Week 25 Day 3 🛟 容错日——奖励服务与 rollout worker 容错 + LC 460 LFU 缓存 = O(1) get/put = reward server 的热度分层 = APRIL buffer 的淘汰策略）- 🛟 [LFU 缓存（LC 460 · 三哈希表 + minFreq = O(1) get/put = reward server 热度分层 = APRIL buffer 淘汰策略）+ 奖励服务与 rollout worker 容错——reward 账本三本账 / worker 故障域三档（失联/卡死/半残）/ APRIL buffer 四要素 / 失败 episode 四类归因 / 设计题「十万 QPS 奖励服务」](week25/2026-11-12-lfu-cache-reward-service-fault-tolerance.md) — 🔥 **今日时事锚点 = Anthropic IPO 获数倍超额认购（彭博 11/12：承销商考虑上调发行价超 $78·完全摊薄估值冲 1.5 万亿·总募资或破 1000 亿·暗盘炒至 1.8 万亿·11/13 Amodei × Dimon 直播对谈——洪峰来了，无限放量还是上调价格？这是一道容量规划题）** × **智元 LIFE 框架（11/11：RoboBeats 心跳 + Plastic Memory 记忆 + EDM 能力复用——健康检查/checkpoint/失败归因的行业版三件套）** × **vLLM 博客 11/11《Encoder Disaggregation: EPD》（把 encoder 拆出 decode 进程 = 教科书级故障域划分）** × **Mercor 估值 200 亿 D 轮 7.5 亿（11/11：评估即产业·reward 是 RL 的燃料）** — 算法题「LFU 缓存」= **三哈希表 + minFreq 指针**（kv: key→[value,freq]·freqMap: freq→双向链表·get/put 全 O(1)·minFreq 唯二变更点：旧桶空且 f==minFreq 时 +1 / 插入新 key 复位 1·面试三坑：elem 摘除回写/minFreq 上移条件/put 已存在也走 touch·Go container/list 完整实现·进阶六连：LRU 对照[burst 刷榜 vs 新数据爬坡]·2Q/ARC 抗突发·TinyLFU[Caffeine·W-TinyLFU=窗口+Count-Min Sketch+准入策略]·带 TTL 的 LFU·分布式 LFU[sketch 可合并=加法就行]·加权 LFU[缓存是省钱游戏不是命中率游戏]）·工程映射=reward server 缓存分层（L0 规则→L1 轨迹哈希缓存 LFU 淘汰→L2 judge 池）+ APRIL buffer 淘汰（频率×新近性×信息密度=44% 吞吐的缓存读法）·面试技巧：**reward server 账本三轴**（QPS/P50·P99 从 T_iter 倒推/打分成功率·小比例缺分用最终一致补账）·**worker 故障域三档**（失联=心跳消失秒级重启拉 checkpoint ‖ 卡死=liveness 正常 progress 为零=L3 进展探针三层递进 ‖ **半残=行为基线偏离·最阴险·吞吐损失线性 vs 分布污染收敛性**）·**故障域划分三隔离**（进程内 cgroup/服务间 bulkhead 有界队列+超时/vLLM EPD 拆分故障性格不同组件）·**APRIL buffer 四要素**（存片段/续跑复用/LFU+新近性+加权淘汰/偏向完成 episode=受控对比方差更小）·**失败 episode 四类归因表**（环境故障→重试安全·RL 重试天然幂等=新采样 ‖ reward 故障→重打分零成本 ‖ 任务不可解→剔除反哺任务生成器 ‖ **模型答错→是训练信号千万别扔**·没有 reward 的 episode 才是废数据·低 reward 的是负样本）·设计题四步（三澄清→分层架构→**least in-flight LB[judge 长度方差大·秒杀一半候选人]**→按代价升序降级[每次降级打 degraded 标记·混进正常分=污染 reward 分布]·容量参考：绿鞋机制+上调定价=计划内弹性容量而非无限接单）·模拟面试速答五连（卡死排查/P99 飙 10 倍先背压后止损/半残先隔离再定量·分布污染是收敛性/分账报警不设总阈值·盯归因分布不盯失败总数/APRIL 提精度三机制=缓存淘汰策略就是数据课程策略）·金句「**Reward 是训练系统的收银台……容量永远给热度让路，故障永远先分域再处置；失败不是垃圾，是没排好序的资产。**」- 🎤 面试技巧：今日金句「**Reward 是训练系统的收银台：rollout 生产 episode，reward 给它们定价——定价慢了，整条流水线在等一张小票；定价挂了，今天的货全部白跑。所以 reward server 的全部要义是三句话：打分要快（缓存分层，让重复的轨迹直接记账）、坏 worker 要分档（失联的拉起来，卡死的踢出去，半残的隔离掉——最危险的是那个还能干活但越来越慢的）、失败要归因（环境坏了重试，judge 坏了重打分，模型答错了——那是训练信号，千万别扔）。Anthropic 的承销商在数倍超额认购面前选择绿鞋机制和上调价格而不是无限放量，智元给机器人接上心跳与记忆，vLLM 把 encoder 拆成独立故障域——三个行业，同一门课：容量永远给热度让路，故障永远先分域再处置。而 APRIL 用 44% 的吞吐告诉我们：失败不是垃圾，是没排好序的资产——每一条失败的 episode，都应该是下一次更便宜复用的库存。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 25「Agentic RL 训练基础设施实战」**进行中（Day 161/Week 25 Day 3 🛟 容错日已完成）**（Day 159 rollout 基础设施与异步解耦 ✓ → Day 160 权重同步与训练-推理一致性 ✓ → Day 161 奖励服务与 rollout worker 容错 ✓ → Day 162 训练安全与成本工程 → Day 163 收官复盘），**每日更新从未中断**。上一周：Week 24「长时程 Agent 工程」已收官（Day 154 checkpoint 双指打字 ✓ → Day 155 心跳与健康检查×可观测性 ✓ → Day 156 容错分类与自动恢复 ✓ → Day 157 混沌测试与故障注入 ✓ → Day 158 收官混合模拟面试 ✓）。

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
- 🛟 [Day 161 — LFU 缓存 / 奖励服务与 rollout worker 容错（LC 460 · 三哈希表 + minFreq = O(1) get/put = reward server 热度分层 = APRIL buffer 淘汰策略）](week25/2026-11-12-lfu-cache-reward-service-fault-tolerance.md)（**三哈希表 + minFreq 指针**（kv: key→[value,freq]·freqMap: freq→双向链表·minFreq 唯二变更点：旧桶空且 f==minFreq +1 / 新 key 复位 1·面试三坑：elem 回写/minFreq 条件/put 已存在也 touch·Go 完整实现·进阶：LRU 对照[burst 刷榜 vs 爬坡]·2Q/ARC·TinyLFU·TTL 复合·分布式 sketch 可合并·加权 LFU=省钱游戏） / 热点：**Anthropic IPO 数倍超额认购（11/12 彭博：考虑上调超 $78·冲 1.5 万亿·暗盘 1.8 万亿·11/13 Amodei×Dimon 对谈=容量规划题）**·**智元 LIFE 框架（11/11：RoboBeats 心跳+Plastic Memory+EDM 复用=健康检查/checkpoint/归因三件套）**·**vLLM 11/11 EPD（encoder 拆 decode=故障域划分教科书）**·**Mercor 200 亿（11/11：评估即产业）** / 面试技巧：reward 账本三轴（QPS/P99 从 T_iter 倒推/成功率·最终一致补账）·worker 三档（失联/卡死=progress 零/半残=分布污染收敛性）·隔离三件套（cgroup/bulkhead/EPD）·APRIL 四要素·失败四类归因（**模型答错=训练信号别扔**）·设计题四步（三澄清→分层→**least in-flight LB**→降级打 degraded 标记·绿鞋=计划内弹性容量）·速答五连·金句「失败不是垃圾，是没排好序的资产」）
- ⚖️ [Day 160 — 数据流中位数回归 / 权重同步与训练-推理一致性（LC 295 · 双堆 = 权重版本流的一致性水位线）](week25/2026-11-11-stream-median-weight-sync-consistency.md)（**双堆三天后回归**：数据流=worker 心跳版本号·中位数=P50 一致性水位线·addNum O(log n)/findMedian O(1)=调度器每步免费体检·进阶：延迟删除=LC 480·t-digest P90·加权中位数·分布式 sketch 可合并性·向量时钟偏序 / 热点：**Anthropic 更新招股书路演正式启动（11/10-11：定价 $70-78·估值 1-1.4 万亿·904 亿超 SpaceX 纪录·足额认购·11/19 挂牌·昨天撤回的定价文件今天正式同步=现实版两阶段提交 commit）**·**Fable 5.2/Mythos 5.2 发布（11/9-10：路演当天发新权重版本·API 降价 20%）**·**vLLM 博客 11/6《Zero Mismatch, Simple Async RL》（REINFORCE Zero·DeepSeek-V4.1·Kimi K3.1·bitwise 对齐+unclipped IS）** / 面试技巧：**不一致四层来源**（L1 数值·L2 语义 top-p 定义·L3 tokenization·L4 版本·排查二分法：step-0 大=定义问题·小但爬升=漂移）·**对齐三流派**（同引擎 Slime/Miles bitwise identical·GLM-5.3 diff 1e-7 降 99.99% ‖ 解耦但 token 一致 MiniMax Forge·FP32 LM head fix[M1 教训：一个层停掉整个训练]·CISPO ‖ 显式分离 OpenRLHF·agentic 第一个撞墙）·军备赛三数字（vLLM 100 步 diff=0@91.3%·ROCm 200 步零 mismatch·11/6 数值一致性与策略滞后是两个独立问题）·**top-p 定义陷阱**（processed vs raw logprob·gap 跳 0.053·比较前先同意定义·减常量=逐 token 留 bias）·**权重广播五板斧**（量化 fp8·QAT inference-ready 打包·RDMA 400G<2s·分块流水·P2P swarming·AReaL #1560）·**vLLM Weight Transfer 四阶段**（init/start/update/finish）·**百万 worker 权重分发设计题**（三澄清→三层分发→Merkle 先校验后切换→版本水位线→灰度 1%·主线=可度量可告警最终一致）·金句「权重版本不一致是时间差·数值不一致是口径差·token 不一致是对象差」）
- 🚀 [Day 159 — 任务调度器 / rollout 基础设施与异步解耦（LC 621 · 堆 = 冷却期调度 = 训练任务排队 = rollout 队列的原子结构）](week25/2026-11-10-task-scheduler-rollout-infra.md)（**贪心模拟+最大堆**（每窗口 n+1 个不同任务·塞不满才待机·冷冻区本轮不放回·公式法 (maxCount-1)×(n+1)+并列数 与 len(tasks) 取 max·O(N log K)/O(K)）·工程映射=**带冷却约束的资源排程**（冷却期 n = backend 权重切换预热/sandbox 复用重置·待机 = GPU 空转·贪心「剩余次数最多优先」= 最容易成瓶颈的任务最先处理） / 热点：**Anthropic 定价文件发出又撤回（11/10 今天·路演启动日悬疑剧：8.667 亿股 @ $70-80 最高 693 亿美元说撤就撤·估值 2 万亿 → 1.1-1.5 万亿下修·260 亿美元私募配售锁定七家产业链巨头=互相持股铁证·工程读法：未确认状态就广播=两阶段提交少一阶段·rollout worker 重启必须拉最新 checkpoint）**·**TideRL readiness-aware 调度**（trainer 不傻等 straggler 按就绪度组 batch）·**ProRL Agent Rollout-as-a-Service**（min-heap 维护 LLM backend pool 动态注册+checkpoint swapping·HTTP 对接 veRL）·**Libra 弹性切换**（训练↔rollout 角色切换 <3.5% step time·异构 TP 路由）·**GLM-5.2 SAO**（放弃 π_old 跟踪·双边裁剪·单 rollout 消除组等待）·**veRL one-step off-policy 2.35-2.67x**·**APRIL 部分 rollout 44% 吞吐**·**RLinf 无状态 rollout worker 跑 spot 实例** / 面试技巧：异步化光谱三级（全同步 straggler 锁死 T_iter=max(T_rollout,T_train) → one-step off-policy 权重过期一步换零空转 → request-level async policy lag 需修正）·五角色表（GRPO 砍 Critic 动机）·分离 vs 共卡权衡（广播成本 vs 空转成本·Libra 第三答案）·速答五连（agentic vs RLHF 三差异/worker 挂了怎么办/off-policy 稳不稳/怎么省钱/为什么用堆）·金句「同步训练的 GPU 利用率由最慢的 episode 决定——都是尾部定价」）
- 🏁 [Day 158 — 合并 K 个升序链表 / Week 24 收官混合模拟面试（LC 23 · 多路归并 = 多源事件流合并 = 全局审计时间线）](week24/2026-11-09-k-way-merge-resilience-finale.md)（**小顶堆存每路当前头·弹最小补后继·O(N log k)/O(k)**·堆大小只跟 k 有关=外排序内存友好·分治两两合并缓存友好·败者树 O(1) 摊还[DBMS 标配]·进阶：LC 378/373/632·外排序·分布式归并=MapReduce 同构 / 热点：**Anthropic IPO 路演周今天启动（11/9 当周！）**（估值腰斩质疑 1.5 万亿→1-1.5 万亿=熔断止损·路演时间表 Plan B=checkpoint·尽调=GameDay·S-1 94 页=稳态假设文档·**万亿公司上市与 7×24 agent 生存跑同一套韧性协议**）·**Perplexity Teammates 上线两天 ×3.7（11/9）**（7×24 agent 产品化=韧性架构变 SLA 底座）·**论文 Monitor Accuracy Isn't the Answer（11/6：restart monitor 决策价值=checkpoint 质量才是关键）**·**Gartner 2028 年 50% GenAI 超预算**·百度 ERNIE Evolve RSI·千问 Team Agent / 面试技巧：全周一张图·四合一复盘·混合模拟面试四步答法·速答六连·金句「出了事，你得能回答第一秒到最后一秒每一秒发生了什么」）


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

当前进度：**Week 25 进行中 / Day 161**（Week 25 Day 3 🛟 容错日：奖励服务与 rollout worker 容错。Day 161 LFU 缓存 = LC 460 三哈希表 + minFreq = O(1) get/put = reward server 热度分层 = APRIL buffer 淘汰策略（kv/freqMap/minFreq·minFreq 唯二变更点·Go container/list 实现·进阶：LRU 对照/2Q/ARC/TinyLFU/TTL/分布式 sketch/加权 LFU）·面试技巧：reward 账本三轴（QPS/P50·P99 从 T_iter 倒推/成功率）·worker 故障域三档（失联=秒级重启拉 checkpoint/卡死=liveness 正常 progress 零=L3 探针/半残=行为基线偏离·分布污染收敛性）·故障域三隔离（cgroup/bulkhead/vLLM EPD）·APRIL buffer 四要素（存片段/续跑/LFU+新近性+加权/受控对比方差小）·失败四类归因（环境重试安全/reward 重打分/任务不可解剔除/**模型答错=负样本别扔**·无 reward 才是废数据）·设计题十万 QPS reward 服务（三澄清→分层→least in-flight LB→降级打 degraded 标记·绿鞋=计划内弹性容量）·热点：**Anthropic IPO 数倍超额认购（11/12：承销商考虑上调超 $78·冲 1.5 万亿·暗盘 1.8 万亿·11/13 Amodei×Dimon 对谈）**·智元 LIFE 框架（11/11：心跳+记忆+能力复用三件套）·vLLM 11/11 EPD（故障域划分教科书）·Mercor 200 亿（评估即产业）；Week 25 Day 2：Day 160 数据流中位数回归（LC 295 双堆=权重版本一致性水位线）已完成。**Day 162 训练安全与成本工程明日继续**）

**更新记录**：已连续更新 **161** 天

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
