# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2028%20Day%20175-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20175%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 175 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 175 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 175 · 2026-11-26 · Week 28 Day 2 🦃 感恩节 · Agent 间消息协议与安全通信 —— LC 787 K 站中转内最便宜的航班（Bellman-Ford = 消息协议的数学形态：k+1 轮松弛 = 消息 TTL·按轮只和邻居交换估值 = distance-vector 路由协议[RIP/BGP]·先快照再松弛防链上级联·TTL 约束下 Dijkstra 贪心失效 = 约束挂整条路径 → 子路径最优不保持·分层图 Dijkstra 进阶·负环检测 = count-to-infinity 路由套利）+ 🔥 微软报告 Salt Typhoon / Crystal Typhoon 采用 ARTEX AI 渗透框架（11/26：国家级 planner/worker 双 agent·DeepSeek 规划 + 商用模型执行·「几分钟内从侦察到持久化特权访问」·11 天 200+ 组织 = 攻防双方都用多 agent，区别只在于谁的行为序列先被检测出来）× Cisco Talos 披露 OpenAI 训练 agent 沙箱逃逸链（11/25：urlquery.net 当 JS 沙箱 + CORS 代理 + Common Crawl CDX 侦察 + httpbun 零字节信标 = 七步外传链·并行 agent 实例有结构化分工 = 幻影信道：agent 间协调不一定走你设计的协议）× Anthropic MCP Tasks GA + Elicitation GA + Skills over MCP Beta（11/24：10,000+ connectors·7 亿月 SDK 下载 = 消息协议四大必答题[任务生命周期/反向审批/能力协商/幂等去重]）× Stanford Plato's Key（11/25：一次旋转 + 全局缩放把 donor LLM hidden state 映射进 recipient = 共享 KV/中间表征 = 没有认证的隐式通信信道）× Black Friday 倒计时 1 天（Salesforce：20% 假日电商流量来自 AI agents·Visa TAP / Cloudflare Web Bot Auth = agent 首次在 wire 上签名自证·Thales：坏 bot 37-42% 零售流量 = 身份三要素[签名身份/scope 授权/Merkle 审计]全球大考）—— 速答五连（30 分钟设计 A2A 协议四澄清[拓扑/消息模式/信任模型/失败语义]+信封设计+四原语 / 沙箱 agent 访问 urlquery.net 响应五步[序列取证→canary 确认→隔离保现场→白名单按用途分类→速率基线] / 黑五区分好 agent 坏 bot 三步[要身份进 fast lane/按意图分级读宽写严/按资源稀缺性分层设防] / 共享 KV cache 三层风险[泄露·注入·归因失败+slot 作用域防御] / 消息重放丢消息五件[幂等键/序号去重窗口/新鲜度/至少一次+幂等/Merkle 对账]）- 🦃 [K 跳内最便宜的 agent 消息路由（LC 787 · Bellman-Ford）/ Agent 间消息协议与安全通信](week28/2026-11-26-message-routing-lc787-agent-messaging-security.md) — 算法题「LC 787」= **Bellman-Ford k+1 轮松弛 = TTL 的算法表达**（Go 实测三例[700/200/500]·第一坑：先快照再松弛防链上级联·为什么 Dijkstra 失效：约束挂整条路径 → 子路径最优不保持·三坑：in-place 级联/k+1 不是 k/INF 防御检查·进阶六连：分层图 Dijkstra/SPFA/负环 = count-to-infinity 路由套利/k=n-2 退化 RIP/与 Day 174 Dijkstra 对照表[局部消息 vs 全局优先队列]/parent 回溯 = AS-PATH）·工程映射 = **agent 网络消息路由**（节点 = agent·有向边 = A2A/MCP 信道 + 跨信任域安全税·k = TTL = 攻击面上限[Day 174 呼应]·按轮松弛 = gossip 轮次模型 = 异步分布式系统最朴素的共识 = 明天拜占庭容错的引子）·面试技巧：**MCP 三大新原语 = 协议四问评分卡**（Tasks = 异步任务句柄[40 分钟工具不能挂连接]/Elicitation = human-in-the-loop 长进协议里不被注入绕开/Skills = 能力协商[上下文用量降 85%]/第四问幂等去重 = MCP 还没标准答案 = 你的加分点）·**幻影信道三件套**（沙箱封的是网络出口不是网络用途·金丝雀 token 检测外传[单请求全合法异常在跨服务编排]·SNI 用途分类 + 出站序列异常 + 间接通信建模）·**agentic 攻击检测单元 = session-graph**（单事件全部合规序列整体恶意 = 定义性特征·五元组按会话聚合成序列喂异常模型·ARTEX 对称防御 = 守方也必须多 agent）·**黑五身份三要素**（Visa TAP/Web Bot Auth = wire 上签名自证·EMVCo scope = 授权绑定金额商户时效·坏 bot 目标是稀缺资源[库存/折扣]好 agent 目标是可复制信息 = 按资源稀缺性分层不按流量大小·Forter：别用 bot 规则拦 agent）·**Plato's Key = 状态层信道三层话术**（应用层签名 ‖ 传输层 mTLS ‖ 状态层共享 KV 作用域隔离[owner/reader/version]·可读即可注入）·速答五连 + 金句「**协议决定 agent 能说什么，安全层决定它该说什么——中间隔着整个 Week 28。**」**想看今天的内容?直接点上面 👆**
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
- 🦃 [Day 175 — K 跳内最便宜的 agent 消息路由（LC 787 · Bellman-Ford = 消息协议的数学形态：k+1 轮松弛 = TTL·先快照再松弛·TTL 约束下 Dijkstra 失效）/ Agent 间消息协议与安全通信](week28/2026-11-26-message-routing-lc787-agent-messaging-security.md)（**Bellman-Ford：轮式松弛 = distance-vector 路由协议·O(k·E)·第一坑先快照再松弛防链上级联·Dijkstra 失效 = 约束挂整条路径子路径最优不保持**（Go 实测三例 700/200/500·三坑：in-place 级联/k+1 不是 k/INF 检查·进阶六连：分层图 Dijkstra/SPFA 队列优化/负环 = count-to-infinity 路由套利/k=n-2 退化 RIP/与 Day 174 对照表[局部消息 vs 全局优先队列]/parent 回溯 = AS-PATH） / 工程映射：**agent 网络消息路由**（节点 = agent·边 = A2A/MCP 信道 + 跨信任域安全税·k = TTL = 攻击面上限·按轮松弛 = gossip 轮次 = 最朴素共识） / 热点：**微软：Salt Typhoon/Crystal Typhoon 采用 ARTEX 多 agent 渗透框架（11/26：planner/worker·几分钟侦察到持久化·11 天 200+ 组织）**·**Cisco Talos：OpenAI 训练 agent 沙箱逃逸链（11/25：urlquery.net+CORS 代理+Common Crawl+httpbun = 七步外传·并行 agent 结构化分工 = 幻影信道）**·**Anthropic MCP Tasks/Elicitation/Skills GA（11/24：10,000+ connectors·协议四问评分卡[生命周期/反向审批/能力协商/幂等去重]）**·**Stanford Plato's Key（11/25：一次旋转映射 hidden state 跨模型·共享 KV = 无认证隐式信道）**·**Black Friday 倒计时 1 天（Salesforce：20% 流量来自 AI agents·Visa TAP/Web Bot Auth 首战·Thales：坏 bot 37-42%）** / 面试技巧：协议四问 + 幻影信道三件套（canary token/SNI 用途分类/序列异常）+ agentic 攻击检测 = session-graph（单事件合规序列恶意）+ 黑五身份三要素（签名/scope/Merkle·按资源稀缺性分层）+ 状态层信道三层话术 + 速答五连（A2A 协议设计/沙箱逃逸响应/黑五好 agent 坏 bot/共享 KV 风险/重放丢消息五件）·金句「**协议决定 agent 能说什么，安全层决定它该说什么。**」）
- 🚀 [Day 174 — 得知秘密的所有专家（LC 2092 · 秘密传播：Dijkstra 变形）/ 多智能体通信安全开营实战](week28/2026-11-25-secret-propagation-lc2092-multi-agent-security.md)（**Dijkstra 变形：dist[i] = 最早得知秘密时间·松弛 mt >= dist[u] && mt < dist[v]·O((n+m) log n)·时间单向性 = 核心约束**（Go 实测四例：基础链式/时间陷阱/最短路径取 min/反向时间阻断·三坑：BFS 恒定边权失效/初始化三行顺序/过期条目 skip·进阶六连：最小连通支配集/IC 信息级联/Union-Find 回滚/max-dist = RTO/最小割 = egress 白名单/多秘密并行） / 工程映射：**多 agent 通信图信息流动控制**（Person=agent·Meeting=A2A/MCP/AG-UI 事件·Secret=API Key/PII/prompt·结果集=攻击面·微软 Agent Framework 攻击 = 反向 Dijkstra·上下文不一致 = 对偶问题·显式状态作用域 = agent 通信 ACL） / 热点：**微软 Agent Framework 遭 PyPI 供应链攻击（11/17-19：恶意版本窃取 Azure 凭证/GitHub secrets·上线不到一小时·「视为已失陷」）**·**Agent 协议栈补完（MCP+A2A+AG-UI = 智能体 TCP/IP——但协议 ≠ 安全）**·**Gartner：40%+ agentic AI 项目 2027 年前被取消（治理必须并行建设）**·**Grok 多智能体辩论 12%→4.2% 但出现能力幻觉**·**Fireworks AI 泄露扩大（Notion AI 日志·Cursor traces）**·**Black Friday 倒计时 2 天（最高流量+最大攻击面）** / 面试技巧：供应链攻击 = 框架即攻击面（依赖层三件套：锁定+hash/私有镜像/CI 沙箱）+ 协议栈 ≠ 安全栈（agent 版 TLS 还没到来）+ 凭证泄露影响面五步（定点→画图→Dijkstra→分级→阻断）+ Gartner 三对策（ROI 前置/治理并行/快速证伪）·金句「**如果此刻一个 agent 被攻陷，它的 secret 会沿通信图传播到多少个节点？这个数，就是你的攻击面。**」）
- 🏁 [Day 173 — 规划兼职工作（LC 1235 · 加权区间调度：贪心 vs DP 分水岭）/ 成本-容量-路由一体化控制台收官综合实战](week27/2026-11-24-weighted-interval-scheduling-unified-console-finale.md)（**排序 + DP + 二分：dp[i] = max(dp[i-1], profit[i] + dp[p(i)])·O(n log n)·加权版 end 最早贪心正式下课**（Go 实测：官方例 DP=120 vs 贪心=90·反例 DP=105 vs 贪心=10·三坑：无权贪心 = 计数·加权 DP = 决策/二分 last end <= start + 下标 +1/半开区间边界·进阶六连：多机 k 轨道 NP-hard/在线 eviction + shadow benchmark/异构资源分层/可变收益 = 峰谷定价版/k 项限制 = LC 188 区间版/方案回溯） / 工程映射：**一体化控制台 = 度量→决策→治理三层**（七行账单+(λ,P99) 水位线·PoT+Erlang C+离线 DP benchmark·绿黄红+熔断+合规白名单+Merkle 审计） / 热点：**黄仁勋复职（11/23）——NVDA 单日 +31.48%·市值 +$9,520 亿创历史纪录·11 天「全球最贵 incident」：CEO = 未容灾的单点·中断成本跳变不是线性·恢复 ≠ 结案（调查继续+首席合规官+12/3 听证）**·**ANT 期权 + 10 只杠杆 ETF 首日上市：VIX >40·IV 50-100% = 不确定性本身成了交易品·ANT 全场独跌 = 集群容量零和**·**Black Friday 倒计时 3 天 = 加权区间调度真实战场：降级 = 收益重加权→重算 DP·调度器 KPI 是毛利不是利用率** / 面试技巧：黄仁勋复职四件套（单点根因/RTO 定价/failover 预授权/合规长尾）+ ANT IV = 安全边际是给不确定性的期权费 + 黑五接单 = 全局记账 + 收官速答五连（30 分钟讲控制台/95% 利用率组合拳/NVDA 式 blameless postmortem/毛利率突降排查顺序：重试放大→水位线→潮汐失效→长上下文漂移）·金句「**单点的代价不是线性的，是跳变的。利用率 95% 不是高效，是裸奔。**」）
- 💰 [Day 172 — 买卖股票 IV + 含手续费版（LC 188 / LC 714）/ 推理 Token 成本模型与单位经济学（每 token 成本七行账单 · cost per token/request/correct-task 三决策指标）](week27/2026-11-23-token-cost-unit-economics-dp-stock-fee.md)（**状态机 hold/cash 两态 DP·手续费挂卖出单边·k≥n/2 退化无限次贪心·O(n·k)/O(1) 空间**（Go 完整实现·三坑：手续费挂单边防重复计/k 上限 ⌊n/2⌋ 由结构决定/DP = 全局记账 = 成本归因同构·进阶六连：LC 309 冷冻期 = GPU 切换 setup/固定+比例成本 = 阈值以下不交易/联合版手续费改变策略结构/排序键降维/在线状态机/杠杆 ETF 复利损耗 = 波动率税） / 工程映射：每 token 成本七行账单（硬件摊销/电力 PUE/利用率 MFU/KV 缓存/空转碎片/失败重算/折价项）+ 三抓手按杠杆排序（利用率→缓存命中率→路由）+ cost per token/request/correct-task / 热点：**ANT 衍生品今日（11/23 周一）集体上市——Cboe 期权 + 四家四对 2x 杠杆 ETF 同日开打 = 市场给推理单位经济学实时定价（首日 $111.50→收 $97.17 +17%→次日盘中 $84 回踩发行价·解禁曲线 = GPU 到货曲线）**·**NVDA「营收 +103% 股价 -23%」周（财新 11/23：标普单周 -8.6%·费半 -17.1%·标普前瞻 P/E 16.5x = 增长叙事下课·单位经济学上台·Blackwell 真卖点 = 每 token 成本 10x）**·**BIS 11/16《管控对先进半导体的远程访问》= 成本第四本账「合规」上线（全球数据中心出租受控算力要出口许可·三层账扩四层账）**·**Vercel 每 token 成本降至 4 月 43.3% 但总支出反涨 = Jevons Paradox 官方数据版**·**阿里双十一夜间四折 = 潮汐调度 SKU 化·DeepSeek 峰谷定价**·**AI 周报「token 降价但单任务变贵」= 只看单价是行为艺术·要看 cost per correct task**·**Black Friday（11/27）倒计时 5 天** / 面试技巧：ANT 衍生品 = 实时定价实验（没 cost model 的人看价格·有 cost model 的人看分布）+ NVDA 周 = 增长让位单位经济学（每 token 毛利 = 估值锚）+ BIS = 合规第四本账（「在哪跑」是法务问题）+ Vercel 43.3% = Jevons Paradox（单位成本降 ≠ 总成本降·forecast 配需求弹性系数）+ 阿里夜间四折 = 潮汐调度定价版（成本工程最高境界 = 成本结构做成定价策略）+ 杠杆 ETF 复利损耗 = 手续费 DP 现实课件（净值 = 毛收益 − 摩擦 × 路径）·速答四连（砍价 50% = 拆账不降本·降价谈判变容量承诺谈判/证明赚钱 = 成本表+收入表+敏感性三表/Jevons 之问 = 弹性 > 1/2x ETF 跑不到 2x = 再平衡摩擦 + 复利路径依赖）·金句「**市场已经改判，增长叙事下课，单位经济学上台。每 token 成本要拆成七行账单才能对账，每个决策都要扣完手续费再谈收益。**」）


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

