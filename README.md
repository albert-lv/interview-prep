# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2027%20Day%20169-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20169%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 169 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 169 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 169 · 2026-11-20 · Week 27 Day 1 🚀 开营 —— 本周主题「推理成本与容量工程实战」：Makespan 多机调度 = 推理路由的算法内核 = List Scheduling 无空闲论证 2-1/m 近似 + LPT 降序 4/3-1/(3m) 紧界 + Power of Two Choices 多查一台砍掉整个尾部分布（Θ(ln n/ln ln n)→Θ(ln ln n)）+ Go 实测突发窗口 Round-Robin = 下界 3.2 倍 + m 个 M/M/1 ≫ 1 个 M/M/m 池化大坑 + 路由策略全家桶选型表 + 🔥 ANT 挂牌首日（11/19 开盘 $111.50 = +34%·收 +15%·今天 = 首周收官日 = Day 167 n=1 观察兑现）× Anthropic 每美元算力创收 $1.72 较年初四倍（TIKR = 推理单位经济学首次进估值文件）× IEA 官宣 2030 数据中心电力翻倍 75% 给 AI（电力入模·电价/碳感知路由从论文走向生产）× Vera Rubin NVL72 落地 Azure + Dynamo 推理软件（Ignite 收尾官宣·机器买得到吞吐调出来）× 加州 AG 向英伟达发调查传票 + SpaceX $320 亿发债买 GPU + 苹果 AI agent 立项 + AMD -9%）- 🚀 [最小化最大完成时间（Makespan 多机调度 · LPT 贪心 + 在线 List Scheduling + Power of Two Choices）](week27/2026-11-20-load-balancing-makespan-lpt-power-of-two-inference-routing.md) — 🔥 **今日时事锚点 = ANT 首周收官日（11/19 挂牌：开盘 $111.50 较 $83 定价 +34%·收盘 +15%·fully diluted ~$1.5 万亿·今天周五收首周最后一个价 = 「首周累积分布」第一个完整切片 = Day 167 统计诚实读法兑现：点位无信息量·分布才作数）** × **Anthropic 每美元算力创收 $1.72，较年初提升逾四倍（TIKR：市场第一次给「推理单位经济学」定价·四倍提升 = 批处理×调度×路由×定价组合拳 = 本周三面框架教科书开篇）** × **IEA《2026 世界能源展望》：2030 数据中心新增 120GW·75% 给 AI·93 座 AI 工厂两年 <2GW→17GW ≈ 新增一个英国（电力 = 推理成本一阶项官方背书·电价/碳强度进路由排序键 = 「最闲且最便宜的机器」）** × **Vera Rubin NVL72 落地 Azure + Dynamo 推理软件官宣（Ignite 收尾日：硬件军备赛升级·但利用率是路由和调度决定的）** × **加州总检察长向英伟达发调查传票（黄仁勋与疑似中国情报人员关系/受限芯片流向中国/披露不足·SEC 民事调查在途）+ SpaceX 完成 $320 亿发债专款采购英伟达芯片（SPV 表外结构 = 推理需求催生金融工程）+ 苹果 11/18 立项自主 AI agent（彭博：最快明年底 = 需求端海啸）+ AMD 同日重挫 9%（推理芯片第二席位重排）** — 算法题「最小化最大完成时间（Makespan 多机调度）」= **List Scheduling 贪心 + LPT 降序加强 + Power of Two Choices**（NP-complete（Partition 归约）·贪心「每个请求丢给当前最闲机器」·无空闲论证：最后任务开始前所有机器都在忙 → C ≤ Σp/m + p_j(1-1/m) ≤ (2-1/m)C*·LPT 降序 → 4/3-1/(3m) 紧界·在线版 2-竞争比 = least-loaded 路由理论底盘·PoT 随机选 2 台进较闲的：max load Θ(ln n/ln ln n)→Θ(ln ln n/ln 2) = 只多查一台干掉整个尾部分布 = nginx least_conn among two 原型·Go 完整实现 + 四种路由策略实测：突发窗口（每机 ~25 请求）RR = 下界 3.2x / LL = 1.18x / PoT 拿九成效果·稳态大数定律抹平一半 = P99 的钱全在突发窗口·三坑：①手推 2-近似无空闲论证 ②在线不能排序 ≠ 离线 LPT ③PoT 优化 max load 非平均·两候选独立均匀随机·看 3 台以上收益骤降·进阶六连：LC 621 冷却期调度/异构加权 LPT（排序键 = 宣布优化什么）/带 release time 贪心 2-近似/Bin Packing 对偶 FFD 11/9/一致性哈希分层（哈希定会话 + 池间水位平衡）/排队论大坑 m 个 M/M/1 ≫ 1 个 M/M/m（中央排队 + 工作窃取））·工程映射 = **推理网关路由全家桶**（least-in-flight（状态便宜全量）/power-of-two（状态贵折中·90% 效果 1% 开销）/一致性哈希（session affinity）/prefix-cache-aware（KV 局部性）·分层解耦局部性与均衡·「路由决定已购算力的变现率」）·面试技巧：**Week 27 开营 · 推理经济学三面框架**（成本侧 = per-token/per-request/cost-per-correct 先对齐 CFO 口径 ‖ 容量侧 = Little's Law + 水位线 + M/M/m（Day 170 展开） ‖ 路由侧 = 今天·决定已购算力变现率 ‖ 共用铁律：排序键按什么排 = 宣布优化什么）·**ANT $1.72 分解**（批处理×调度×路由×定价 = 三面组合拳）·**IEA 电力入模三后果**（电价/碳强度进路由键·潮汐调度混部·容量规划盯瓦数不盯 GPU 数）·**利用率口径三问**（SM Active ≠ MFU ≠ goodput·prefill/decode·P50/P99 = 管过真集群的标志）·速答五连（Q1 Little's Law 1000QPS×500ms = 500 在途·峰值 2000×2s = 4000 = 先限流再扩容 ‖ Q2 LL vs RR：方差 60 倍世界选 LL·staleness 大时 PoT·中央单队列更优 ‖ Q3 利用率 92% 能接 20% 流量？口径三连反问 + ρ=0.92 等待 = ρ=0.8 的 4.6 倍 = 恰恰不能接 ‖ Q4 一致性哈希 vs LL 不是二选一是分层（局部性×均衡 Pareto） ‖ Q5 电力翻倍怎么降本：路由键加电价碳项 + 潮汐混部 + prefix caching = 「排序键这次宣布的是账单」）·金句「**Anthropic 挂牌首日 +34% 与 +15% 之间，切下的是首周分布的第一刀；真正被写进估值文件的是每美元算力创收 $1.72——机器是买来的，吞吐是调度出来的。makespan 是 NP-complete，但「丢给最闲机器」就有 2-竞争比，加一步降序就是 4/3，多看一台随机机器就砍掉整个尾部分布。路由决定的不是负载均衡，是已购算力的变现率。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 27「推理成本与容量工程实战」已开营 🚀（Day 169 负载均衡调度：Makespan·LPT·Power of Two Choices·路由全家桶选型 ✓ ·周五 2026-11-20 = ANT 首周收官日 + Anthropic 每美元算力创收 $1.72 较年初四倍 + IEA 官宣 2030 AI 电力翻倍 + Vera Rubin NVL72 上 Azure），**每日更新从未中断**。上一周：Week 26「Agent 评估与数据工程实战」已收官 🏁（Day 164 基线测量与采样[蓄水池抽样 LC 398] ✓ → Day 165 指标设计与 judge 校准[LC 528 加权采样] ✓ → Day 166 评估数据管线与污染防护[LFU+TTL·MinHash/LSH] ✓ → Day 167 线上评估与统计显著性[双比例 z 检验] ✓ → Day 168 收官混合模拟面试[Merkle Tree·可审计评估平台三件套] ✓）。

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
- 🚀 [Day 169 — 最小化最大完成时间（Makespan 多机调度）/ Week 27 开营「推理成本与容量工程实战」（LPT 贪心 + 在线 List Scheduling + Power of Two Choices · 推理请求路由的算法内核）](week27/2026-11-20-load-balancing-makespan-lpt-power-of-two-inference-routing.md)（**List Scheduling 无空闲论证 (2-1/m)·LPT 降序 (4/3-1/(3m) 紧界)·PoT 两台取闲 max load Θ(ln n/ln ln n)→Θ(ln ln n)·Go 实测突发窗口 RR = 下界 3.2x·LL 1.18x·PoT 九成效果**（NP-complete（Partition 归约）·O(n log n)+堆·三坑：手推 2-近似无空闲论证/在线不能排序≠离线 LPT/PoT 优化 max load 非平均且候选须独立均匀随机·进阶六连：LC 621 冷却期/异构加权 LPT（排序键 = 宣布优化什么）/release time 贪心/Bin Packing FFD 11/9 对偶/一致性哈希分层（哈希定会话+池间水位平衡）/m 个 M/M/1 ≫ 1 个 M/M/m 池化大坑） / 热点：**ANT 挂牌首日开盘 +34%（$111.50）收 +15%·今天 = 首周收官日 = Day 167 n=1 观察兑现**·**Anthropic 每美元算力创收 $1.72 较年初四倍（TIKR = 推理单位经济学首次进估值文件）**·**IEA 2030 AI 电力翻倍（75% 增容给 AI ≈ 新增一个英国·电力入模·电价/碳感知路由）**·**Vera Rubin NVL72 上 Azure + Dynamo（机器买得到吞吐调出来）**·**加州 AG 向英伟达发传票 + SpaceX $320 亿发债买 GPU + 苹果 agent 立项 + AMD -9%** / 面试技巧：Week 27 三面框架（成本侧 CFO 口径对齐‖容量侧 Little's Law+水位线‖路由侧 = 已购算力变现率·排序键 = 宣布优化什么）·ANT $1.72 分解（批处理×调度×路由×定价组合拳）·IEA 电力入模三后果（电价碳进路由键/潮汐混部/盯瓦数）·利用率三口径（SM Active/MFU/goodput·prefill/decode·P50/P99）·速答五连（Little's Law 500 在途/方差 60 倍选 LL·PoT 折中/ρ=0.92 等待 4.6 倍恰恰不能接/一致性哈希分层不是二选一/电力翻倍路由键加电价项）·金句「**机器是买来的，吞吐是调度出来的。**」）
- 🏁 [Day 168 — Merkle Tree / Week 26 收官混合模拟面试（可审计 Agent 评估平台 · Build O(N) / 证明与验证 O(log N) · 32 字节 root = 数据集身份指纹）](week26/2026-11-19-merkle-tree-audit-agent-eval-platform-finale.md)（**叶子 H(0x00‖data)/内部 H(0x01‖l‖r) 域名分隔防第二原像·奇数复制配对是协议·证明带方向位·100 万块证明仅 640 字节**（Go 完整实现含三审计场景·三坑：奇数填充/第二原像攻击/方向位·进阶六连：哈希链保序 vs Merkle 保集合/sorted Merkle 不存在证明/MMR 追加累加器/稀疏 Merkle/content-addressed 与 MinHash 互补/分布式 root 对账） / 热点：**Anthropic 昨夜定价 $83 超区间上限 6.4%（机构 $4000 亿+散户 $1000 亿 6 倍超额·募资 $813 亿+260 亿私募 = 史上最大 IPO·今晚 22:30 纳斯达克挂牌 = n=1 首次无偏观察·看首周累积分布不看首日点位）**·**英伟达财报消化第二天（Vera Rubin 量产·事件冲击→趋势定价 = 时间序列思维）**·**Gemini 4 Carbon 登顶 MathArena 但成本 15 倍（$15.70 vs $1.07·最常失败模式 = 超时且几乎不放弃 = cost-per-correct 才是决策指标·超时 ≠ 答错要分列）**·**UNESCO 报告：AI 迎合用户抛弃科学事实（judge 谄媚 = LLM-as-judge 头号系统偏置）** / 面试技巧：全周五合一复盘（测量先于叙事‖指标先于分数‖数据先于结论‖统计先于勇气‖审计先于信任·五环缺一环信任塌一环）·五环一张图·混合模拟面试四步答法（三澄清→五层架构→三性落地→治理兜底）·速答五连（跑个评估先问三问题/半年后质疑造假哈希链自证/judge 8 vs 7 四步验证/成本爆炸先查功效再砍/第一名贵 15 倍的报告怎么写）·金句「**人会离职，会遗忘，会改口，而 SHA-256 不会。**」）
- 📊 [Day 167 — 双比例 z 检验 / 线上评估与统计显著性（A/B 检验引擎 · z/p-value/CI + 功效分析 + SRM 三件套）](week26/2026-11-18-ab-test-two-proportion-ztest-online-eval.md)（**合并比例 + 正态近似**（p_pool=(x1+x2)/(n1+n2)·SE=√(p(1-p)(1/n1+1/n2))·z=(p1-p2)/SE·p-value=2(1-Φ(|z|))·CI 用 unpooled SE = 面试分水岭·Go 完整实现含正态 CDF·Acklam 分位数·功效分析·SRM χ² 检测·O(1) 闭式解·三追问：p=0.074 的正确解释/CI 为什么 unpooled/peeking 纪律） / 热点：**英伟达财报昨夜出炉（11/17 盘后：营收 $115.8B vs 预期 $109.15B = +6.1% beat·EPS $2.68 +8.5%·Q4 指引 beat·$1,500 亿回购·盘前 +3% 打破「beat 但跌」魔咒·期权 ±8.5% 定价 = 市场 MDE·实际 +3% 落在区间内 = 「不显著」的财报·毛利率 74.9%→71-72% = 多维指标混合结论）**·**Anthropic 今天定价明天挂牌 ANT（11/18 定价：6~7 倍超额·CNBC 报道大概率顶格或超区间 >$80·簿记 = 有偏估计·开盘 = n=1 首次无偏观察·看首周累积分布不看首日点位）**·**微软 Ignite 首日六模型零定价（MAI-Code-2 GA 等六款无一款公布 per-token 价格 = 没有数据就无法做显著性检验·instrumentation 先于评估）** / 面试技巧：英伟达财报夜 = 市场自定价的 A/B 检验教材（多维 beat 要 Bonferroni 校正后才是真信号·期权 = 市场自设 MDE·「beat 但跌」1/4→2/5 被证伪 = n=5 实验推翻强叙事 = 小样本模式+强叙事=统计地雷·毛利率混合效应 = 上线决策看效用函数不是单一指标）·Anthropic = n=1 实验的统计诚实（簿记超额认购 = 标注偏置·开盘价 = holdout 首次无偏观察）·工程四件套（SRM 第一道关/peeking 平台级强制/功效分析前置 MDE 必填/CUPED 方差缩减省 30-50% 样本）·多重比较校正三选（Bonferroni 守门/Holm/BH-FDR 探索·20 指标挑 2 个显著 = 噪声预期之内·指标体系不分层 = 多重检验裸奔）·CI vs p-value（p 答有没有·CI 答有多大·CI 宽度 = 决策风险度·报告永远给差值 CI）·速答五连·金句「**先查 SRM，再算功效，管住 peeking，守住多重比较的纪律——然后才有资格说这个提升是真的。**」）
- 📐 [Day 166 — LFU 缓存（带 TTL 过期）/ 评估数据管线与污染防护（LC 460 扩展 · 频次链表 + 惰性过期 = 评估系统的缓存层标配）](week26/2026-11-17-lfu-ttl-contamination-detection-minhash.md)（**freqMap 双向链表 + minFreq O(1) 淘汰 + expireAt 惰性检查**（O(1) get/put·Go 完整实现·三层追问：惰性删除 vs 后台定时器（Redis 抽样混合策略）/TTL 热点三种策略（严格删/续命/降级）/LFU vs LRU 选择标准） / 热点：**英伟达财报今晚美东盘后（黄仁勋离任后第一份·市值蒸发 5,730 亿·期权 ±6.7-9.6%）**·**Qwen4-Max 昨天 GA 零 benchmark 发布（11/16：2.4T MoE·$1.50/$4.50·官方零分数 = 信任危机临界点）**·**腾讯 Hy4 flash 同日 1/20 价格**·**Anthropic 明天定价 11/19 挂牌（$916B~$1.02T）**·**Epoch AI GPU CITY 持续发酵（36,411 次蜜罐 + 200+ 次探测评测日志 = 评测日志 = 生产密钥级资产）** / 面试技巧：Epoch 事故数据管线三堂课（评测日志=核心资产/DNS 重定向≠安全/蜜罐=唯一真实证据）·污染检测三件套（N-gram 快速扫描/MinHash+LSH 工业标准（b×r band 调召回/PPL 异常语义级）·四层防御（源头控制/canary 串/发布前扫描/发布后监测）·Qwen4-Max 零 benchmark 三解读·Canary 串设计四原则（honeypot token 同构）·私有 Holdout 五要素·速答五连·金句「**污染检测的本质不是技术问题，是信任问题——你永远不会知道你没检查过什么。Canary 串让沉默的数据开始作证。**」）


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

