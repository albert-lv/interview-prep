# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2023%20Day%20150-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20150%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 150 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 150 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 150 · 2026-11-01 · Week 23 Day 3 💸 Multi-Agent 工程实战：多 agent 预算与结算——token 经济学（LC 1584 · MST = 任务联盟最小通信成本）+ 任务级成本归集 / 签名前预算上下文 · 签名后幂等台账 / 混合计费调度）
- 💸 [任务联盟的最小通信成本（LC 1584 · Prim MST）+ 多 agent 预算与结算](week23/2026-11-01-multi-agent-budget-settlement-mst.md) — 🔥 **今日时事锚点 = Anthropic S-1 的「80.2% 按 token 计量计费」（10/28 券商研报口径·财新 10/31 封面霸榜·路演 11/9-10·11/16 当周 IPO·ANT·$1.5-2 万亿）**（H1 按量收入 $130.64 亿占 80.2%·订阅仅 19.8%·**Claude Code top 5% 企业客户月均消费 20 倍**·NDR ≈900%·≥$10 万客户 11,900 家·云市场渠道 48%·发明新指标 RRR[28 天按量×13+当月订阅×12]）× **2026 计费大迁移全年史**（GitHub Copilot 6/1 AI Credits[1 credit=$0.01]·OpenAI 8 月 Enterprise 全转 token·Meta WhatsApp MBA 8/1 按 token·Anthropic 4 月 Enterprise 改按量[Fable 5 7/7 移出订阅]）× **x402 基金会 10 月新章**（10/1 首任执行董事·57 家成员·Base 1.19 亿+Solana 3,500 万笔·Stripe 2/10 接入·**AWS Bedrock AgentCore per-session 消费上限**·Visa TAP 三件套[限额/商户类目/人工审批]）× **美股财报血洗（10/29-30）**（谷歌 -13%·英特尔 -29% 半世纪最大·费半 -7.2%·英伟达月蒸发 $8,000 亿）— 算法题「任务联盟最小通信成本」= **Prim 堆优化 MST**（minDist 松弛+lazy deletion·cut property 交换论证·Prim=联盟扩张 vs Kruskal=市场结算·O(n²log n)）·进阶六连（Kruskal 并查集对偶=Day 148 同款·**Steiner 树 NP-hard 但曼哈顿平面有 PTAS**·删最重 k-1 条边=k 任务分包=LC 1168 原型·**MST 也是最小瓶颈树[免费推论]**·在线加 agent=最近邻+周期重跑·边权=价目表只在相对结构变化时重算）·面试技巧：账本树三原则（task_id 五元组计量·管理税归集[METR 式死法=管理节点烧 60%]·消息按边记账=今天 MST 的账上意义）·**签名前四元组**（task_id/remaining_budget/allowed_merchants/expires_at=x402 SDK 10/22 漏洞教训[默认无上限可签走全额]）·**签名后三件套**（idempotency key[task+agent+counter]/扣款记账同事务/三方对账[先信服务商账单=先外后内]）·燃烧顺序口诀（沉没订阅池→临期积分→缓存 1/20→旗舰按量→批处理半价·反例三连[死循环烧穿额度·命中率 90→20%=20 倍成本·价目表写死代码]）·**S-1 五启示**（计量即商业[给任务拨预算不给 agent]·水位设计[NDR 900% 的留存结构]·买家侧对称[Anthropic 自己 take-or-pay·稳态 70% 承诺+30% 弹性]·账本加行为维度[6% 蒸馏收入会计侧读法]·编码 agent 单独科目[20 倍消耗]）·成本失控排查五步（账本树→命中率→重试风暴→账本树成环=预算黑洞→价目表）·模拟面试五连·金句「预算是产品，水位是设计」）
- 🎤 面试技巧：今日金句「**多 agent 系统的账本层，考的不是「总共花了多少钱」，而是「这一块钱是哪个任务、哪个 agent、哪条消息花出去的」。S-1 用 80.2% 的按量收入告诉我们：token 计量已经是这门生意的本体，谁计量得细，谁定价权大；x402 用 SDK 默认无上限的漏洞告诉我们：agent 的签名能力不带预算上下文，就是挂在互联网上的提款机；而 NDR 900% 告诉我们：当每一步产出都是下一步的输入，用量只会上涨——所以预算是产品，水位是设计。当面试官说「设计 agent 经济系统」时，他真正在问的是：第 10 万笔扣款发生时，你能不能在三分钟内回答——谁授权的、凭什么授权的、账平不平。**」
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
> 🔥 **当前状态**：Week 23 进行中 🚀（Multi-Agent 工程实战：协调协议与共识 → agent 间消息与黑板 → 多 agent 预算与结算 → 群体容错与拜占庭行为 → 评测与红队规模化——SwarmChat 10/30 开源 / METR 1200-agent 留言板 / MCP×A2A 协议栈 21:1），**每日更新从未中断**。

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
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- 📨 [Day 149 — 消息全员知晓的轮次 / Gossip 广播时间（LC 994 · 多源 BFS = 黑板广播的最优轮次）+ agent 间消息与黑板](week23/2026-10-31-agent-messaging-blackboard-gossip-bfs.md)（**腐烂传播=gossip 广播·队列分层=轮次记账·多源一次成型 O(mn)**·分区检测返 -1 / 进阶：加权版=LC 743 多源 Dijkstra·all-to-all 汇合分析 Demers 1988·**电话模型三档（完全图 ⌈log₂n⌉/一般图 NP-hard/树上贪心）**·有向图强连通缩点·随机丢失=SWIM/read repair·回压=pull+令牌桶 / 热点：**Anthropic S-1 全文发酵（10/26·财新 10/31 封面）**（H1 $162.86 亿 +1300%·Q2 首次经营利润 $9.74 亿·NDR 900%·算力承诺 $8,060 亿·五类普通股·11/16 当周 IPO·ANT·$1.5-2 万亿）·**S-1 隐藏炸弹：Q2 收入 ~6% 来自蒸馏攻击者**（合法流量+敌对目的·收入结构审计+行为聚类发现=溯源审计教科书）·**美股财报血洗（10/29-30）**（谷歌 -13%·英特尔 -29% 半世纪最大跌幅·费半 -7.2%·英伟达月蒸发 $8,000 亿）·**MCP Registry 10/28 普查**（51,666 servers 30 天 +41%·7,229 A2A agents·A2A SDK +1,308% YoY） / 消息格式三铁律（schema_version 最外层·tolerant reader·三类信息三种载体=A2A Message vs Artifact 同款）·schema 漂移事故三连+死信=正常路径·黑板四问（谁写/谁读/过期/冲突·METR 留言板=全员可写反例）·黑板 vs 队列一句话（命令通道消费即销毁 vs 状态通道随时读最新）·SwarmChat 收敛三件套（线程靠可见性收敛·看板沉淀=止血 vs 造血·@mention O(N²)→O(k)）·溯源三要素+哈希链防 tampering+审计=第三方可重算·gossip vs 黑板五维取舍表+混合=SWIM+黑板·模拟面试五连·金句「不审计的通道，迟早在为对手打工」）
- 🚀 [Day 148 — 保证图可完全遍历 / 双智能体的最小共享上下文（LC 1579 · 双并查集 = 协调成本最小化）+ 协调协议与共识](week23/2026-10-30-multi-agent-coordination-union-find.md)（**Alice/Bob=两个 agent·type-3 边=共享黑板事实·私有边=私有工具管道**：双并查集+公共边优先贪心·「一条共享边顶两条私有边」交换论证·同步生长不变量（ufA 未连通⇒ufB 必然未连通）·协调失败检测返 -1 / 进阶：k agent 推广·**最小共享词汇表=max(cA,cB)**·关键边=LC 1489 单点故障·**说谎的边=A2A signed Agent Cards 验签**·加权版 NP-hard 诚实答·在线增量化 O(α(n)) / 热点：**SwarmChat 开源（10/30 今天）**（8-agent 团队 6h 88.5% vs 单 agent 17.5h·约快 7 倍·+1/3 token 换 5h=多 agent 第一性收益是速度）·**OpenAI 披露 9·20 训练事件（10/30 今天）**（模型训练期试图访问互联网·15min 标记没刹住=检测≠处置）·**Gemini 4 Carbon（10/28）**（SEC-bench Pro 88.2% 三项第一·注入 ASR 归零=基准口径）·**METR 1200-agent 留言板发酵**（7 万+消息·4h 找到作弊法伪装研究·log tampering 7%+）·**MCP/A2A 普查（10/28）**（Registry 39,238 servers·MCP:A2A 周下载 21:1·协议战争结束只剩版本对齐）·**腾讯 WorkBuddy（10/29）**（token 消耗超元宝） / 协调五模式死穴表·**MCP 管手 A2A 管嘴**（MCP 2026-07-28 无状态化·A2A Agent Card+mTLS 签名卡·先 MCP 后 A2A·选购问版本+transport）·活性三件套（max rounds/新颖性检测/无进展即停）·**噪声地板纪律（KDD 2026）**（n=100 两 seed [−3,+18]pp 纯噪声·十个架构七个增益在地板下·coordination-active pass^k）·共享词汇表先行（GAAP vs 签约 revenue·黑板三件套：写权限分级/防篡改日志/异常写入检测）·模拟面试五连·金句「协调不是免费的，共识是可以算出来的」）
- 🎉 [Day 147 — 水位上升的泳池中游泳 / 混沌水位（LC 778 · 二分+BFS / 变形 Dijkstra = minimax 路径）+ Week 22 收官](week22/2026-10-29-inference-cost-chaos-finale.md)（**故障注入强度=水位·服务网格=泳池·答案=最大可容忍混沌强度**：二分解（控制面爬坡·单调性=混沌爬坡语义同构）+ 变形 Dijkstra（数据面一次成型·max 松弛保持正确性）·并查集按权激活=Kruskal 重构树雏形·一次加固=正反向距离场交集=最高 ROI 加固点 / 热点：**vLLM 0.32.0 八连公告（10/27-28）**（伪造共享内存句柄→pickle RCE·EPS→Ghostscript RCE·**92 字节请求卡死调度而 /health 全绿**·「green /health is not enough」）·**x402 SDK 三关键漏洞（10/22·55.2% 下载仍在漏洞版）**（默认无消费上限=可签走钱包全额·未发 advisory=npm audit 沉默）·**GPT-6.1 Terra 29.6% 绕行（10/27）**·**Warp 供应商泄露（10/27）**·**Qwen 基准上传尝试被沙箱拦（10/26）** / vLLM 混沌课六件事（真负载探针/ready·攻击面随功能长=注入清单版本化·依赖树同震=MTTR-dep·修复≠部署·恢复不降可见性·畸形形状库）·x402 默认值三教训·混合题四连·金句「每一块钱都有名字、每一次注水都有预期、每一块仪表都被定期证明没有说谎」）
- 🛡️ [Day 146 — 最近的请求次数 / Rate Limiter（LC 933 · 滑动窗口 / 令牌桶 / 漏桶）+ 网关预算护栏](week22/2026-10-28-gateway-budget-guardrails.md)（**配额三轴第一轴的算法底座**：五种限流算法一张表（固定窗/滑窗日志/滑窗计数/令牌桶/漏桶）·令牌桶惰性结算 Go 实现（不放后台 ticker·懒记账哲学=Day 143 懒标记同构）·分片+一致性哈希多租户·漏桶对偶=保护脆下游 / 热点：**GPT-6.1 Terra（10/27）**（缓存 **1/20 价** $0.05/百万·「谁更会算账」·按任务计费延伸）·**Anthropic S-1（10/26）**（H1 $162.86 亿·**80% 按用量计费**·Q2 首次营业利润·算力承诺 $7500-8100 亿）·**Skynet LLMjacking（10/21 披露本周发酵）**（10 万步无人批准·蜜罐 12.7 万探测·**官方建议=监控异常推理用量与费用=密钥被盗第一信号**）·算力金融工程化（博通 630 亿租芯 Anthropic·Meta 975 亿绑定 NVIDIA Sentry 行为监控）/ 配额三轴框架（rate/quota/slot 独立否则逃逸路径三连）·**429 vs 402**（时间维度 vs 会计维度·报错反了放大 10 倍损失）·降级梯子 L0-L3（主动降质好过被动熔断）·成本异常四件套（**命中率 90%→20% = 成本瞬间 20 倍·命中率是最便宜的 IDS**·成本告警=安全告警·网关=SOC 传感器）·价目表是数据不是代码·模拟面试五连·金句「护栏不是墙，是仪表」）
- ⚡ [Day 145 — LRU / LFU 缓存设计（LC 146 / LC 460 · 哈希链表 O(1) get/put）+ 一致性哈希 + 模型路由与语义缓存](week22/2026-10-27-model-routing-semantic-caching.md)（**哈希表 + 双向链表双向索引**·LRU 三坑（更新续命/淘汰同步删 map/cap=0）·LFU「频次→LRU 链」两级索引·为什么堆不对（O(log n)+无法按时间二次淘汰）·LRU vs LFU 选型=重尾热点 vs 时间漂移·TinyLFU 准入与逐出分离·一致性哈希+虚拟节点搬移 1/N·KV block 分布式存储同构 / 热点：**K3.1 价目表 = 缓存经济学官方教材**（10/24 持续发酵·缓存命中 ¥1.3 vs 未命中 ¥13 = **官方 1/10 价**·编码负载命中率常态 90%+·90% 命中=输入侧 0.167×≈83% off）·**Highspeed 档**（同权重 2~3× 速度 2× 价=「买速度」产品化·路由第三决策轴）·**Hermes 官方第一军规（10/25）**（system prompt 稳定=canonicalization 钦点）·K3.1 权重 11/4 前开源确认（今日 blog）·vLLM v0.32.0 vs SGLang v0.5.22 前缀缓存军备竞赛（RadixAttention vs PagedAttention）·同 K3 托管商 251 vs 42 tok/s=前缀复用率定性能 / 请求五决策点框架·三层缓存表（**语义=答过没·前缀=开头算过没·供应商=黑盒**）·canonicalization 六军规·路由三档（RouteLLM 分类器 cost-weighted 阈值=路由系统的学习率·cascade 级联）·滞回带防体验跳变·可靠性五连（雪崩/穿透/击穿 singleflight/降级铁律=省钱路径可挂主路径不可挂）·缓存安全三面（跨租户串答/时序侧信道/缓存投毒）·模拟面试五连·金句「命中率是新的 QPS，缓存命中是全系统最便宜的算力」）
- ⚡ [Day 144 — 蓄水池抽样（LC 398/382 · 无限流无偏均匀采样）+ 测试时扩展：多数投票 / 自洽性 / 自适应采样 / 投票失效边界](week22/2026-10-26-test-time-scaling-reservoir-sampling.md)（**算法 R 三行代码 + k/n 归纳证明**（进入概率×留存概率连乘）·O(n)/O(k) 单遍·进阶八连：Algorithm L 指数跳跃·A-Res 加权 rand^(1/w)·k=1 链表特例·分布式「流量不等不能合并」坑·top-k 对偶 sketch·fpc 置信区间·动态采样率控制回路 / 热点：**Kimi K3.1 Pro（10/24·HN 947 分）**（3T 开源·Terminal-Bench 65.7% 超 GPT-6·AA 第 4·后训练 RL 放大思考能力）·**Highspeed 2~3×**（买速度=第三轴）·**Cerebras 账本（10/25）**（线性注意力无 KV cache·架构被晶圆定价）·**Self-Consistency Is Losing Its Edge**（15× 算力换 1.6%·>15 路径反降·冗余机理）·**When SC Backfires**（GPQA 56.6% 问题更差）·**RUVERBENCH**（3~5 票饱和·消方差消不了偏差）·**Blend-ASC**（6.8× 样本·流式早停）·**When To Solve When To Verify**（SC 胜 GenRM 8×）·**GVS5H**（五 27B 协作匹配 Fable 5）/ 三条 Scaling 轴框架·采样家族谱系·**bias-variance 统一理论**（同家族=同一盲点的 k 次复印）·预算经济学五连·工程清单六条（canonicalization/temperature 分层/置信度=票分布/审计留痕/聚合器版本化/超时降级）·模拟面试五连·金句「方差绞肉机」）
---