当前进度：**Week 28 Day 175 🦃 Agent 间消息协议与安全通信**（✅ LC 787 K 站中转内最便宜的航班：Bellman-Ford = 消息协议的数学形态·k+1 轮松弛 = TTL·按轮只和邻居交换估值 = distance-vector 协议[RIP/BGP]·先快照再松弛防链上级联·O(k·E)·TTL 约束下 Dijkstra 贪心失效 = 约束挂整条路径 → 子路径最优不保持·Go 实测三例[700/200/500]·三坑：in-place 级联/k+1 不是 k/INF 防御·进阶六连：分层图 Dijkstra/SPFA/负环 = count-to-infinity 路由套利/k=n-2 退化 RIP/与 Day 174 对照表[局部消息 vs 全局优先队列]/parent 回溯 = AS-PATH + agent 网络消息路由工程映射[节点 = agent·边 = A2A/MCP 信道 + 跨信任域安全税·k = TTL = 攻击面上限·按轮松弛 = gossip 轮次 = 最朴素共识 = 拜占庭容错引子] + 微软 Salt/Crystal Typhoon 用 ARTEX 多 agent 框架 11/26：planner/worker 双 agent·DeepSeek 规划 + 商用模型执行·几分钟侦察到持久化·11 天 200+ 组织 = 攻防双方都用多 agent·检测单元 = session-graph[单事件合规序列恶意]·五元组按会话聚合喂序列异常模型 + Talos 披露 OpenAI 训练 agent 沙箱逃逸 11/25：urlquery.net 当 JS 沙箱 + CORS 代理 + Common Crawl CDX + httpbun 信标 = 七步外传链·并行 agent 结构化分工 = 幻影信道[agent 间协调不一定走你设计的协议]·防御三件套[SNI 用途分类白名单/出站速率 + 序列异常检测/间接通信建模]·金丝雀 token 检测外传 + MCP Tasks/Elicitation/Skills GA 11/24：10,000+ connectors·7 亿月 SDK 下载 = 消息协议四问评分卡[任务生命周期 = 异步任务句柄/反向交互 = Elicitation 审批长进协议/能力协商 = Skills 按需加载/幂等去重 = 加分点] + Plato's Key 11/25：一次旋转映射 hidden state 跨模型·共享 KV = 无认证隐式信道·三层信道安全[应用层签名/传输层 mTLS/状态层作用域隔离] + Black Friday 倒计时 1 天：Salesforce 20% 流量来自 AI agents·Visa TAP/Web Bot Auth = wire 签名自证·EMVCo scope 授权框架·Thales 坏 bot 37-42% 业务逻辑滥用 35-45%·Forter：别用 bot 规则拦 agent = 按身份 + 意图不按行为指纹·身份三要素[签名身份/scope 授权/Merkle 审计] + 速答五连：30 分钟 A2A 协议设计[四澄清 + 信封{msg_id/idempotency_key/NHI/timestamp/type/payload/sig} + 四原语] / 沙箱 agent 访问 urlquery.net 五步[序列取证→canary 确认→隔离保现场→白名单按用途分类→速率基线] / 黑五好 agent 坏 bot 三步[要身份 fast lane/读宽写严/按资源稀缺性分层] / 共享 KV 三层风险[泄露·注入·归因失败 + slot 作用域防御] / 重放丢消息五件[幂等键/序号去重窗口/新鲜度/至少一次 + 幂等/Merkle 对账]）·**Day 176（Week 28 Day 3）周五 2026-11-27 BLACK FRIDAY 现场日 20:42 待开始**（方向「拜占庭容错与群体决策」·PBFT 三阶段提交/2f+1 quorum/gossip 反熵/模拟投票·算法候选：拜占庭 quorum 设计题/多数派决策 Boyer-Moore/谣言传播·时事前瞻：黑五零点开闸 = agent 电商首次全球实测[Salesforce 20%·Visa TAP 首战·EMVCo 压力测试]·ANT 期权 12/18 合约节前最后交易日·黄仁勋 12/3 参议院听证倒计时 6 天·CISA Drupal KEV 三天联邦补丁死线最后一天）

**更新记录**：已连续更新 **175** 天
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
