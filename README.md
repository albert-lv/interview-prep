# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2027%20Day%20172-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20172%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 172 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 172 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 172 · 2026-11-23 · Week 27 Day 4 💰 容量规划与成本归因——推理 Token 成本模型与单位经济学 —— 本周主题「推理成本与容量工程实战」：LC 188 买卖股票 IV + 含手续费版 LC 714（成本归因思维的 DP 形态：状态机 hold/cash·手续费挂卖出单边·k≥n/2 退化无限次贪心·O(n·k)/O(1)）+ follow-ups 六连（冷冻期 LC 309 = GPU 切换 setup 时间/固定+比例成本 = 小请求不该上旗舰模型/杠杆 ETF 复利损耗 = 手续费 DP 连续版）+ 🔥 ANT 衍生品今日（11/23 周一）集体上市——Cboe 挂牌期权 + 四家发行商四对 2x 杠杆 ETF（GraniteShares/Defiance/ProShares/Tradr）同日开打 = 市场开始给「推理单位经济学」实时定价（首日 $111.50 开 → $97.17 收 +17% → 次日盘中 $84 回踩发行价 = 解禁曲线 = GPU 到货曲线同一画法）× NVDA「营收 +103%、股价 -23%」周（财新 11/23：标普单周 -8.6%·费半 -17.1%·FactSet 反推标普前瞻 P/E ~16.5x = 增长叙事下课·单位经济学上台·Blackwell 真卖点 = 每 token 成本 10x·每瓦性能 10x）× BIS 11/16《管控对先进半导体的远程访问》= 成本第四本账「合规」上线（五国列高转运风险·全球数据中心出租受控算力要出口许可·三层账扩成四层账）× Vercel 每 token 成本降至 4 月 43.3% 但总支出反涨 = Jevons Paradox 官方数据版 × 阿里双十一夜间 22:00-8:00 四折 = Day 171 潮汐调度做成 SKU·DeepSeek 峰谷定价 × AI 周报「token 降价但单任务变贵」= 只看单价是行为艺术·要看 cost per correct task × Black Friday（11/27）倒计时 5 天 = 推理集群年终大考）- 💰 [买卖股票 IV + 含手续费版（LC 188 / LC 714）/ 推理 Token 成本模型与单位经济学（七行账单拆解 · cost per token/request/correct-task）](week27/2026-11-23-token-cost-unit-economics-dp-stock-fee.md) — 算法题「LC 188 + LC 714」= **状态机 DP：hold/cash 两态·手续费挂卖出单边·k≥n/2 退化无限次贪心·O(n·k)/O(1) 空间**（Go 完整实现·三坑：手续费挂单边防重复计/k 上限由 ⌊n/2⌋ 结构决定不是参数/不能局部最优拼全局·DP 的价值就是全局记账 = 成本归因同构·进阶六连：LC 309 冷冻期 = GPU 切换 setup/固定+比例成本 = 阈值以下不交易/联合版手续费改变策略结构/排序键降维/在线版状态机/杠杆 ETF 复利损耗 = 波动率税）·工程映射 = **每 token 成本七行账单**（硬件摊销/电力 PUE/利用率 MFU/KV 缓存/空转碎片/失败重算/折价项·三抓手按杠杆排序：利用率→缓存命中率→路由·三决策指标：cost per token/request/correct-task）·面试技巧：**ANT 衍生品今日上市 = 实时定价实验**（没有 cost model 的人看价格·有 cost model 的人看分布）·**NVDA「营收 +103%、股价 -23%」周 = 增长让位单位经济学**（每 token 毛利是估值锚·Blackwell 用单位经济学卖货）·**BIS 11/16 = 成本第四本账「合规」**（「在哪跑」从此是法务问题·四层账都要 CFO+法务签字）·**Vercel 43.3% + 总支出反涨 = Jevons Paradox**（单位成本降 ≠ 总成本降·forecast 要配需求弹性系数）·**阿里夜间四折 = 潮汐调度定价版**（成本工程最高境界 = 把成本结构做成定价策略）·**杠杆 ETF 复利损耗 = 手续费 DP 现实课件**（净值 = 毛收益 − 摩擦 × 路径）·速答四连（客户砍价 50% = 拆账不降本·绑定承诺换阶梯·降价谈判变容量承诺谈判/证明集群赚钱 = 成本表+收入表+敏感性三表/Jevons 之问 = 弹性 > 1 时单位成本降总账反涨/2x ETF 跑不到 2x = 每日再平衡摩擦 + 复利路径依赖）·金句「**市场已经改判，增长叙事下课，单位经济学上台。每 token 成本要拆成七行账单才能对账，每个决策都要扣完手续费再谈收益，每本账都要加上合规那一行。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 27「推理成本与容量工程实战」进行中 🚀（Day 169 负载均衡调度：Makespan·LPT·Power of Two Choices·路由全家桶选型 ✓ → Day 170 排队论与容量水位线：LC 621 冷却调度·Little's Law·M/M/1 利用率陷阱·Erlang C 容量线四步·dispatcher = 第 m+1 个节点 ✓ → Day 171 电力成本与潮汐调度：LC 630 变体·可抢占最小电费·EDF + 最便宜槽位贪心·三层成本模型·训练/推理混部·财务尽调视角答砍单 ✓ → Day 172 容量规划与成本归因：LC 188/714 手续费 DP·每 token 成本七行账单·cost per correct task·Jevons Paradox·合规第四本账 ✓ ·周一 2026-11-23 = ANT 期权+四对 2x 杠杆 ETF 今日上市 + NVDA「营收 +103% 股价 -23%」周[标普前瞻 P/E 16.5x] + BIS 远程算力管制[合规账] + Vercel 每 token 成本 43.3% 但总支出反涨 + 阿里夜间四折 SKU 化 + Black Friday 倒计时 5 天），**每日更新从未中断**。上一周：Week 26「Agent 评估与数据工程实战」已收官 🏁（Day 164 基线测量与采样[蓄水池抽样 LC 398] ✓ → Day 165 指标设计与 judge 校准[LC 528 加权采样] ✓ → Day 166 评估数据管线与污染防护[LFU+TTL·MinHash/LSH] ✓ → Day 167 线上评估与统计显著性[双比例 z 检验] ✓ → Day 168 收官混合模拟面试[Merkle Tree·可审计评估平台三件套] ✓）。

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
- 💰 [Day 172 — 买卖股票 IV + 含手续费版（LC 188 / LC 714）/ 推理 Token 成本模型与单位经济学（每 token 成本七行账单 · cost per token/request/correct-task 三决策指标）](week27/2026-11-23-token-cost-unit-economics-dp-stock-fee.md)（**状态机 hold/cash 两态 DP·手续费挂卖出单边·k≥n/2 退化无限次贪心·O(n·k)/O(1) 空间**（Go 完整实现·三坑：手续费挂单边防重复计/k 上限 ⌊n/2⌋ 由结构决定/DP = 全局记账 = 成本归因同构·进阶六连：LC 309 冷冻期 = GPU 切换 setup/固定+比例成本 = 阈值以下不交易/联合版手续费改变策略结构/排序键降维/在线状态机/杠杆 ETF 复利损耗 = 波动率税） / 工程映射：每 token 成本七行账单（硬件摊销/电力 PUE/利用率 MFU/KV 缓存/空转碎片/失败重算/折价项）+ 三抓手按杠杆排序（利用率→缓存命中率→路由）+ cost per token/request/correct-task / 热点：**ANT 衍生品今日（11/23 周一）集体上市——Cboe 期权 + 四家四对 2x 杠杆 ETF 同日开打 = 市场给推理单位经济学实时定价（首日 $111.50→收 $97.17 +17%→次日盘中 $84 回踩发行价·解禁曲线 = GPU 到货曲线）**·**NVDA「营收 +103% 股价 -23%」周（财新 11/23：标普单周 -8.6%·费半 -17.1%·标普前瞻 P/E 16.5x = 增长叙事下课·单位经济学上台·Blackwell 真卖点 = 每 token 成本 10x）**·**BIS 11/16《管控对先进半导体的远程访问》= 成本第四本账「合规」上线（全球数据中心出租受控算力要出口许可·三层账扩四层账）**·**Vercel 每 token 成本降至 4 月 43.3% 但总支出反涨 = Jevons Paradox 官方数据版**·**阿里双十一夜间四折 = 潮汐调度 SKU 化·DeepSeek 峰谷定价**·**AI 周报「token 降价但单任务变贵」= 只看单价是行为艺术·要看 cost per correct task**·**Black Friday（11/27）倒计时 5 天** / 面试技巧：ANT 衍生品 = 实时定价实验（没 cost model 的人看价格·有 cost model 的人看分布）+ NVDA 周 = 增长让位单位经济学（每 token 毛利 = 估值锚）+ BIS = 合规第四本账（「在哪跑」是法务问题）+ Vercel 43.3% = Jevons Paradox（单位成本降 ≠ 总成本降·forecast 配需求弹性系数）+ 阿里夜间四折 = 潮汐调度定价版（成本工程最高境界 = 成本结构做成定价策略）+ 杠杆 ETF 复利损耗 = 手续费 DP 现实课件（净值 = 毛收益 − 摩擦 × 路径）·速答四连（砍价 50% = 拆账不降本·降价谈判变容量承诺谈判/证明赚钱 = 成本表+收入表+敏感性三表/Jevons 之问 = 弹性 > 1/2x ETF 跑不到 2x = 再平衡摩擦 + 复利路径依赖）·金句「**市场已经改判，增长叙事下课，单位经济学上台。每 token 成本要拆成七行账单才能对账，每个决策都要扣完手续费再谈收益。**」）
- ⚡ [Day 171 — 训练集群的潮汐排班（LC 630 变体 · 带 Deadline 与峰谷电价的任务调度）/ 电力成本与潮汐调度（可抢占最小电费 · EDF + 最便宜槽位贪心）](week27/2026-11-22-power-aware-scheduling-deadline-greedy.md)（**问题一共用骨架：按 deadline 升序 + 交换论证·不可抢占 = 时长最大堆 cur>d 弹堆顶丢最长 O(n log n) · 可抢占 = EDF 顺序·紧任务先挑 [0,d) 内最便宜 t_i 个空闲槽 最小堆 O(n log n + T log T)·不可行返回 -1**（Go 完整实现·三坑：排序键是 deadline 不是时长/弹堆顶 cur 同步减/可抢占版前缀槽位超集 = 交换论证关键·进阶四连：段数受限 DP/多机 LPT 近似/online rolling horizon/碳强度 λ 合成成本曲线） / 热点：**NVDA 周五 -14% 单周 -$900B·SEC 会计调查 + 加州 AG 传票 = 需求真实性焦虑 = 财务尽调视角进容量规划（合约基线+弹性现货两层对冲）**·**ANT 次日盘中 -13.5% 回踩 $84 一线·Anthropic 签 ratepayer pledge 为数据中心电价买单 = 电力成本 = 社会责任账**·**NVIDIA Snapshot GPU checkpoint/restore <45s = 可抢占假设工程地基**·**电力大盘 11/21：4,841 数据中心·135 个 ≥1GW campus** / 面试技巧：三层成本模型把省电翻译成目标函数（电量账什么时候跑‖容量/需量账跑多快‖碳账在哪跑）+ 混部代价主动说（checkpoint 间隔×进度 = 抢占损失·噪声邻居 P99 抖动·显存碎片）+ 财务尽调视角答砍单（合约基线+弹性现货·可抢占分层 推理>客户训练>内部实验·每度电产出 token 数 = ROI）·速答三连·金句「**电量账决定什么时候跑，容量账决定跑多快，碳账决定在哪跑。**」）
- 📐 [Day 170 — 任务调度器（LC 621 · 带冷却期的调度）/ 排队论与容量水位线（Little's Law · M/M/1 利用率陷阱 · Erlang C 容量线）](week27/2026-11-21-task-scheduler-queueing-theory-capacity-waterline.md)（**公式法 max((f-1)(n+1)+cnt, N) + 最大堆模拟 O(N·log k) 支持异构冷却**（Go 完整实现·三坑：忘取 max len(tasks) = 第一道钓鱼题/公式依赖同构冷却假设·异构退化为事件模拟/末框架不满不补空转·进阶六连：异构冷却矩阵 = 车间调度 setup 时间/LC 358 同构公式 + 可行重排 f≤(N+1)/2/无限任务流 = M/G/1 with setup/优先级反转/概率冷却） / 热点：**ANT 次日盘中 -13.5% 回 $83 发行价（首周分布切片兑现：+34%→+15%→~0%·均值漂移·尾部撒谎·只有分布诚实）**·**GTA VI 首日 $3.79B = 突发容量教科书（λ 爆炸·排队/扩容/降级三件套）**·**NGINX 关键漏洞 + F5 BIG-IP 6 严重 RCE = dispatcher = 第 m+1 个节点单点现形**·**谷歌砍 TPU 8i 订单 20%（forecast 翻车）vs Blue Origin 2029 TPU 卫星（m 不够用就打上轨道）**·**NVDA 七连跌 + SEC 会计调查 + 加州 AG 传票 + vCenter CVSS 9.8** / 面试技巧：Little's Law 三边界（稳态/守恒[超时截断 = 幸存者偏差]/均值不直接给 P99）+ ρ/(1-ρ) 爆炸表（0.8→4×·0.92→11.5×）+ m 个 M/M/1 ≫ 1 个 M/M/m 池化 + 容量线四步（SLA 分位→Erlang C 理论上限→压测 75% 工程红线→绿黄红行动）+ 速答五连·金句「**均值会漂移，尾部会撒谎，只有分布诚实。**」）
- 🚀 [Day 169 — 最小化最大完成时间（Makespan 多机调度）/ Week 27 开营「推理成本与容量工程实战」（LPT 贪心 + 在线 List Scheduling + Power of Two Choices · 推理请求路由的算法内核）](week27/2026-11-20-load-balancing-makespan-lpt-power-of-two-inference-routing.md)（**List Scheduling 无空闲论证 (2-1/m)·LPT 降序 (4/3-1/(3m) 紧界)·PoT 两台取闲 max load Θ(ln n/ln ln n)→Θ(ln ln n)·Go 实测突发窗口 RR = 下界 3.2x·LL 1.18x·PoT 九成效果**（NP-complete（Partition 归约）·O(n log n)+堆·三坑：手推 2-近似无空闲论证/在线不能排序≠离线 LPT/PoT 优化 max load 非平均且候选须独立均匀随机·进阶六连：LC 621 冷却期/异构加权 LPT（排序键 = 宣布优化什么）/release time 贪心/Bin Packing FFD 11/9 对偶/一致性哈希分层（哈希定会话+池间水位平衡）/m 个 M/M/1 ≫ 1 个 M/M/m 池化大坑） / 热点：**ANT 挂牌首日开盘 +34%（$111.50）收 +15%·今天 = 首周收官日 = Day 167 n=1 观察兑现**·**Anthropic 每美元算力创收 $1.72 较年初四倍（TIKR = 推理单位经济学首次进估值文件）**·**IEA 2030 AI 电力翻倍（75% 增容给 AI ≈ 新增一个英国·电力入模·电价/碳感知路由）**·**Vera Rubin NVL72 上 Azure + Dynamo（机器买得到吞吐调出来）**·**加州 AG 向英伟达发传票 + SpaceX $320 亿发债买 GPU + 苹果 agent 立项 + AMD -9%** / 面试技巧：Week 27 三面框架（成本侧 CFO 口径对齐‖容量侧 Little's Law+水位线‖路由侧 = 已购算力变现率·排序键 = 宣布优化什么）·ANT $1.72 分解（批处理×调度×路由×定价组合拳）·IEA 电力入模三后果（电价碳进路由键/潮汐混部/盯瓦数）·利用率三口径（SM Active/MFU/goodput·prefill/decode·P50/P99）·速答五连（Little's Law 500 在途/方差 60 倍选 LL·PoT 折中/ρ=0.92 等待 4.6 倍恰恰不能接/一致性哈希分层不是二选一/电力翻倍路由键加电价项）·金句「**机器是买来的，吞吐是调度出来的。**」）


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

当前进度：**Week 27 Day 172 💰 容量规划与成本归因——推理 Token 成本模型与单位经济学**（✅ LC 188 买卖股票 IV + 含手续费版 LC 714：状态机 hold/cash 两态·手续费挂卖出单边·k≥n/2 退化无限次贪心·O(n·k)/O(1)·三坑：手续费挂单边/k 上限 ⌊n/2⌋ 由结构决定/DP = 全局记账 = 成本归因同构·进阶六连：LC 309 冷冻期 = GPU 切换 setup/固定+比例成本 = 阈值以下不交易/联合版/排序键降维/在线状态机/杠杆 ETF 复利损耗 = 波动率税 + 每 token 成本七行账单：硬件摊销/电力 PUE/利用率 MFU/KV 缓存/空转碎片/失败重算/折价项·三抓手：利用率→缓存命中率→路由·三决策指标：cost per token/request/correct-task + ANT 衍生品今日上市 = 实时定价实验 + NVDA「营收 +103% 股价 -23%」周 = 增长让位单位经济学[标普前瞻 P/E 16.5x] + BIS 11/16 = 合规第四本账 + Vercel 43.3% = Jevons Paradox + 阿里夜间四折 = 潮汐调度 SKU 化 + Black Friday 倒计时 5 天）·**Day 173（Week 27 Day 5）周二 2026-11-24 20:42 待开始**（主题：Week 27 收官——推理成本工程全周综合实战/混合模拟面试·候选题方向：全周三面框架综合设计题「给推理集群做一套成本-容量-路由一体化控制台」或贪心/DP 综合·时事前瞻：Black Friday 11/27 前最后备战日·ANT 期权+杠杆 ETF 上市次日波动复盘[今日上市首日]·NVDA 七连跌后第二个周一走势·Bessent「10 天答复」到期倒计时）

**更新记录**：已连续更新 **172** 天
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
