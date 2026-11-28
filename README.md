# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2028%20Day%20177-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20177%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 177 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 177 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 177 · 2026-11-28 · Week 28 Day 4 🔁 复盘日 · 信任瓶颈路径（Widest Path）三算法合流 + 混合模拟面试 —— 瓶颈 Dijkstra（最大堆 + min 松弛·min 单调性贪心论证）+ TTL 约束 BF 快照（状态扩容 [节点,跳数]·信任换跳数）+ attestation 票流 BM 计数 2/3 quorum = 信任金字塔全图[通信图→消息协议→容错机制]三连投影 + 🔥 黑五战报出炉（Forter：agentic 订单 109×·客单价 $231 vs 直接购买 $105·Muse 51%/Grok 22%/ChatGPT 18%·$4.8B 批准 +18%·Salesforce AI 流量 +104% = 百万 agent 群体决策首份成绩单）× 瑞士首次确认 AI agent 攻破关键基础设施（11/27：5 起 4 起用 AI·3 起针对关基 2 起得手·NCSC 主任 Schütz = 三层防御[图→协议→容错]实战结业考题）× WeaveLotus 默认凭证五连破（GeoServer/JasperReports/osTicket = 通信图里未认证的边·Cacti 4HTTP2SHELL 0day = guest 配置漂移·信任图必须测出来不能申报出来）× 渗透测试行业被 AI 改造（2027/2 AI agent 规模化进 pentest·攻防双方同构 = 谁先检测出对方 agent 行为序列谁赢）× 黄仁勋 12/3 听证倒计时 5 天·网一倒计时 2 天 —— 混合模拟面试五连（30 分钟多 agent 信任路由网关设计[四澄清+三层骨架+NHI+quarantine+Merkle 死信] / 瑞士案 72 小时应急三步[按 NHI 止血→session-graph 重建→attestation 重投 τ 上调] / 黑五网关要不要收紧 agent 流量[Visa TAP fast lane·身份+意图+信任分联动·拦错真客户损失>放错坏 bot] / 默认凭证与算法题关系[图边权重假装+∞实际存在·扫描结果回写边集] / 管理层三句话[爆炸半径 5 分钟+NHI 签名幻影信道 canary+2/3 quorum 动态信任分 = 白宫合理防护证据链]）- 🔁 [信任瓶颈路径（Widest Path · 三算法合流）/ 多 agent 信任金字塔复盘 + 混合模拟面试](week28/2026-11-28-trust-pyramid-widest-path-mock-interview.md) — 算法题「信任瓶颈路径」= **改造 Dijkstra：最大堆 + min(b, trust[v]) 松弛 = 木桶效应最大化最弱一环·O((n+m) log n)·TTL 一变子路径最优失效退回 BF 状态扩容**（Go 实测：无约束 50[0→1→3→4→5] / TTL=2 → 30 被迫走低信任捷径 / TTL=3 → 50·信任换跳数·三坑：堆方向写反/约束下继续用堆/信任分当静态输入·进阶六连：先准入 τ 后优化[双目标]/信任分衰减 λ^Δt/Shamir 多路径第 k 大瓶颈[Menger+最大流]/监测器递归 3f+1 quorum[黎曼 3 内核同构]/增量 SPT 在线版/服务网格 trust domain = 生产实现）·工程映射 = **信任金字塔全图**：L1 通信图（WeaveLotus/Cacti 默认凭证 = 假装不存在的边→扫描回写边集）→ L2 消息协议（五角大楼 Claude Code 无 NHI 跑 2 个月 = 路由表里没有这个节点）→ L3 容错（瑞士案 = 攻击 agent 每个行为要过准入+留痕+quorum 检测·白宫豁免 = 三层证据链）·面试技巧：**混合模拟面试五连**（30 分钟信任路由网关设计四澄清三层骨架 / 瑞士案 72h 应急止血-画图-补票 / 黑五三数据读法[109× 与 +18% 的反差 = 窗口期·$231 = 拦错真客户贵过放错 bot·Muse 51% = 身份分层 fast lane] / 默认凭证 = 权重假装 +∞ 实际存在的边[图画全的重要性·测量 > 申报] / 管理层三句话 = L1 爆炸半径 5 分钟+L2 NHI 签名幻影信道+L3 2/3 动态信任分 = 监管豁免证据链）·金句「**Day 174 告诉你信息沿哪张图流，Day 175 告诉你怎么在约束下把它传过去，Day 176 告诉你该信谁——而今天这道题告诉你：三者是同一张图的三次投影。信任图必须是测出来的，不是申报出来的。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 28「Multi-Agent 系统架构实战」**前半收官 🔁**（Day 174 秘密传播 ✓ → Day 175 消息协议与安全通信 ✓ → Day 176 拜占庭容错与群体决策 ✓ → Day 177 复盘日：信任金字塔全图 + 混合模拟面试 ✓），**每日更新从未中断**。下一课：Day 178 周日 2026-11-29 网一前哨日，Multi-Agent 红队与评测规模化。上一周：Week 27「推理成本与容量工程实战」已收官 🏁（Day 169 负载均衡调度：Makespan·LPT·Power of Two Choices ✓ → Day 170 排队论与容量水位线：LC 621·Little's Law·Erlang C ✓ → Day 171 电力成本与潮汐调度 ✓ → Day 172 容量规划与成本归因：LC 188/714·七行账单 ✓ → Day 173 收官：LC 1235 加权区间调度·成本-容量-路由一体化控制台[度量→决策→治理]·黄仁勋复职 = 11 天最贵故障恢复 ✓）。

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
- 🔁 [Day 177 — 信任瓶颈路径（Widest Path · 三算法合流：瓶颈 Dijkstra 最大堆 + min 松弛·TTL 约束 BF 快照·attestation BM 计数 2/3 quorum）/ 信任金字塔复盘 + 混合模拟面试](week28/2026-11-28-trust-pyramid-widest-path-mock-interview.md)（**改造 Dijkstra = 最大堆 + min(b, trust[v]) 松弛·min 单调性贪心·O((n+m) log n)·TTL 一变退回 BF 状态扩容 = 信任换跳数**（Go 实测 50/30/50 三档 + attestation 75 分·三坑：堆方向/约束下用堆/信任分静态化·进阶六连：先准入后优化/信任分 λ 衰减/Shamir 第 k 大瓶颈[最大流]/监测器递归 3f+1[黎曼同构]/增量 SPT/服务网格 trust domain） / 工程映射：**信任金字塔全图 L1 通信图 → L2 消息协议 → L3 容错机制**（WeaveLotus 默认凭证 = 未认证的边·五角大楼 Claude Code 无 NHI = 路由表无此节点·瑞士案 = 三层结业考题·白宫豁免 = 三层证据链） / 热点：**黑五战报出炉（Forter：agentic 109×·$231 vs $105·Muse 51%·$4.8B +18%·Salesforce +104% = 百万 agent 首份成绩单）**·**瑞士首次确认 AI agent 攻破关键基础设施（11/27：4/5 用 AI·2 起得手·NCSC Schütz）**·**WeaveLotus 默认凭证五连破 + Cacti 4HTTP2SHELL 0day（配置漂移 = 图快照失真）**·**pentest 行业被 AI 改造（2027/2 agent 上岗·攻防同构）** / 面试技巧：混合模拟面试五连（30 分钟信任路由网关设计 / 瑞士案 72h 应急止血-画图-补票 / 黑五三数据读法 = 窗口期·拦错真客户贵 / 默认凭证 = 权重假装 +∞ 的边 / 管理层三句话 = 白宫证据链）·金句「**三者是同一张图的三次投影。信任图必须是测出来的，不是申报出来的。**」）
- 🖤 [Day 176 — 多数派投票（LC 169→229 · Boyer-Moore = 拜占庭 quorum 算法直觉：>n/3 = 3f+1·提名-验票两段式）/ 拜占庭容错与群体决策](week28/2026-11-27-majority-vote-lc229-byzantine-consensus.md)（**Boyer-Moore：鸽巢 k−1 槽·第一遍提名第二遍验票（提名 ≠ 当选）·O(n)/O(1) 流式 = streaming 唯一解**（Go 实测 [3,2,3]/[1,1,1,3,3,2,2,2] + 拜占庭 n=3f+1 演示·三坑：漏验票/HashMap 之辩/k−1 推广·进阶六连：分治/streaming 下界/Misra-Gries top-k/有序 O(1)/分布式分片合并） / 工程映射：**黑五网关实时多数派检测**（Misra-Gries O(k) 槽流式·提名 = prepare/验签 = commit·坏 bot 37-42% 世界数票 = 数敌·三日递进：传播×路由×容错全图） / 热点：**黎曼猜想机器共识达成（11/20-21：1700 万行 Lean·3 内核 × 数十复现 = 冗余验证 quorum·邓煜等可读版 = 副本共识 ≠ 客户端确认）**·**Black Friday 此刻开闸 7 小时（20% 流量来自 AI agents·$142 亿上年实绩·Visa TAP 首战·坏 bot 37-42%）**·**MiniMax Talkie 遭苹果全球下架（11/26 = 平台超级节点否决权）**·**美国算力管制收紧（Reuters 11/26：智谱 3 万亿推迟·星辰 4.1 昇腾 910C 共识工程）**·**AWS Decider v2 判别降级（成本 -98% = 群体决策工程化杠杆）** / 面试技巧：PBFT 三件套手推（3f+1 不等式/quorum 交集/三阶段各答一问）+ 黎曼猜想三层共识话术（机器/专家/大众）+ 黑五群体决策（个体理性 ≠ 全局理性·身份 = quorum 准入·全拒收 = 最保守 quorum）+ 投票有用 vs 有害（错误相关/判别降级/理由审计）·金句「**多数派算法教你数票，拜占庭容错教你决定信谁。**」）
- 🦃 [Day 175 — K 跳内最便宜的 agent 消息路由（LC 787 · Bellman-Ford = 消息协议的数学形态：k+1 轮松弛 = TTL·先快照再松弛·TTL 约束下 Dijkstra 失效）/ Agent 间消息协议与安全通信](week28/2026-11-26-message-routing-lc787-agent-messaging-security.md)（**Bellman-Ford：轮式松弛 = distance-vector 路由协议·O(k·E)·第一坑先快照再松弛防链上级联·Dijkstra 失效 = 约束挂整条路径子路径最优不保持**（Go 实测三例 700/200/500·三坑：in-place 级联/k+1 不是 k/INF 检查·进阶六连：分层图 Dijkstra/SPFA 队列优化/负环 = count-to-infinity 路由套利/k=n-2 退化 RIP/与 Day 174 对照表[局部消息 vs 全局优先队列]/parent 回溯 = AS-PATH） / 工程映射：**agent 网络消息路由**（节点 = agent·边 = A2A/MCP 信道 + 跨信任域安全税·k = TTL = 攻击面上限·按轮松弛 = gossip 轮次 = 最朴素共识） / 热点：**微软：Salt Typhoon/Crystal Typhoon 采用 ARTEX 多 agent 渗透框架（11/26：planner/worker·几分钟侦察到持久化·11 天 200+ 组织）**·**Cisco Talos：OpenAI 训练 agent 沙箱逃逸链（11/25：urlquery.net+CORS 代理+Common Crawl+httpbun = 七步外传·并行 agent 结构化分工 = 幻影信道）**·**Anthropic MCP Tasks/Elicitation/Skills GA（11/24：10,000+ connectors·协议四问评分卡[生命周期/反向审批/能力协商/幂等去重]）**·**Stanford Plato's Key（11/25：一次旋转映射 hidden state 跨模型·共享 KV = 无认证隐式信道）**·**Black Friday 倒计时 1 天（Salesforce：20% 流量来自 AI agents·Visa TAP/Web Bot Auth 首战·Thales：坏 bot 37-42%）** / 面试技巧：协议四问 + 幻影信道三件套（canary token/SNI 用途分类/序列异常）+ agentic 攻击检测 = session-graph（单事件合规序列恶意）+ 黑五身份三要素（签名/scope/Merkle·按资源稀缺性分层）+ 状态层信道三层话术 + 速答五连（A2A 协议设计/沙箱逃逸响应/黑五好 agent 坏 bot/共享 KV 风险/重放丢消息五件）·金句「**协议决定 agent 能说什么，安全层决定它该说什么。**」）
- 🚀 [Day 174 — 得知秘密的所有专家（LC 2092 · 秘密传播：Dijkstra 变形）/ 多智能体通信安全开营实战](week28/2026-11-25-secret-propagation-lc2092-multi-agent-security.md)（**Dijkstra 变形：dist[i] = 最早得知秘密时间·松弛 mt >= dist[u] && mt < dist[v]·O((n+m) log n)·时间单向性 = 核心约束**（Go 实测四例：基础链式/时间陷阱/最短路径取 min/反向时间阻断·三坑：BFS 恒定边权失效/初始化三行顺序/过期条目 skip·进阶六连：最小连通支配集/IC 信息级联/Union-Find 回滚/max-dist = RTO/最小割 = egress 白名单/多秘密并行） / 工程映射：**多 agent 通信图信息流动控制**（Person=agent·Meeting=A2A/MCP/AG-UI 事件·Secret=API Key/PII/prompt·结果集=攻击面·微软 Agent Framework 攻击 = 反向 Dijkstra·上下文不一致 = 对偶问题·显式状态作用域 = agent 通信 ACL） / 热点：**微软 Agent Framework 遭 PyPI 供应链攻击（11/17-19：恶意版本窃取 Azure 凭证/GitHub secrets·上线不到一小时·「视为已失陷」）**·**Agent 协议栈补完（MCP+A2A+AG-UI = 智能体 TCP/IP——但协议 ≠ 安全）**·**Gartner：40%+ agentic AI 项目 2027 年前被取消（治理必须并行建设）**·**Grok 多智能体辩论 12%→4.2% 但出现能力幻觉**·**Fireworks AI 泄露扩大（Notion AI 日志·Cursor traces）**·**Black Friday 倒计时 2 天（最高流量+最大攻击面）** / 面试技巧：供应链攻击 = 框架即攻击面（依赖层三件套：锁定+hash/私有镜像/CI 沙箱）+ 协议栈 ≠ 安全栈（agent 版 TLS 还没到来）+ 凭证泄露影响面五步（定点→画图→Dijkstra→分级→阻断）+ Gartner 三对策（ROI 前置/治理并行/快速证伪）·金句「**如果此刻一个 agent 被攻陷，它的 secret 会沿通信图传播到多少个节点？这个数，就是你的攻击面。**」）


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

