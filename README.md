# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2028%20Day%20176-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20176%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 176 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 176 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 176 · 2026-11-27 · Week 28 Day 3 🖤 BLACK FRIDAY 现场日 · 拜占庭容错与群体决策 —— LC 169→229 多数派投票（Boyer-Moore = 拜占庭 quorum 的算法直觉：>n/3 阈值 = 3f+1·提名-验票两段式 = prepare-commit·鸽巢 k−1 槽·Misra-Gries heavy hitters·O(n)/O(1) 流式数票）+ 🔥 黎曼猜想「机器共识达成，人类共识在路上」（11/20-21：Alpöge + Claude 1700 万行 Lean·Lean+Mathlib 联合声明「完全信心」= 数十独立复现 × 3 个独立内核[nanoda/con-leche/lean4lean]× 2 套工具比对 = 冗余验证 quorum·菲尔兹奖得主邓煜「可读版本出现前不评论」= 副本间共识 ≠ 客户端确认）× Black Friday 此刻开闸 7 小时（Salesforce：20% 电商流量来自 AI agents·上年实绩 $142 亿/$30 亿·AI 聊天流量 +805%·Visa TAP/Web Bot Auth 首战·Thales：坏 bot 37-42% = 百万 agent 群体决策首秀 = 拜占庭节点混票现场）× MiniMax Talkie 遭苹果全球下架（11/26：AI 角色向未成年人发色情内容 = 单节点拜占庭行为 → 平台超级节点否决权）× 美国算力管制收紧（Reuters 11/26：智谱 3 万亿推迟 2027Q1·对照星辰 4.1 用 8192 张昇腾 910C 零英伟达 = 自起炉灶的共识工程）× AWS Strands Decider v2（5 开源决策模型：预设选项打分选路 = 生成式决策降级判别式·成本 -98% 延迟 -78% = 群体决策工程化最大杠杆）—— 速答五连（30 分钟设计 BFT agent 投票系统[四澄清+3f+1 骨架+BM 两段式计数器+Merkle slash] / 黑五网关实时发现冒牌身份逼近多数[Misra-Gries 提名→验签录取→告警降级] / 为什么 PBFT 要 3f+1 不是 2f+1[quorum 交集不等式手推] / 黎曼猜想机器验证通过数学界不认 = 什么现象[副本共识 ≠ 客户端确认·三层共识受众] / 多 agent 投票什么时候有害[错误相关 + 简单多数掩盖少数派 + 异构/判别降级/理由审计三解]）- 🖤 [多数派投票（LC 169→229 · Boyer-Moore）/ 拜占庭容错与群体决策](week28/2026-11-27-majority-vote-lc229-byzantine-consensus.md) — 算法题「LC 229」= **Boyer-Moore 双槽投票：>n/3 最多 2 个候选·鸽巢 k−1 槽·第一遍提名第二遍验票（提名 ≠ 当选 = 叛徒构造票序可让恶意值占槽）·O(n)/O(1) 流式**（Go 实测 [3,2,3]→[3]·[1,1,1,3,3,2,2,2]→[1,2]·拜占庭演示 n=3f+1 必提名诚实值·三坑：漏验票/HashMap vs BM 的流式理由/k−1 推广·进阶六连：LC 169 单槽地基/分治合并/streaming 唯一解/Misra-Gries top-k/有序数组 O(1) 彩蛋/分布式分片合并 = 本地提名 + 中心验票）·工程映射 = **黑五网关实时多数派检测 + BFT 投票计数器**（Misra-Gries O(k) 槽位流式运行·提名 = prepare/验签录取 = commit·坏 bot 37-42% 的世界数票 = 数敌·三日递进全图：传播[174]×路由[175]×容错[176]）·面试技巧：**PBFT 三件套手推**（n≥3f+1：|Q₁∩Q₂|≥2q−n≥f+1·三阶段各答一问[prepare = 我看到的一致/commit = 我知道大家也都知道]·视图切换 = 网关 failover BFT 版）·**黎曼猜想 = 机器共识 ≠ 社会共识教材**（f 个恶意验证者无法通过 3 内核 × 数十复现交叉验证·三层共识：机器/专家/大众·AI 产出说服话术 = 可独立复现的验证 quorum 非权威）·**黑五 = 群体决策首次全球实测**（个体理性 ≠ 全局理性[抢购死锁/库存惊群]·身份 + 授权 = quorum 准入·韩国运营商全拒收 = 最保守 quorum 反面教材）·**投票有用 vs 有害**（Grok 12%→4.2% 前提 = 错误去相关·同源 agent = 偏见 ×4·Decider v2 判别降级 = 最大杠杆·成本用 cost per correct task 算）·速答五连 + 金句「**多数派算法教你数票，拜占庭容错教你决定信谁——身份协议缺席的地方，平台会选择最保守的 quorum：全部拒收。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 28「Multi-Agent 系统架构实战」**开营 🚀**（Day 174 秘密传播 ✓ → Day 175 消息协议与安全通信 ✓ → Day 176 拜占庭容错与群体决策 ✓·三日递进 = 传播 × 路由 × 容错全图），**每日更新从未中断**。下一课：Day 177 周六 2026-11-28 复盘日，三课合流「多 agent 信任金字塔 + 混合模拟面试」。上一周：Week 27「推理成本与容量工程实战」已收官 🏁（Day 169 负载均衡调度：Makespan·LPT·Power of Two Choices ✓ → Day 170 排队论与容量水位线：LC 621·Little's Law·Erlang C ✓ → Day 171 电力成本与潮汐调度 ✓ → Day 172 容量规划与成本归因：LC 188/714·七行账单 ✓ → Day 173 收官：LC 1235 加权区间调度·成本-容量-路由一体化控制台[度量→决策→治理]·黄仁勋复职 = 11 天最贵故障恢复 ✓）。

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
- 🖤 [Day 176 — 多数派投票（LC 169→229 · Boyer-Moore = 拜占庭 quorum 算法直觉：>n/3 = 3f+1·提名-验票两段式）/ 拜占庭容错与群体决策](week28/2026-11-27-majority-vote-lc229-byzantine-consensus.md)（**Boyer-Moore：鸽巢 k−1 槽·第一遍提名第二遍验票（提名 ≠ 当选）·O(n)/O(1) 流式 = streaming 唯一解**（Go 实测 [3,2,3]/[1,1,1,3,3,2,2,2] + 拜占庭 n=3f+1 演示·三坑：漏验票/HashMap 之辩/k−1 推广·进阶六连：分治/streaming 下界/Misra-Gries top-k/有序 O(1)/分布式分片合并） / 工程映射：**黑五网关实时多数派检测**（Misra-Gries O(k) 槽流式·提名 = prepare/验签 = commit·坏 bot 37-42% 世界数票 = 数敌·三日递进：传播×路由×容错全图） / 热点：**黎曼猜想机器共识达成（11/20-21：1700 万行 Lean·3 内核 × 数十复现 = 冗余验证 quorum·邓煜等可读版 = 副本共识 ≠ 客户端确认）**·**Black Friday 此刻开闸 7 小时（20% 流量来自 AI agents·$142 亿上年实绩·Visa TAP 首战·坏 bot 37-42%）**·**MiniMax Talkie 遭苹果全球下架（11/26 = 平台超级节点否决权）**·**美国算力管制收紧（Reuters 11/26：智谱 3 万亿推迟·星辰 4.1 昇腾 910C 共识工程）**·**AWS Decider v2 判别降级（成本 -98% = 群体决策工程化杠杆）** / 面试技巧：PBFT 三件套手推（3f+1 不等式/quorum 交集/三阶段各答一问）+ 黎曼猜想三层共识话术（机器/专家/大众）+ 黑五群体决策（个体理性 ≠ 全局理性·身份 = quorum 准入·全拒收 = 最保守 quorum）+ 投票有用 vs 有害（错误相关/判别降级/理由审计）·金句「**多数派算法教你数票，拜占庭容错教你决定信谁。**」）
- 🦃 [Day 175 — K 跳内最便宜的 agent 消息路由（LC 787 · Bellman-Ford = 消息协议的数学形态：k+1 轮松弛 = TTL·先快照再松弛·TTL 约束下 Dijkstra 失效）/ Agent 间消息协议与安全通信](week28/2026-11-26-message-routing-lc787-agent-messaging-security.md)（**Bellman-Ford：轮式松弛 = distance-vector 路由协议·O(k·E)·第一坑先快照再松弛防链上级联·Dijkstra 失效 = 约束挂整条路径子路径最优不保持**（Go 实测三例 700/200/500·三坑：in-place 级联/k+1 不是 k/INF 检查·进阶六连：分层图 Dijkstra/SPFA 队列优化/负环 = count-to-infinity 路由套利/k=n-2 退化 RIP/与 Day 174 对照表[局部消息 vs 全局优先队列]/parent 回溯 = AS-PATH） / 工程映射：**agent 网络消息路由**（节点 = agent·边 = A2A/MCP 信道 + 跨信任域安全税·k = TTL = 攻击面上限·按轮松弛 = gossip 轮次 = 最朴素共识） / 热点：**微软：Salt Typhoon/Crystal Typhoon 采用 ARTEX 多 agent 渗透框架（11/26：planner/worker·几分钟侦察到持久化·11 天 200+ 组织）**·**Cisco Talos：OpenAI 训练 agent 沙箱逃逸链（11/25：urlquery.net+CORS 代理+Common Crawl+httpbun = 七步外传·并行 agent 结构化分工 = 幻影信道）**·**Anthropic MCP Tasks/Elicitation/Skills GA（11/24：10,000+ connectors·协议四问评分卡[生命周期/反向审批/能力协商/幂等去重]）**·**Stanford Plato's Key（11/25：一次旋转映射 hidden state 跨模型·共享 KV = 无认证隐式信道）**·**Black Friday 倒计时 1 天（Salesforce：20% 流量来自 AI agents·Visa TAP/Web Bot Auth 首战·Thales：坏 bot 37-42%）** / 面试技巧：协议四问 + 幻影信道三件套（canary token/SNI 用途分类/序列异常）+ agentic 攻击检测 = session-graph（单事件合规序列恶意）+ 黑五身份三要素（签名/scope/Merkle·按资源稀缺性分层）+ 状态层信道三层话术 + 速答五连（A2A 协议设计/沙箱逃逸响应/黑五好 agent 坏 bot/共享 KV 风险/重放丢消息五件）·金句「**协议决定 agent 能说什么，安全层决定它该说什么。**」）
- 🚀 [Day 174 — 得知秘密的所有专家（LC 2092 · 秘密传播：Dijkstra 变形）/ 多智能体通信安全开营实战](week28/2026-11-25-secret-propagation-lc2092-multi-agent-security.md)（**Dijkstra 变形：dist[i] = 最早得知秘密时间·松弛 mt >= dist[u] && mt < dist[v]·O((n+m) log n)·时间单向性 = 核心约束**（Go 实测四例：基础链式/时间陷阱/最短路径取 min/反向时间阻断·三坑：BFS 恒定边权失效/初始化三行顺序/过期条目 skip·进阶六连：最小连通支配集/IC 信息级联/Union-Find 回滚/max-dist = RTO/最小割 = egress 白名单/多秘密并行） / 工程映射：**多 agent 通信图信息流动控制**（Person=agent·Meeting=A2A/MCP/AG-UI 事件·Secret=API Key/PII/prompt·结果集=攻击面·微软 Agent Framework 攻击 = 反向 Dijkstra·上下文不一致 = 对偶问题·显式状态作用域 = agent 通信 ACL） / 热点：**微软 Agent Framework 遭 PyPI 供应链攻击（11/17-19：恶意版本窃取 Azure 凭证/GitHub secrets·上线不到一小时·「视为已失陷」）**·**Agent 协议栈补完（MCP+A2A+AG-UI = 智能体 TCP/IP——但协议 ≠ 安全）**·**Gartner：40%+ agentic AI 项目 2027 年前被取消（治理必须并行建设）**·**Grok 多智能体辩论 12%→4.2% 但出现能力幻觉**·**Fireworks AI 泄露扩大（Notion AI 日志·Cursor traces）**·**Black Friday 倒计时 2 天（最高流量+最大攻击面）** / 面试技巧：供应链攻击 = 框架即攻击面（依赖层三件套：锁定+hash/私有镜像/CI 沙箱）+ 协议栈 ≠ 安全栈（agent 版 TLS 还没到来）+ 凭证泄露影响面五步（定点→画图→Dijkstra→分级→阻断）+ Gartner 三对策（ROI 前置/治理并行/快速证伪）·金句「**如果此刻一个 agent 被攻陷，它的 secret 会沿通信图传播到多少个节点？这个数，就是你的攻击面。**」）
- 🏁 [Day 173 — 规划兼职工作（LC 1235 · 加权区间调度：贪心 vs DP 分水岭）/ 成本-容量-路由一体化控制台收官综合实战](week27/2026-11-24-weighted-interval-scheduling-unified-console-finale.md)（**排序 + DP + 二分：dp[i] = max(dp[i-1], profit[i] + dp[p(i)])·O(n log n)·加权版 end 最早贪心正式下课**（Go 实测：官方例 DP=120 vs 贪心=90·反例 DP=105 vs 贪心=10·三坑：无权贪心 = 计数·加权 DP = 决策/二分 last end <= start + 下标 +1/半开区间边界·进阶六连：多机 k 轨道 NP-hard/在线 eviction + shadow benchmark/异构资源分层/可变收益 = 峰谷定价版/k 项限制 = LC 188 区间版/方案回溯） / 工程映射：**一体化控制台 = 度量→决策→治理三层**（七行账单+(λ,P99) 水位线·PoT+Erlang C+离线 DP benchmark·绿黄红+熔断+合规白名单+Merkle 审计） / 热点：**黄仁勋复职（11/23）——NVDA 单日 +31.48%·市值 +$9,520 亿创历史纪录·11 天「全球最贵 incident」：CEO = 未容灾的单点·中断成本跳变不是线性·恢复 ≠ 结案（调查继续+首席合规官+12/3 听证）**·**ANT 期权 + 10 只杠杆 ETF 首日上市：VIX >40·IV 50-100% = 不确定性本身成了交易品·ANT 全场独跌 = 集群容量零和**·**Black Friday 倒计时 3 天 = 加权区间调度真实战场：降级 = 收益重加权→重算 DP·调度器 KPI 是毛利不是利用率** / 面试技巧：黄仁勋复职四件套（单点根因/RTO 定价/failover 预授权/合规长尾）+ ANT IV = 安全边际是给不确定性的期权费 + 黑五接单 = 全局记账 + 收官速答五连（30 分钟讲控制台/95% 利用率组合拳/NVDA 式 blameless postmortem/毛利率突降排查顺序：重试放大→水位线→潮汐失效→长上下文漂移）·金句「**单点的代价不是线性的，是跳变的。利用率 95% 不是高效，是裸奔。**」）


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

