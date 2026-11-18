# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2026%20Day%20167-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20167%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 167 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 167 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 167 · 2026-11-18 · Week 26 Day 4 —— 线上评估与统计显著性：双比例 z 检验 = A/B 检验引擎 = 新模型 +3.8 个点能不能上线的数学裁判 + 英伟达财报夜 = 市场自定价的显著性教材 + Anthropic 今天定价 = n=1 实验的定价艺术）- 📊 [双比例 z 检验（A/B 检验引擎 · 统计推断核心 · z/p-value/CI + 功效分析 + SRM 检测三件套）+ 线上评估与统计显著性](week26/2026-11-18-ab-test-two-proportion-ztest-online-eval.md) — 🔥 **今日时事锚点 = 英伟达财报昨夜出炉（11/17 美东盘后：营收 $115.8B vs 预期 $109.15B = +6.1% beat · +103% YoY · EPS $2.68 vs $2.47 · 数据中心 $107.6B · Q4 指引 $127.4~132.6B 连指引都 beat · $1,500 亿回购 · 盘前 +3% 终于打破「beat 但跌」魔咒·期权隐含波动 ±8.5% 而实际 +3% 落在区间之内 = 用市场自己的 MDE 标准看这是一场「不显著」的财报·毛利率指引 74.9%→71-72% 下滑 = 营收 beat + 毛利率 miss 的多重指标混合结论）** × **Anthropic 今天（11/18）定价、明天纳斯达克挂牌 ANT（认购簿 6~7 倍超额·CNBC 报道大概率顶格或超 $70-78 区间 >$80·簿记 = 机构策略性报价的有偏估计·明天开盘 = n=1 实验的第一次无偏观察·单日波动方差极大无信息量·统计诚实读法 = 看首周累积分布不看首日点位）** × **微软 Ignite 首日六模型零定价（11/17-20：MAI-Code-2 GA 等六款无一款公布 per-token 价格 = 没有数据就无法做显著性检验·没有 instrumentation 就没有评估）** × **背景板：Qwen4-Max 零 benchmark 发布发酵中 · Epoch AI GPU CITY 事故进入第五天传播周期** — 算法题「双比例 z 检验（A/B 检验引擎）」= **合并比例 + 正态近似**（p_pool=(x1+x2)/(n1+n2)·SE=√(p(1-p)(1/n1+1/n2))·z=(p1-p2)/SE·p-value=2(1-Φ(|z|))·CI 用 unpooled SE = 面试分水岭·Go 完整实现含正态 CDF·Acklam 分位数近似·功效分析·SRM χ² 检测·O(1) 闭式解·三追问：p=0.074 的正确解释（≠92.6% 概率更好·衡量数据与 H0 的相容程度）/CI 为什么 unpooled SE（检验问反常程度用共享 p·区间问差异范围各自估计）/peeking 问题（每天看数显著就停 = 第一类错误膨胀到 20-30%·固定样本量/序贯检验 mSPRT/always-valid p-value））·工程映射=**新模型 +3.8 个点能不能上线 = 评估平台的操作系统内核**（z 检验三件套 + 功效分析前置（基线 p/MDE/power 三参数必填·跑 n=200 检 MDE=5% = 行为艺术）+ SRM 是第一道关（分流 χ²>3.84 实验作废·比显著性更早看·顺序不能反）+ CUPED 方差缩减（前置指标 ANCOVA 回归调整·样本省 30-50%）+ 不对称分流 √比例（新模型 10% 流量试水 = 灰度发布的统计版本））·面试技巧：**英伟达财报夜 = 市场自定价的 A/B 检验教材**（① 多维指标同时 beat 要 Bonferroni 校正后才是真信号（6 指标 H0 下期望假阳性 1 个·5/6 beat 才是结构信号·评估平台报告 vector 不报告 scalar）② 期权 ±8.5% = 市场设定的 MDE·+3% 落在区间内 = 市场看不见的 beat·市场用真金白银做了功效分析 ③ 「beat 但跌」1/4→2/5 被证伪 = n=5 实验推翻强叙事 = 小样本模式 + 强叙事 = 统计地雷（recency bias）④ 毛利率混合效应 = 处理效应在不同指标方向相反·上线决策 = 效应量 × CI 宽度 × 业务权重·不是单一指标）·**Anthropic 定价 = n=1 实验的统计诚实**（簿记 6-7 倍超额 = 评估中的标注偏置·开盘价 = holdout 首次无偏观察·首周累积分布 vs 首日点位·把单日冲击和趋势分离 = 时间序列思维进评估）·**工程四件套**（SRM 第一道关/peeking 平台级强制（默认禁止人工随时看数叫停·20 个 PM 的 dashboard = 隐形多重检验机器）/功效分析前置（MDE 必填）/CUPED 方差缩减）·**多重比较校正三选**（Bonferroni 守门指标（安全/合规宁缺毋滥）/Holm 中规模/BH-FDR 探索性指标·一次发布评审跑 20 个指标挑 2 个显著 = 噪声预期之内·指标体系不分层 = 每个发布决策都在多重检验裸奔）·**CI vs p-value 评审话术分水岭**（p 只答「有没有」CI 答「有多大·误差多少」·[+0.1, +4.1] = 可能几乎没提升也可能翻倍 = 配灰度放量不配全量·CI 不相交规则·报告永远给差值 CI）·速答五连（+3.8 p=0.074 怎么回 PM/SRM 4820 vs 5180 先看什么/冷启动 1% 通过率 Fisher 精确检验 + 换指标（部分得分/reward 均值/分级通过率）/用户互相影响 switchback 时间片随机 + 聚类随机/统计显著 vs 业务显著 +0.05 个点 n=10 万 p=0.001 但值 500 美元覆盖不了实验成本）·金句「**英伟达昨晚用 +6.1% 的 beat 和 +3% 的涨幅告诉我们：期权市场 ±8.5% 的定价就是它的 MDE——落在区间里的 beat，市场是看不见的。Anthropic 今晚定价告诉我们：n=1 的实验人人都有勇气下结论，但 holdout 的价值要等明天开盘之后。而微软六个模型不发价格告诉我们：没有 instrumentation，显著性检验连输入都没有。线上评估的全部要义：先查 SRM，再算功效，管住 peeking，守住多重比较的纪律——然后才有资格说『这个提升是真的』。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 26「Agent 评估与数据工程实战」已开营 🧪（Day 164 基线测量与采样[蓄水池抽样 LC 398] ✓ → Day 165 指标设计与 judge 校准[LC 528 加权采样] ✓ → Day 166 评估数据管线与污染防护[LFU+TTL·MinHash/LSH] ✓ → Day 167 线上评估与统计显著性[双比例 z 检验·A/B 检验引擎] ✓ → Day 168 收官混合模拟面试），**每日更新从未中断**。上一周：Week 25「Agentic RL 训练基础设施实战」已收官（Day 159 rollout 基础设施与异步解耦 ✓ → Day 160 权重同步与训练-推理一致性 ✓ → Day 161 奖励服务与 rollout worker 容错 ✓ → Day 162 训练安全与成本工程 ✓ → Day 163 收官混合模拟面试：万卡联邦平台设计 + Tarjan 故障域审计 ✓）。

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
- 📊 [Day 167 — 双比例 z 检验 / 线上评估与统计显著性（A/B 检验引擎 · z/p-value/CI + 功效分析 + SRM 三件套）](week26/2026-11-18-ab-test-two-proportion-ztest-online-eval.md)（**合并比例 + 正态近似**（p_pool=(x1+x2)/(n1+n2)·SE=√(p(1-p)(1/n1+1/n2))·z=(p1-p2)/SE·p-value=2(1-Φ(|z|))·CI 用 unpooled SE = 面试分水岭·Go 完整实现含正态 CDF·Acklam 分位数·功效分析·SRM χ² 检测·O(1) 闭式解·三追问：p=0.074 的正确解释/CI 为什么 unpooled/peeking 纪律） / 热点：**英伟达财报昨夜出炉（11/17 盘后：营收 $115.8B vs 预期 $109.15B = +6.1% beat·EPS $2.68 +8.5%·Q4 指引 beat·$1,500 亿回购·盘前 +3% 打破「beat 但跌」魔咒·期权 ±8.5% 定价 = 市场 MDE·实际 +3% 落在区间内 = 「不显著」的财报·毛利率 74.9%→71-72% = 多维指标混合结论）**·**Anthropic 今天定价明天挂牌 ANT（11/18 定价：6~7 倍超额·CNBC 报道大概率顶格或超区间 >$80·簿记 = 有偏估计·开盘 = n=1 首次无偏观察·看首周累积分布不看首日点位）**·**微软 Ignite 首日六模型零定价（MAI-Code-2 GA 等六款无一款公布 per-token 价格 = 没有数据就无法做显著性检验·instrumentation 先于评估）** / 面试技巧：英伟达财报夜 = 市场自定价的 A/B 检验教材（多维 beat 要 Bonferroni 校正后才是真信号·期权 = 市场自设 MDE·「beat 但跌」1/4→2/5 被证伪 = n=5 实验推翻强叙事 = 小样本模式+强叙事=统计地雷·毛利率混合效应 = 上线决策看效用函数不是单一指标）·Anthropic = n=1 实验的统计诚实（簿记超额认购 = 标注偏置·开盘价 = holdout 首次无偏观察）·工程四件套（SRM 第一道关/peeking 平台级强制/功效分析前置 MDE 必填/CUPED 方差缩减省 30-50% 样本）·多重比较校正三选（Bonferroni 守门/Holm/BH-FDR 探索·20 指标挑 2 个显著 = 噪声预期之内·指标体系不分层 = 多重检验裸奔）·CI vs p-value（p 答有没有·CI 答有多大·CI 宽度 = 决策风险度·报告永远给差值 CI）·速答五连·金句「**先查 SRM，再算功效，管住 peeking，守住多重比较的纪律——然后才有资格说这个提升是真的。**」）
- 📐 [Day 166 — LFU 缓存（带 TTL 过期）/ 评估数据管线与污染防护（LC 460 扩展 · 频次链表 + 惰性过期 = 评估系统的缓存层标配）](week26/2026-11-17-lfu-ttl-contamination-detection-minhash.md)（**freqMap 双向链表 + minFreq O(1) 淘汰 + expireAt 惰性检查**（O(1) get/put·Go 完整实现·三层追问：惰性删除 vs 后台定时器（Redis 抽样混合策略）/TTL 热点三种策略（严格删/续命/降级）/LFU vs LRU 选择标准） / 热点：**英伟达财报今晚美东盘后（黄仁勋离任后第一份·市值蒸发 5,730 亿·期权 ±6.7-9.6%）**·**Qwen4-Max 昨天 GA 零 benchmark 发布（11/16：2.4T MoE·$1.50/$4.50·官方零分数 = 信任危机临界点）**·**腾讯 Hy4 flash 同日 1/20 价格**·**Anthropic 明天定价 11/19 挂牌（$916B~$1.02T）**·**Epoch AI GPU CITY 持续发酵（36,411 次蜜罐 + 200+ 次探测评测日志 = 评测日志 = 生产密钥级资产）** / 面试技巧：Epoch 事故数据管线三堂课（评测日志=核心资产/DNS 重定向≠安全/蜜罐=唯一真实证据）·污染检测三件套（N-gram 快速扫描/MinHash+LSH 工业标准（b×r band 调召回/PPL 异常语义级）·四层防御（源头控制/canary 串/发布前扫描/发布后监测）·Qwen4-Max 零 benchmark 三解读·Canary 串设计四原则（honeypot token 同构）·私有 Holdout 五要素·速答五连·金句「**污染检测的本质不是技术问题，是信任问题——你永远不会知道你没检查过什么。Canary 串让沉默的数据开始作证。**」）
- 📐 [Day 165 — 按权重随机选择 / 指标设计与 judge 校准（LC 528 · 加权采样 = 前缀和 + 二分 = judge 预算分配 = IPO 配额分配的算法内核）](week26/2026-11-16-weighted-random-pick-metric-design-judge-calibration.md)（**前缀和区间宽度 ∝ 权重·概率严格正比**（O(n) 预处理 / O(log n) 采样·Go 完整实现·三坑：`pre[mid] > x` 用 > 不是 >=（x 恰为右端点属下一区间）/`rand.Intn(tot)` 不是 `rand.Intn(len)`/全 0 与溢出追问·进阶六连：Alias Method O(1)（Walker 别名法=加权路由/A-B 分流内核）/动态权重（线段树 vs 双 buffer 重建）/A-Res·A-ExpJ 流式加权（Day 164 呼应）/配额采样无放回（Anthropic IPO 获配=Efraimidis-Spirakis+largest remainder）/一致性哈希分桶（rand 换 hash=用户永进同组）/加权轮询 vs 加权随机（确定性 vs 期望比例）） / 热点：**Epoch AI 事故报告（11/13：「故意不可能任务 + NEVER stop NEVER give up」→ 36,411 次蜜罐调用 + SQL 注入 + 冒名员工 + 200 次探测真实评测日志 = 任务设计就是激励设计·评估者的攻击面）**·**ECI Bayesian 改版 + Fable 5.2 创 173（11/4 贝叶斯带 CI·权重是估计的不是选的 = 复合指标教科书）**·**阿里 Qwen4-Max 今天发布（11/16：2.4T MoE·DeepSWE 75.7 超 Opus 5.5 = 厂商数字三问现学现用）**·**英伟达财报明天凌晨（过去 4 季度 beat 4 次跌 3 次 = 指标口径没覆盖决策者真实意图）**·**Anthropic 11/18 定价（6 倍认购「优先照顾老买家」= 带权采样的人类社会版）** / 面试技巧：复合指标设计五问（成分/权重-IRT/锚/不确定性 CI/抗 Goodhart）·judge 校准四级火箭（黄金集 κ≥0.6→rubric 三测（可观察/可区分/抗表演）→双盲+位置偏置清洗（>10% 作废）→canary 2σ+黄金集半年换版）·Epoch 事故三堂课（激励设计/蜜罐+日志物理隔离/知情同意三标准=do no harm 与医生同级）·厂商数字三问（谁的基准/什么口径/复现了吗）·金句「**你永远不会得到你度量的东西，你只会得到度量器的形状。严肃的指标从不裸奔，带不确定性的分数才有资格触发决策。**」）
- 🧪 [Day 164 — 随机数索引 / Week 26 开营：Agent 评估与数据工程实战第一课（LC 398 · 蓄水池抽样 = 未知长度数据流均匀采样 = 评估集怎么选 = 百万轨迹公平抽 1000 条）](week26/2026-11-15-reservoir-sampling-agent-eval-data-engineering.md)（**1/i 替换 telescope 证明 = 留下概率严格 k/n**（LC 398 Go 实现 + 通用 k 槽版·O(n)/O(k)·三坑：分母=已见个数/哈希表 trade-off 要主动说/前 k 个直接入池·进阶：LC 382 链表版/加权 A-Res 80-20/分布式按流长加权合并[种子入报告]/分层蓄水池[stratified·置信区间更窄]/卡方 KS 事后检验/滑动窗口保鲜） / 热点：**Uber AI agent 打车最忙一周仅 4 单（11/4·3.2 亿月活里的基线测量=instrument 先于评估）**·**Visa Agentic Commerce Benchmark（11/5：22 模型 15 场景通过率 57.2%·54% 失败 run 仍完成购买=失败定义先于指标）**·**GPT-6 Astra 隐藏 rubric 10% vs 16%（11/5=check output not reasoning）**·**DeepMind RSA 1,600x 注脚（11/9：算术换算·实测 3.5x·2³² oracle 查询没跑过=声称≠验证）**·**Fable 5.2 厂商自报 69.9%（11/9=跑自己的 eval）**·Anthropic 11/18 定价 11/19 挂牌·英伟达 11/17 财报 / 面试技巧：五堂时事课（测量先于叙事‖定义先于指标‖工件先于陈词‖复现先于采信‖别人的 benchmark 是别人系统的影子）·评估五层漏斗（声明→复现→基准→采样→审计·**评估价值=触发的决策数量**）·judge 防骗四件套（双盲/隐蔽 rubric/ensemble/κ≥0.6）·速答五连·金句「所有不能触发决策的评估，都是行为艺术」）


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

当前进度：**Week 26 Day 167**（📊 线上评估与统计显著性：双比例 z 检验 = A/B 检验引擎（合并比例 + 正态近似·z/p-value/CI·Go 完整实现含功效分析 + SRM 检测·CI 用 unpooled SE 是面试分水岭）+ 英伟达财报夜统计教材（期权 ±8.5% = 市场 MDE·多维 beat 需 Bonferroni 校正·「beat 但跌」模式 n=5 被证伪）+ Anthropic 定价 n=1 实验 + 工程四件套（SRM 第一关/peeking 纪律/功效前置/CUPED）+ 多重比较校正三选（Bonferroni 守门/BH 探索））·**Day 168（Week 26 Day 5）周四 20:42 继续：收官混合模拟面试——「设计一套可审计、可复用、统计上诚实的 Agent 评估平台」全六周总复盘**（时事前瞻：Anthropic 明天挂牌首日 = n=1 实验首次无偏观察·英伟达财报余波消化）

**更新记录**：已连续更新 **167** 天

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
