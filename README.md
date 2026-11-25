# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2028%20Day%20174-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20174%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 174 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 174 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 174 · 2026-11-25 · Week 28 Day 1 🚀 开营——秘密传播与多智能体通信安全 —— 本周主题「Multi-Agent 系统架构实战」开营日：LC 2092 得知秘密的所有专家（带时间约束的信息传播 = Dijkstra 变形：dist[i] = 最早得知秘密的时间·松弛条件 mt >= dist[u] && mt < dist[v]·O((n+m) log n)·时间单向性 = 核心约束——t=5 知道秘密无法影响 t=3 的会面·过期条目跳过·Union-Find 回滚进阶·最小割阻断传播 = egress 白名单理论基础·多秘密并行传播）+ 🔥 微软 Agent Framework 遭遇 PyPI 供应链攻击（11/17-19：恶意版本 agent-framework 1.26.1/1.26.2 窃取 Azure 凭证/GitHub Actions 密钥/CI 环境变量·Python 启动即自动执行·上线不到一小时·微软：「凡安装过的系统应视为已失陷」= 你用来构建 agent 的框架就是攻击者的入口）× Agent 协议栈补完（MCP[agent→工具] + A2A[agent→agent] + AG-UI[agent→用户] = 「智能体时代的 TCP/IP」——但协议 ≠ 安全：TCP/IP 到 TLS 隔了二十年，agent 协议的安全层现在在哪？）× Gartner 预警：2027 年底前 40%+ agentic AI 项目将被取消（ROI 不清 + 风控薄弱·解药：治理与编排并行建设）× Grok 多智能体辩论架构（4 agent 幻觉率 12%→4.2%——但早期 beta 出现能力幻觉 = 辩论能纠错也能集体陷入同样盲区）× Fireworks AI 泄露扩大（Notion AI 聊天日志·Cursor 发行密钥暴露→历史 agent traces 泄露 = agent 间通信日志本身就是秘密载体）× Black Friday 倒计时 2 天（agent 系统年度大考 + 年度最大攻击面同时到来）—— 开营速答五连（30 分钟讲多 agent 安全架构四澄清开场三层架构[身份层 NHI 独立凭证/通信层 MCP 白名单+A2A 签名/审计层五元组+哈希链+LC 2092 传播分析] / API key 泄露影响面评估五步[定点→画通信图→跑 Dijkstra→分级响应→阻断] / 多 agent 调试三层难度[状态爆炸/非确定性/上下文不一致+显式状态作用域规则] / Agent Framework 被投毒应急五步[隔离→排查→全量轮换→审计→加固] / Gartner 40% 预警三对策[ROI 前置/治理并行/快速证伪]）- 🚀 [得知秘密的所有专家（LC 2092 · 秘密传播）/ 多智能体通信安全开营实战](week28/2026-11-25-secret-propagation-lc2092-multi-agent-security.md) — 算法题「LC 2092」= **Dijkstra 变形：最早得知时间最短路径**（Go 完整实现实测四例：基础链式传播/时间陷阱[双方都不知情时无法传播]/最短路径取 min[两条路径取更早]/反向时间阻断[t=1 < dist=t=2 切断传播链]·三坑：普通 BFS 恒定边权假设失效/初始化 firstPerson 三行顺序不能反/过期条目 if t > dist[u] skip·进阶六连：最小连通支配集[NP-hard]/IC 信息级联模型[蒙特卡洛]/Union-Find 按时间分组+回滚/全网传播 max-dist = 配置管理 RTO/最小割阻断 = egress 白名单理论基础/多秘密并行传播[k 次独立 Dijkstra]）·工程映射 = **多 agent 通信图信息流动控制**（LC 2092 最小完备模型：Person=agent 实例·Meeting=A2A/MCP/AG-UI 通信事件·Secret=API Key/PII/权重/prompt·dist=凭证传播时延·时间单向性=凭证传播不可逆·结果集=攻击面[多少个 agent 持有这个秘密]·微软 Agent Framework 攻击 = LC 2092 反向 Dijkstra[攻击者已知秘密→爆炸半径]·Fireworks AI：Cursor 迁离 = 删除图中所有与 F 相连的边·上下文不一致 = LC 2092 对偶问题[秘密传播太快=信息泄露·状态同步太慢=决策错误]·共同解 = 显式状态作用域规则[owner/reader/version = agent 通信 ACL]）·面试技巧：**微软 Agent Framework 供应链攻击 = 框架本身就是攻击面**（依赖层安全三件套：版本锁定+SHA-256 hash/私有 PyPI 镜像/CI 沙箱最小权限·「上线不到一小时」= 松散依赖约束在 60 分钟内自动拉取恶意代码 = LC 2092 t=0·凭证传播 = 反向 Dijkstra 爆炸半径·「视为已失陷」= 全量轮换不是排查后轮换）·**Agent 协议栈补完 = 能通信 ≠ 该通信**（MCP/A2A/AG-UI 三层协议各管什么·协议栈 ≠ 安全栈：MCP 定义怎么调工具没有内建谁可以调·agent 版 TLS 时刻还没到来·每层信任边界：MCP 工具白名单/A2A agent 身份认证/AG-UI human-in-the-loop·McKinsey 最低安全基线：个体 agent NHI 身份+最小权限凭证非共享）·**Gartner 40% 预警 + Grok 能力幻觉**（治理与编排并行建设·多 agent ≠ 更可靠：事实性错误降但系统性自信错误不一定[同底层模型偏见相关]·异构模型组合成本翻 3-5 倍但偏见去相关·Black Friday = 最高流量+最大攻击面：先限流再收紧权限最后保审计）·开营速答五连（30 分钟安全架构四澄清[拓扑/协议/数据流/SLA]三层[身份 NHI/通信签名+加密+脱敏/审计五元组+哈希链+传播分析]·API key 泄露影响面五步·多 agent 调试三层[状态爆炸/非确定性/上下文不一致+确定性重放]·投毒应急五步[隔离/排查/全量轮换/审计/加固]·Gartner 三对策[ROI 前置/治理并行/快速证伪]）·金句「**微软用一次 PyPI 投毒告诉全世界：你用来防御的框架，就是攻击者的入口。Agent 协议栈补完了「能通信」的问题，但「该通信吗」的安全层还没有标准答案。Black Friday 倒计时 2 天，当你在优化 agent 的推理延迟时，别忘了另一个指标：如果此刻一个 agent 被攻陷，它的 secret 会沿通信图传播到多少个节点？这个数，就是你的攻击面。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 28「Multi-Agent 系统架构实战」**开营 🚀**（Day 174 秘密传播与多智能体通信安全：LC 2092 Dijkstra 变形·时间单向性信息传播·微软 Agent Framework PyPI 供应链攻击 = 框架即攻击面·MCP+A2A+AG-UI 协议栈补完 = 能通信 ≠ 该通信·Gartner 40% 预警 = 治理必须并行建设·凭证泄露影响面评估五步·显式状态作用域 = agent 通信 ACL ✓），**每日更新从未中断**。下一课：Day 175 周四 2026-11-26 感恩节，建议方向「Agent 间消息协议与安全通信」或「拜占庭容错与群体决策」。上一周：Week 27「推理成本与容量工程实战」已收官 🏁（Day 169 负载均衡调度：Makespan·LPT·Power of Two Choices ✓ → Day 170 排队论与容量水位线：LC 621·Little's Law·Erlang C ✓ → Day 171 电力成本与潮汐调度 ✓ → Day 172 容量规划与成本归因：LC 188/714·七行账单 ✓ → Day 173 收官：LC 1235 加权区间调度·成本-容量-路由一体化控制台[度量→决策→治理]·黄仁勋复职 = 11 天最贵故障恢复 ✓）。

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
- 🚀 [Day 174 — 得知秘密的所有专家（LC 2092 · 秘密传播：Dijkstra 变形）/ 多智能体通信安全开营实战](week28/2026-11-25-secret-propagation-lc2092-multi-agent-security.md)（**Dijkstra 变形：dist[i] = 最早得知秘密时间·松弛 mt >= dist[u] && mt < dist[v]·O((n+m) log n)·时间单向性 = 核心约束**（Go 实测四例：基础链式/时间陷阱/最短路径取 min/反向时间阻断·三坑：BFS 恒定边权失效/初始化三行顺序/过期条目 skip·进阶六连：最小连通支配集/IC 信息级联/Union-Find 回滚/max-dist = RTO/最小割 = egress 白名单/多秘密并行） / 工程映射：**多 agent 通信图信息流动控制**（Person=agent·Meeting=A2A/MCP/AG-UI 事件·Secret=API Key/PII/prompt·结果集=攻击面·微软 Agent Framework 攻击 = 反向 Dijkstra·上下文不一致 = 对偶问题·显式状态作用域 = agent 通信 ACL） / 热点：**微软 Agent Framework 遭 PyPI 供应链攻击（11/17-19：恶意版本窃取 Azure 凭证/GitHub secrets·上线不到一小时·「视为已失陷」）**·**Agent 协议栈补完（MCP+A2A+AG-UI = 智能体 TCP/IP——但协议 ≠ 安全）**·**Gartner：40%+ agentic AI 项目 2027 年前被取消（治理必须并行建设）**·**Grok 多智能体辩论 12%→4.2% 但出现能力幻觉**·**Fireworks AI 泄露扩大（Notion AI 日志·Cursor traces）**·**Black Friday 倒计时 2 天（最高流量+最大攻击面）** / 面试技巧：供应链攻击 = 框架即攻击面（依赖层三件套：锁定+hash/私有镜像/CI 沙箱）+ 协议栈 ≠ 安全栈（agent 版 TLS 还没到来）+ 凭证泄露影响面五步（定点→画图→Dijkstra→分级→阻断）+ Gartner 三对策（ROI 前置/治理并行/快速证伪）·金句「**如果此刻一个 agent 被攻陷，它的 secret 会沿通信图传播到多少个节点？这个数，就是你的攻击面。**」）
- 🏁 [Day 173 — 规划兼职工作（LC 1235 · 加权区间调度：贪心 vs DP 分水岭）/ 成本-容量-路由一体化控制台收官综合实战](week27/2026-11-24-weighted-interval-scheduling-unified-console-finale.md)（**排序 + DP + 二分：dp[i] = max(dp[i-1], profit[i] + dp[p(i)])·O(n log n)·加权版 end 最早贪心正式下课**（Go 实测：官方例 DP=120 vs 贪心=90·反例 DP=105 vs 贪心=10·三坑：无权贪心 = 计数·加权 DP = 决策/二分 last end <= start + 下标 +1/半开区间边界·进阶六连：多机 k 轨道 NP-hard/在线 eviction + shadow benchmark/异构资源分层/可变收益 = 峰谷定价版/k 项限制 = LC 188 区间版/方案回溯） / 工程映射：**一体化控制台 = 度量→决策→治理三层**（七行账单+(λ,P99) 水位线·PoT+Erlang C+离线 DP benchmark·绿黄红+熔断+合规白名单+Merkle 审计） / 热点：**黄仁勋复职（11/23）——NVDA 单日 +31.48%·市值 +$9,520 亿创历史纪录·11 天「全球最贵 incident」：CEO = 未容灾的单点·中断成本跳变不是线性·恢复 ≠ 结案（调查继续+首席合规官+12/3 听证）**·**ANT 期权 + 10 只杠杆 ETF 首日上市：VIX >40·IV 50-100% = 不确定性本身成了交易品·ANT 全场独跌 = 集群容量零和**·**Black Friday 倒计时 3 天 = 加权区间调度真实战场：降级 = 收益重加权→重算 DP·调度器 KPI 是毛利不是利用率** / 面试技巧：黄仁勋复职四件套（单点根因/RTO 定价/failover 预授权/合规长尾）+ ANT IV = 安全边际是给不确定性的期权费 + 黑五接单 = 全局记账 + 收官速答五连（30 分钟讲控制台/95% 利用率组合拳/NVDA 式 blameless postmortem/毛利率突降排查顺序：重试放大→水位线→潮汐失效→长上下文漂移）·金句「**单点的代价不是线性的，是跳变的。利用率 95% 不是高效，是裸奔。**」）
- 💰 [Day 172 — 买卖股票 IV + 含手续费版（LC 188 / LC 714）/ 推理 Token 成本模型与单位经济学（每 token 成本七行账单 · cost per token/request/correct-task 三决策指标）](week27/2026-11-23-token-cost-unit-economics-dp-stock-fee.md)（**状态机 hold/cash 两态 DP·手续费挂卖出单边·k≥n/2 退化无限次贪心·O(n·k)/O(1) 空间**（Go 完整实现·三坑：手续费挂单边防重复计/k 上限 ⌊n/2⌋ 由结构决定/DP = 全局记账 = 成本归因同构·进阶六连：LC 309 冷冻期 = GPU 切换 setup/固定+比例成本 = 阈值以下不交易/联合版手续费改变策略结构/排序键降维/在线状态机/杠杆 ETF 复利损耗 = 波动率税） / 工程映射：每 token 成本七行账单（硬件摊销/电力 PUE/利用率 MFU/KV 缓存/空转碎片/失败重算/折价项）+ 三抓手按杠杆排序（利用率→缓存命中率→路由）+ cost per token/request/correct-task / 热点：**ANT 衍生品今日（11/23 周一）集体上市——Cboe 期权 + 四家四对 2x 杠杆 ETF 同日开打 = 市场给推理单位经济学实时定价（首日 $111.50→收 $97.17 +17%→次日盘中 $84 回踩发行价·解禁曲线 = GPU 到货曲线）**·**NVDA「营收 +103% 股价 -23%」周（财新 11/23：标普单周 -8.6%·费半 -17.1%·标普前瞻 P/E 16.5x = 增长叙事下课·单位经济学上台·Blackwell 真卖点 = 每 token 成本 10x）**·**BIS 11/16《管控对先进半导体的远程访问》= 成本第四本账「合规」上线（全球数据中心出租受控算力要出口许可·三层账扩四层账）**·**Vercel 每 token 成本降至 4 月 43.3% 但总支出反涨 = Jevons Paradox 官方数据版**·**阿里双十一夜间四折 = 潮汐调度 SKU 化·DeepSeek 峰谷定价**·**AI 周报「token 降价但单任务变贵」= 只看单价是行为艺术·要看 cost per correct task**·**Black Friday（11/27）倒计时 5 天** / 面试技巧：ANT 衍生品 = 实时定价实验（没 cost model 的人看价格·有 cost model 的人看分布）+ NVDA 周 = 增长让位单位经济学（每 token 毛利 = 估值锚）+ BIS = 合规第四本账（「在哪跑」是法务问题）+ Vercel 43.3% = Jevons Paradox（单位成本降 ≠ 总成本降·forecast 配需求弹性系数）+ 阿里夜间四折 = 潮汐调度定价版（成本工程最高境界 = 成本结构做成定价策略）+ 杠杆 ETF 复利损耗 = 手续费 DP 现实课件（净值 = 毛收益 − 摩擦 × 路径）·速答四连（砍价 50% = 拆账不降本·降价谈判变容量承诺谈判/证明赚钱 = 成本表+收入表+敏感性三表/Jevons 之问 = 弹性 > 1/2x ETF 跑不到 2x = 再平衡摩擦 + 复利路径依赖）·金句「**市场已经改判，增长叙事下课，单位经济学上台。每 token 成本要拆成七行账单才能对账，每个决策都要扣完手续费再谈收益。**」）
- ⚡ [Day 171 — 训练集群的潮汐排班（LC 630 变体 · 带 Deadline 与峰谷电价的任务调度）/ 电力成本与潮汐调度（可抢占最小电费 · EDF + 最便宜槽位贪心）](week27/2026-11-22-power-aware-scheduling-deadline-greedy.md)（**问题一共用骨架：按 deadline 升序 + 交换论证·不可抢占 = 时长最大堆 cur>d 弹堆顶丢最长 O(n log n) · 可抢占 = EDF 顺序·紧任务先挑 [0,d) 内最便宜 t_i 个空闲槽 最小堆 O(n log n + T log T)·不可行返回 -1**（Go 完整实现·三坑：排序键是 deadline 不是时长/弹堆顶 cur 同步减/可抢占版前缀槽位超集 = 交换论证关键·进阶四连：段数受限 DP/多机 LPT 近似/online rolling horizon/碳强度 λ 合成成本曲线） / 热点：**NVDA 周五 -14% 单周 -$900B·SEC 会计调查 + 加州 AG 传票 = 需求真实性焦虑 = 财务尽调视角进容量规划（合约基线+弹性现货两层对冲）**·**ANT 次日盘中 -13.5% 回踩 $84 一线·Anthropic 签 ratepayer pledge 为数据中心电价买单 = 电力成本 = 社会责任账**·**NVIDIA Snapshot GPU checkpoint/restore <45s = 可抢占假设工程地基**·**电力大盘 11/21：4,841 数据中心·135 个 ≥1GW campus** / 面试技巧：三层成本模型把省电翻译成目标函数（电量账什么时候跑‖容量/需量账跑多快‖碳账在哪跑）+ 混部代价主动说（checkpoint 间隔×进度 = 抢占损失·噪声邻居 P99 抖动·显存碎片）+ 财务尽调视角答砍单（合约基线+弹性现货·可抢占分层 推理>客户训练>内部实验·每度电产出 token 数 = ROI）·速答三连·金句「**电量账决定什么时候跑，容量账决定跑多快，碳账决定在哪跑。**」）


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