当前进度：**Week 27 Day 169 🚀 开营**（⚖️ Makespan 多机调度 = 推理路由算法内核：NP-complete（Partition 归约）·List Scheduling 无空闲论证 (2-1/m)·LPT 降序 4/3-1/(3m) 紧界·在线 2-竞争比 = least-loaded 理论底盘·Power of Two Choices 两台取闲 max load Θ(ln ln n) = 只多查一台砍掉尾部分布·Go 实测突发窗口 RR = 下界 3.2x·m 个 M/M/1 ≫ 1 个 M/M/m 池化大坑 + 推理经济学三面框架（成本侧 CFO 口径/容量侧 Little's Law·水位线/路由侧 = 已购算力变现率）+ 利用率三口径（SM/MFU/goodput）+ ANT 首周收官（首日 +34%→+15%·$1.72/算力美元较年初四倍 = 推理单位经济学定价时刻·Day 167 n=1 观察兑现）+ IEA 2030 AI 电力翻倍（电力入模·电价/碳感知路由）+ Vera Rubin NVL72 上 Azure + Dynamo·加州 AG 传票·SpaceX $320 亿发债买 GPU·苹果 agent 立项·AMD -9%）·**Day 170（Week 27 Day 2）周六 20:42 待开始**（主题：排队论与容量水位线——Little's Law 深入/M/M/m 与利用率陷阱/P50-P99 容量线怎么画·候选题 LC 621 Task Scheduler 或设计题「推理集群容量水位线」·时事观察：ANT 首周收盘后第一个完整分布切片·加州 AG 传票 vs SEC 调查连续剧·AMD -9% 后推理芯片格局）

**更新记录**：已连续更新 **169** 天

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
