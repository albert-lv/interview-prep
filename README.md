# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2025%20Day%20162-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20162%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 162 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 162 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 162 · 2026-11-13 · Week 25 Day 4 🔐 成本日——训练安全与成本工程 + LC 857 雇佣 K 名工人 = 比率排序 + 最大堆 = 性价比选型 = spot 实例池怎么挑）- 🔐 [雇佣 K 名工人的最低成本（LC 857 · 比率排序 + quality 最大堆 = 预算约束下的最优组合 = spot 实例选型 / judge 池选型 / IPO 配额分配）+ 训练安全与成本工程——reward hacking 攻防 / 成本四本账 + 电力账 / spot 抢占艺术 / 预算熔断与成本护栏 / 设计题「训练成本实时监控与自动熔断系统」](week25/2026-11-13-hiring-workers-cost-engineering-training-safety.md) — 🔥 **今日时事锚点 = Anthropic IPO 认购达发行规模约 6 倍（彭博 11/13 今天：优先照顾现有支持者、部分买家获配低于预期、英伟达认购约 100 亿·11/18 定价 11/19 挂牌）——需求 6 倍于容量的标准答案：上调发行价（价格传导）+ 绿鞋机制（弹性扩容 ~15%）+ 优先配额（老买家优先、散户预留 15-20%）= 成本护栏三件套，史上最大 IPO 现场直播版** × **Amodei × Dimon 直播对谈今天开演（11/13 纽约中午）** × **Anthropic 34 亿美元采购数据中心发电设备（电力成一阶项→长期 capex 锁价=对冲；招股书原文"为满足短期需求签订的合同通常成本更高"= spot 溢价的能源版）** × **reward hacking 论文三连（Debate Training[2608.17776]：弱 judge 下对抗式辩论按住 hacking、追回 45% 性能差、critique 限 150 词防反向 hack ‖ ICML 2026 梯度正则化：reward 平坦区更准确 ‖ arXiv 2605.25189：hacking=偏离可信低维轨迹的方向漂移、梯度投影回去=审计 reward 的几何版）** × **背景板：OpenSSH Alpine 长密码绕过认证紧急补丁（11/11）、黑客用 ARTEX AI 入侵 WorkOS 窃取 Slack 对话（11/11）** — 算法题「雇佣 K 名工人」= **比率排序 + 最大堆**（关键观察：最优比率必等于某工人 wage/quality；升序排序后标尺 i 的合法池=[0,i]，cost=r_i×Σquality 最小=选 quality 最小的 k 个·O(n log n)·Go 完整实现·面试三坑：交叉相乘防浮点/先弹再判/堆满 k 才更新·进阶六连：预算 B 内最多雇几人[前缀和+二分=每日配额计算器]·x 名资深配额[改变的是排序池构成不是算法心脏=散户预留 15-20%]·在线增量版·**spot 版期望成本=r/(1-p)+重算成本·便宜 30% 中断率 8% 未必便宜**·配比约束退化背包 NP-hard 的诚实边界·多目标版分母定义=宣布优化什么）·工程映射=spot 实例选型（调整后单价排序+配额）+ judge 池选型（$/期望正确分排序·Fable 5.2 降价 20%=供应商在改排序输入参数）+ Anthropic 6 倍认购分配（排序+名额+配额预留）·面试技巧：**reward hacking 三面孔**（规格博弈[长度偏置/改测试/格式谄媚]=Goodhart RL 版 ‖ 退化策略[复读机/模式坍缩]=奖励坍缩 ‖ 检测三信号：train/eval gap[Gao 2023 过优化 scaling law]/输出分布熵漂移/canary+人工审计·gap 是仪表盘/分布是体温计/审计是活检）·**防线四层**（规则 reward 守硬约束→judge+对抗 rubric[rubric 是活的·hack 样本库是饲料]→ensemble+从不训练的 holdout 审计 RM→KL/clip/entropy/早停）·**成本四本账+电力账**（GPU 时利用率账/带宽账[随规模人人有份]/存储账[砍存储放大抢占损失·账间耦合]/人力账[debug 两周工资>集群月租·可观测性=省人钱]+电力账[34 亿买发电设备=长期合约锁价对冲·9009 亿承诺 4516 亿 2030 前到期=on-demand 保关键路径+spot 填弹性的财务同构]）·**spot 三要素**（无状态化[被杀死时丢掉的东西能否分钟级重建=唯一判断标准]·期望损失 λ×W 定 checkpoint 频率[最优在两条曲线交点·预测抢占窗口前主动 checkpoint=优雅让位]·多样化池防相关性[名义 100 节点实际独立故障单元可能只有 3 个]）·**设计题四步**（三澄清[预算粒度/成本构成/熔断姿态=保训练连续性·砍新提交不砍在跑的]→四层架构[计量层强制归因标签→聚合层滑动窗口+成本归因树→决策层双阈值 80/100+MAD 抓慢漏[阈值管暴涨·异常检测管渗漏]→执行层分级熔断[限新提交→降 judge 规格→降并发→暂停训练]+半开恢复]→**Anthropic 三板斧对照[涨价=价格传导·绿鞋=弹性容量·优先分配=配额制·三个旋钮不是一个 panic 按钮]**·三坑：报警链路不依赖被熔断链路[自举失败]/熔断进审计/误杀漏杀抉择显式化[Gartner 2028 年 50% GenAI 超预算=商业背书]）·模拟面试速答五连（train reward 涨 benchmark 跌→先冻结可疑 checkpoint 保住现场再修 reward·别在漏水的 reward 上继续训 ‖ spot 中断率 5%→15%→动态调频压 W+关键路径迁回 on-demand[真实成本重算]+池子重平衡·中断率是输入参数不是常量 ‖ 预算剩 60%→砍 judge 成本[缓存→分层→规则前置]/采样宽度/episode 上限·**绝不砍 checkpoint/审计 reward/监控告警** ‖ trainer 用 on-demand worker 用 spot→关键路径与弹性路径定价理论[钱花在关键路径·贵的保确定性·便宜的吃弹性] ‖ judge 占 40%→缓存榨干→便宜预筛+贵复核双条件子集→规则前置→debate 替代大 judge·judge 降本与抗 hack 是同一问题两面）·金句「**需求永远会超过容量，问题从来不是你拒绝谁，而是用什么姿势拒绝——涨价是传导，绿鞋是扩容，优先分配是配额；排序键按什么排，就是在宣布你优化什么。成本护栏守的是钱包，审计 reward 守的是灵魂，checkpoint 守的是命。**」- 🎤 面试技巧：今日金句「**Anthropic 的承销商今天面对 6 倍认购，给出了成本工程的标准答案：需求永远会超过容量，问题从来不是你拒绝谁，而是用什么姿势拒绝——涨价是传导，绿鞋是扩容，优先分配是配额。训练系统一模一样：预算永远不够，工程的全部要义是先保什么、后砍什么——保关键路径，砍弹性路径；保训练信号，砍重复打分；保 checkpoint，砍采样宽度。而 reward 度量一旦被当成目标，它就不再是度量：规格上每一个没写清的条目，都会被优化器零元购——排序键按什么排，就是在宣布你优化什么。成本护栏守的是钱包，审计 reward 守的是灵魂，checkpoint 守的是命。收官前的安全检查表，一张都不能少。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 25「Agentic RL 训练基础设施实战」**进行中（Day 162/Week 25 Day 4 🔐 成本日已完成）**（Day 159 rollout 基础设施与异步解耦 ✓ → Day 160 权重同步与训练-推理一致性 ✓ → Day 161 奖励服务与 rollout worker 容错 ✓ → Day 162 训练安全与成本工程 ✓ → Day 163 收官复盘），**每日更新从未中断**。上一周：Week 24「长时程 Agent 工程」已收官（Day 154 checkpoint 双指打字 ✓ → Day 155 心跳与健康检查×可观测性 ✓ → Day 156 容错分类与自动恢复 ✓ → Day 157 混沌测试与故障注入 ✓ → Day 158 收官混合模拟面试 ✓）。

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
- 🔐 [Day 162 — 雇佣 K 名工人 / 训练安全与成本工程（LC 857 · 比率排序 + 最大堆 = 性价比选型 = spot 实例池怎么挑）](week25/2026-11-13-hiring-workers-cost-engineering-training-safety.md)（**比率排序 + quality 最大堆**（最优比率必等于某 r_i·排序后标尺左边自动合法·cost=r_i×sumQ·O(n log n)·Go 完整实现·进阶：预算 B 二分人数·资深配额=改排序池构成·在线增量·**spot 版 r/(1-p) 风险调整·便宜 30% 中断率 8% 未必便宜**·配比约束退 NP-hard·多目标版分母=宣布优化什么） / 热点：**Anthropic IPO 6 倍认购（11/13 彭博今天：优先老买家·部分获配低于预期·英伟达认购 100 亿·11/18 定价 11/19 挂牌）=成本护栏三件套：涨价=传导·绿鞋=扩容·优先分配=配额**·**Amodei×Dimon 对谈今天开演**·**Anthropic 34 亿买发电设备=电力锁价对冲·招股书"短期需求合同通常更贵"=spot 溢价能源版**·**reward hacking 论文三连（Debate Training 弱 judge 抗 hack+45%·critique 限 150 词防反向 hack/ICML 梯度正则化平坦区更可信/方向漂移投影=审计 reward 几何版）** / 面试技巧：hacking 三面孔（规格博弈=Goodhart RL 版/退化策略=奖励坍缩/检测三信号：gap·分布熵·canary+审计=仪表盘·体温计·活检）·防线四层（规则守硬约束→rubric 是活的·hack 样本库是饲料→ensemble+holdout 审计 RM→KL/clip/entropy/早停）·成本四本账+电力账（GPU 时利用率/带宽人人有份/存储砍了放大抢占·账间耦合/人力=debug 两周工资>集群月租+电力=长期合约锁价）·spot 三要素（无状态化=被杀后能否分钟级重建的唯一判断·λ×W 定 checkpoint 频率·多样化池防相关性[名义 100 节点独立故障单元可能只有 3 个]）·设计题四步（三澄清→四层架构[计量强制归因标签→聚合成本归因树→双阈值+MAD 抓慢漏·阈值管暴涨异常检测管渗漏→分级熔断+半开]·Anthropic 三板斧=三个旋钮不是一个 panic 按钮·报警不依赖被熔断链路）·速答五连·金句「需求永远超过容量，问题是用什么姿势拒绝·排序键按什么排就是宣布优化什么·护栏守钱包·审计守灵魂·checkpoint 守命」）
- 🛟 [Day 161 — LFU 缓存 / 奖励服务与 rollout worker 容错（LC 460 · 三哈希表 + minFreq = O(1) get/put = reward server 热度分层 = APRIL buffer 淘汰策略）](week25/2026-11-12-lfu-cache-reward-service-fault-tolerance.md)（**三哈希表 + minFreq 指针**（kv: key→[value,freq]·freqMap: freq→双向链表·minFreq 唯二变更点：旧桶空且 f==minFreq +1 / 新 key 复位 1·面试三坑：elem 回写/minFreq 条件/put 已存在也 touch·Go 完整实现·进阶：LRU 对照[burst 刷榜 vs 爬坡]·2Q/ARC·TinyLFU·TTL 复合·分布式 sketch 可合并·加权 LFU=省钱游戏） / 热点：**Anthropic IPO 数倍超额认购（11/12 彭博：考虑上调超 $78·冲 1.5 万亿·暗盘 1.8 万亿·11/13 Amodei×Dimon 对谈=容量规划题）**·**智元 LIFE 框架（11/11：RoboBeats 心跳+Plastic Memory+EDM 复用=健康检查/checkpoint/归因三件套）**·**vLLM 11/11 EPD（encoder 拆 decode=故障域划分教科书）**·**Mercor 200 亿（11/11：评估即产业）** / 面试技巧：reward 账本三轴（QPS/P99 从 T_iter 倒推/成功率·最终一致补账）·worker 三档（失联/卡死=progress 零/半残=分布污染收敛性）·隔离三件套（cgroup/bulkhead/EPD）·APRIL 四要素·失败四类归因（**模型答错=训练信号别扔**）·设计题四步（三澄清→分层→**least in-flight LB**→降级打 degraded 标记·绿鞋=计划内弹性容量）·速答五连·金句「失败不是垃圾，是没排好序的资产」）
- ⚖️ [Day 160 — 数据流中位数回归 / 权重同步与训练-推理一致性（LC 295 · 双堆 = 权重版本流的一致性水位线）](week25/2026-11-11-stream-median-weight-sync-consistency.md)（**双堆三天后回归**：数据流=worker 心跳版本号·中位数=P50 一致性水位线·addNum O(log n)/findMedian O(1)=调度器每步免费体检·进阶：延迟删除=LC 480·t-digest P90·加权中位数·分布式 sketch 可合并性·向量时钟偏序 / 热点：**Anthropic 更新招股书路演正式启动（11/10-11：定价 $70-78·估值 1-1.4 万亿·904 亿超 SpaceX 纪录·足额认购·11/19 挂牌·昨天撤回的定价文件今天正式同步=现实版两阶段提交 commit）**·**Fable 5.2/Mythos 5.2 发布（11/9-10：路演当天发新权重版本·API 降价 20%）**·**vLLM 博客 11/6《Zero Mismatch, Simple Async RL》（REINFORCE Zero·DeepSeek-V4.1·Kimi K3.1·bitwise 对齐+unclipped IS）** / 面试技巧：**不一致四层来源**（L1 数值·L2 语义 top-p 定义·L3 tokenization·L4 版本·排查二分法：step-0 大=定义问题·小但爬升=漂移）·**对齐三流派**（同引擎 Slime/Miles bitwise identical·GLM-5.3 diff 1e-7 降 99.99% ‖ 解耦但 token 一致 MiniMax Forge·FP32 LM head fix[M1 教训：一个层停掉整个训练]·CISPO ‖ 显式分离 OpenRLHF·agentic 第一个撞墙）·军备赛三数字（vLLM 100 步 diff=0@91.3%·ROCm 200 步零 mismatch·11/6 数值一致性与策略滞后是两个独立问题）·**top-p 定义陷阱**（processed vs raw logprob·gap 跳 0.053·比较前先同意定义·减常量=逐 token 留 bias）·**权重广播五板斧**（量化 fp8·QAT inference-ready 打包·RDMA 400G<2s·分块流水·P2P swarming·AReaL #1560）·**vLLM Weight Transfer 四阶段**（init/start/update/finish）·**百万 worker 权重分发设计题**（三澄清→三层分发→Merkle 先校验后切换→版本水位线→灰度 1%·主线=可度量可告警最终一致）·金句「权重版本不一致是时间差·数值不一致是口径差·token 不一致是对象差」）
- 🚀 [Day 159 — 任务调度器 / rollout 基础设施与异步解耦（LC 621 · 堆 = 冷却期调度 = 训练任务排队 = rollout 队列的原子结构）](week25/2026-11-10-task-scheduler-rollout-infra.md)（**贪心模拟+最大堆**（每窗口 n+1 个不同任务·塞不满才待机·冷冻区本轮不放回·公式法 (maxCount-1)×(n+1)+并列数 与 len(tasks) 取 max·O(N log K)/O(K)）·工程映射=**带冷却约束的资源排程**（冷却期 n = backend 权重切换预热/sandbox 复用重置·待机 = GPU 空转·贪心「剩余次数最多优先」= 最容易成瓶颈的任务最先处理） / 热点：**Anthropic 定价文件发出又撤回（11/10 今天·路演启动日悬疑剧：8.667 亿股 @ $70-80 最高 693 亿美元说撤就撤·估值 2 万亿 → 1.1-1.5 万亿下修·260 亿美元私募配售锁定七家产业链巨头=互相持股铁证·工程读法：未确认状态就广播=两阶段提交少一阶段·rollout worker 重启必须拉最新 checkpoint）**·**TideRL readiness-aware 调度**（trainer 不傻等 straggler 按就绪度组 batch）·**ProRL Agent Rollout-as-a-Service**（min-heap 维护 LLM backend pool 动态注册+checkpoint swapping·HTTP 对接 veRL）·**Libra 弹性切换**（训练↔rollout 角色切换 <3.5% step time·异构 TP 路由）·**GLM-5.2 SAO**（放弃 π_old 跟踪·双边裁剪·单 rollout 消除组等待）·**veRL one-step off-policy 2.35-2.67x**·**APRIL 部分 rollout 44% 吞吐**·**RLinf 无状态 rollout worker 跑 spot 实例** / 面试技巧：异步化光谱三级（全同步 straggler 锁死 T_iter=max(T_rollout,T_train) → one-step off-policy 权重过期一步换零空转 → request-level async policy lag 需修正）·五角色表（GRPO 砍 Critic 动机）·分离 vs 共卡权衡（广播成本 vs 空转成本·Libra 第三答案）·速答五连（agentic vs RLHF 三差异/worker 挂了怎么办/off-policy 稳不稳/怎么省钱/为什么用堆）·金句「同步训练的 GPU 利用率由最慢的 episode 决定——都是尾部定价」）


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