当前进度：**Week 28 Day 174 🚀 开营——秘密传播与多智能体通信安全**（✅ LC 2092 得知秘密的所有专家：Dijkstra 变形·dist[i] = 最早得知秘密时间·松弛 mt >= dist[u] && mt < dist[v]·O((n+m) log n)·时间单向性 = 核心约束·Go 实测四例[基础链式/时间陷阱/最短路径取 min/反向时间阻断]·三坑：BFS 恒定边权假设失效/初始化 firstPerson 三行顺序不能反/过期条目 if t > dist[u] skip·进阶六连：最小连通支配集 NP-hard/IC 信息级联蒙特卡洛/Union-Find 按时间分组+回滚/全网传播 max-dist = 配置管理 RTO/最小割阻断 = egress 白名单理论基础/多秘密并行 k 次独立 Dijkstra + 多 agent 通信图信息流动控制：LC 2092 最小完备模型[Person=agent·Meeting=A2A/MCP/AG-UI 事件·Secret=API Key/PII/prompt·结果集=攻击面]·微软 Agent Framework 攻击 = 反向 Dijkstra 爆炸半径·Fireworks AI：Cursor 迁离 = 删除图中所有与 F 相连的边·上下文不一致 = LC 2092 对偶问题[传播太快=泄露·同步太慢=决策错误]·显式状态作用域规则 = agent 通信 ACL + 微软 Agent Framework 供应链攻击 11/17-19：恶意版本 agent-framework 1.26.1/1.26.2 窃取 Azure 凭证/GitHub Actions 密钥·Python 启动即执行·上线不到一小时·「凡安装过的系统应视为已失陷」= 框架即攻击面·依赖层三件套[版本锁定+SHA-256 hash/私有 PyPI 镜像/CI 沙箱最小权限] + Agent 协议栈补完：MCP[agent→工具]+A2A[agent→agent]+AG-UI[agent→用户] = 智能体时代 TCP/IP——但协议 ≠ 安全[MCP 定义怎么调工具没有内建谁可以调·agent 版 TLS 还没到来]·每层信任边界[MCP 工具白名单/A2A 身份认证/AG-UI human-in-the-loop]·McKinsey 最低安全基线 = 个体 NHI 身份+最小权限凭证非共享 + Gartner 40% 预警：2027 年底前 40%+ agentic AI 项目被取消·ROI 不清+风控薄弱·解药 = 治理与编排并行建设 + Grok 多智能体辩论 4 agent 幻觉率 12%→4.2%——但能力幻觉仍在 = 辩论能纠错也能集体陷入同样盲区[同底层模型偏见相关] + Fireworks AI 泄露扩大：Notion AI 聊天日志受影响·Cursor 发行密钥暴露→历史 agent traces 只读桶泄露 = agent 间通信日志本身就是秘密载体 + Black Friday（11/27）倒计时 2 天 = agent 系统年度大考+年度最大攻击面[先限流保可用→最小权限降权→审计完整保追溯] + 开营速答五连：30 分钟安全架构四澄清[拓扑/协议/数据流/SLA]三层[身份 NHI/通信签名+加密+脱敏/审计五元组+哈希链+传播分析]/API key 泄露影响面五步[定点→画通信图→跑 Dijkstra→分级响应→阻断]/多 agent 调试三层[状态爆炸/非确定性/上下文不一致+确定性重放]/投毒应急五步[隔离→排查→全量轮换→审计→加固]/Gartner 三对策[ROI 前置/治理并行/快速证伪] + Week 28 开营导语：Week 23 练协作·Week 28 练安全协作——所有信号指向 agent 安全已从理论风险变成生产事故·本周主线 = 多 agent 通信图的信任边界）·**Day 175（Week 28 Day 2）周四 2026-11-26 感恩节 20:42 待开始**（建议方向「Agent 间消息协议与安全通信」或「拜占庭容错与群体决策」·时事前瞻：感恩节休市 = 交易量枯竭但 agent 系统不休——黑五零点钟声敲响时零售 agent 准备好了吗·ANT 期权首周期权[12/18 合约]节前持仓变化·黄仁勋 12/3 参议院听证倒计时 7 天·黑五 = agent 安全年度大考[最高流量+最大攻击面同时到来]）

**更新记录**：已连续更新 **174** 天
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