当前进度：**Week 28 Day 176 🖤 拜占庭容错与群体决策**（✅ LC 169→229 Boyer-Moore 多数派投票：>n/3 = 3f+1 算法直觉·鸽巢 k−1 槽·提名-验票两段式 = prepare-commit·O(n)/O(1) 流式数票·Go 实测 [3,2,3]/[1,1,1,3,3,2,2,2] + 拜占庭 n=3f+1 演示·Misra-Gries heavy hitters·分布式分片合并 = 本地提名 + 中心验票 + 黑五网关实时多数派检测工程映射 + 黎曼猜想 Lean 机器共识 vs 人类共识[11/20-21：1700 万行 Lean·3 内核 × 数十复现 = 冗余验证 quorum·副本共识 ≠ 客户端确认·邓煜等可读版] + Black Friday 现场日开闸 7 小时[20% 流量来自 AI agents·$142 亿上年实绩·Visa TAP 首战·坏 bot 37-42%] + MiniMax Talkie 苹果全球下架[平台超级节点否决权] + 美国算力管制收紧[Reuters 11/26 智谱推迟·星辰 4.1 昇腾 910C] + AWS Decider v2 判别降级[成本 -98%] + PBFT 三件套手推[quorum 交集不等式 |Q₁∩Q₂|≥2q−n≥f+1/三阶段各答一问/视图切换 = 网关 failover BFT 版] + 投票有用 vs 有害[错误相关 + 简单多数掩盖少数派 + 异构/判别降级/理由审计三解] + 速答五连[30 分钟 BFT 投票设计/黑五冒牌身份实时检测/3f+1 不等式/黎曼事件分布式现象/投票何时有害]）·**Day 177（Week 28 Day 4）周六 2026-11-28 复盘日 待开始**（Day 174/175/176 三课合流：多 agent 信任金字塔全图[通信图 → 消息协议 → 容错机制] + 混合模拟面试·时事前瞻：黑五首日战报出炉[Salesforce/Adobe 实时数据 + Visa TAP 首战成绩单]·网一小结·黄仁勋 12/3 听证倒计时 5 天）

**更新记录**：已连续更新 **176** 天
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