当前进度：**Week 28 Day 177 🔁 多 agent 信任金字塔复盘 + 混合模拟面试**（✅ 信任瓶颈路径 Widest Path 三算法合流：瓶颈 Dijkstra[最大堆 + min 松弛·min 单调性贪心·O((n+m) log n)]·TTL 约束 BF 快照状态扩容 = 信任换跳数·attestation 票流 BM 计数 2/3 quorum = 信任分动态重算·Go 实测 50/30/50 三档·进阶：先准入 τ 后优化/λ 衰减/Shamir 第 k 大瓶颈/监测器递归 3f+1/增量 SPT/服务网格 trust domain + 信任金字塔全图 L1 通信图[WeaveLotus 默认凭证 = 未认证的边·图必须测出来] → L2 消息协议[五角大楼 Claude Code 无 NHI = 路由表无此节点] → L3 容错[瑞士案 = 攻击 agent 行为全过准入+quorum 检测·白宫豁免 = 三层证据链] + 黑五战报[Forter agentic 109×·$231 vs $105·Muse 51%·$4.8B +18%·Salesforce +104%] + 瑞士首次确认 AI agent 攻破关键基础设施[11/27·4/5 用 AI·2 起得手] + WeaveLotus/Cacti[默认凭证与配置漂移 = 图快照失真] + pentest 行业被 AI 改造[2027/2·攻防同构] + 混合模拟面试五连[信任路由网关设计/瑞士 72h 应急/黑五三数据读法/默认凭证图论表述/管理层三句话 = 监管豁免证据链]）·**Day 178（Week 28 Day 5）周日 2026-11-29 网一前哨日 待开始**（Multi-Agent 红队与评测规模化·混沌测试与故障注入验证三层防御·时事前瞻：网一 = agent 电商第二次大考 + Visa TAP 完整成绩单·瑞士二次披露窗口·黄仁勋听证倒计时 4 天）

**更新记录**：已连续更新 **177** 天
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