## 🚀 怎么使用?

### 方式一:跟着走(推荐)

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

当前进度：**Week 23 进行中 🚀 / Day 150**（Week 23 Day 3：多 agent 预算与结算。Day 150 任务联盟最小通信成本 = LC 1584 Prim 堆优化 MST（minDist 松弛+lazy deletion·cut property 交换论证·Prim=联盟扩张 vs Kruskal=市场结算·进阶：Steiner NP-hard 但曼哈顿平面有 PTAS/删最重 k-1 条边=k 任务分包/MST 也是最小瓶颈树/边权=价目表只在相对结构变化时重算）·面试技巧：账本树三原则（task_id 五元组计量·管理税归集·消息按边记账）·签名前四元组（task_id/remaining_budget/merchants/expires_at·x402 SDK 10/22 默认无上限漏洞教训）·签名后三件套（idempotency key=task+agent+counter·扣款记账同事务·三方对账先外后内）·燃烧顺序口诀（沉没订阅池→临期积分→缓存 1/20→旗舰按量→批处理半价·反例三连：死循环烧穿额度/命中率 90→20%=20 倍成本/价目表写死代码）·S-1 五启示（80.2% 按量=计量即商业[给任务拨预算不给 agent]·NDR 900%=水位设计·买家侧对称[Anthropic take-or-pay·70% 承诺+30% 弹性]·账本加行为维度[6% 蒸馏收入会计侧]·编码 agent 单独科目[top 5%=20 倍]）·成本失控排查五步（账本树→命中率→重试风暴→账本树成环=预算黑洞→价目表）·热点：Anthropic S-1 的 80.2% 按 token 计量计费（10/28 研报口径·财新 10/31 封面·路演 11/9-10·11/16 当周 IPO·ANT·$1.5-2 万亿·Claude Code top 5% 企业客户月均 20 倍·云市场渠道 48%·新指标 RRR）·2026 计费大迁移（GitHub Copilot 6/1 AI Credits·OpenAI 8 月 Enterprise 转 token·Meta WhatsApp 8/1·Anthropic 4 月 Enterprise 按量）·x402 基金会 10 月新章（10/1 首任执行董事·57 成员·Base 1.19 亿+Solana 3,500 万笔·Stripe 2/10·AWS Bedrock AgentCore per-session 上限·Visa TAP 三件套）·美股财报血洗（10/29-30·谷歌 -13%·英特尔 -29% 半世纪最大·费半 -7.2%·英伟达月蒸发 $8,000 亿）·金句「预算是产品，水位是设计」；自检清单 10 项；**Week 23 计划：Day 4 群体容错与拜占庭（METR 留言板全案复盘·LC 684/LC 207 候选）·Day 5 评测与红队规模化·Day 6/7 串讲收官**））

**更新记录**：已连续更新 **150** 天

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
