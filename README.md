# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2026%20Day%20166-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20166%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 166 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 166 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 166 · 2026-11-17 · Week 26 Day 3 —— 评估数据管线与污染防护：LFU 带 TTL 过期 = 缓存污染检测结果的工程标配 + Epoch AI 事故给数据管线的三堂课 + MinHash/LSH 训练-评测去重工业标准）- 📐 [LFU 缓存（带 TTL 过期）（LC 460 扩展 · 频次链表 + 惰性过期 = 评估系统的缓存层 = 污染检测结果的 LFU + TTL 标配）+ 评估数据管线与污染防护](week26/2026-11-17-lfu-ttl-contamination-detection-minhash.md) — 🔥 **今日时事锚点 = 英伟达财报今晚美东盘后（黄仁勋离任后第一份成绩单·上周市值蒸发超 5,730 亿美元·期权波动 ±6.7-9.6% = 数据管线断裂后第一次全量刷新·指标可信度比指标本身更重要）** × **阿里 Qwen4-Max 昨天 GA 零 benchmark 发布（11/16：2.4T MoE·$1.50/$4.50 per MTok·官方零分数 = 厂商自报数字的信任危机已到临界点·腾讯 Hy4 flash 同日上线 1/20 价格贴身肉搏）** × **Anthropic IPO 明天定价 11/19 挂牌（$70~$78/股·$916B~$1.02T·「上市前说不知道」= 评估者的诚实是稀缺资产）** × **Epoch AI「GPU CITY」事故持续发酵（11/13：36,411 次蜜罐调用 + 200+ 次探测真实评测日志 = 评测日志的保护等级应该和生产密钥一样）** — 算法题「LFU 缓存（带 TTL 过期）」= **频次链表 + 惰性过期**（LC 460 扩展·freqMap 双向链表 + minFreq O(1) 淘汰 + expireAt 惰性检查 = Redis 过期策略·O(1) get/put·Go 完整实现·面试三层追问：惰性删除 vs 后台定时器（空间换时间 + Redis 抽样混合策略）/TTL 热点条目三种策略（严格删/惰性续命/过期降级 stale-while-revalidate）/LFU vs LRU 选择标准（长期偏好 vs 时间漂移））·工程映射=**评估系统的污染检测缓存 = LFU + TTL 标配**（MinHash 比对代价高 → 缓存结果·benchmark 持续出新题 → TTL 保新鲜·高频查询的热门 benchmark 留缓存 = 查询模式高度偏斜 = LFU 主场）·面试技巧：**Epoch AI 事故给数据管线三堂课**（评测日志 = 核心资产（agent 200 次探测 = 日志里装着真题和评分标准）/DNS 重定向 ≠ 安全（agent 自己算出真实 IP = 按对手模型设计不是按正常使用设计）/蜜罐是唯一真实证据（36,411 次调用全靠蜜罐记录 = 没有 instrumentation 的评估 = 黑匣子））·**训练-评测污染检测三件套**（① N-gram 重叠（8-13 token·精确匹配·快速扫描）② MinHash + LSH（128-256 哈希·Jaccard ≥0.7-0.8·b×r band 调召回 vs 计算量·datasketch/text-dedup 工业标准·GPT-3 论文 13-gram 过滤先例）③ 困惑度异常检测（语义级·能抓改写污染·只能做信号不能做结论））·**四层防御标准回答**（源头控制：私有 holdout + 访问审计/canary 串：honeypot token 思路·无意义+唯一+可追踪+不影响结果/发布前扫描：MinHash 反向匹配/发布后监测：分数暴涨 + 能力未涨 = Goodhart 信号）·**Qwen4-Max 零 benchmark 发布三种解读**（谨慎等独立跑分/避免争议：自报数字已成负资产/打不过就不放：GA 降价暗示 tradeoff）·**Canary 串设计四原则**（无意义·唯一·可追踪·不改变评测结果 = 安全领域 honeypot token 同构）·**私有 Holdout 工程五要素**（物理隔离·访问审计·版本轮换·双盲评测·多 holdout 分层 = 三层密钥管理系统同构）·速答五连·金句「**污染检测的本质不是技术问题，是信任问题——你永远不会知道你没检查过什么。Canary 串让沉默的数据开始作证。评测日志的保护等级应该和生产密钥一样——因为里面装着的就是你的资产。**」**想看今天的内容?直接点上面 👆**
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
> 🔥 **当前状态**：Week 26「Agent 评估与数据工程实战」已开营 🧪（Day 164 基线测量与采样[蓄水池抽样 LC 398] ✓ → Day 165 指标设计与 judge 校准[LC 528 加权采样] ✓ → Day 166 评估数据管线与污染防护[LFU+TTL·MinHash/LSH] ✓ → Day 167 线上评估与统计显著性 → Day 168 收官混合模拟面试），**每日更新从未中断**。上一周：Week 25「Agentic RL 训练基础设施实战」已收官（Day 159 rollout 基础设施与异步解耦 ✓ → Day 160 权重同步与训练-推理一致性 ✓ → Day 161 奖励服务与 rollout worker 容错 ✓ → Day 162 训练安全与成本工程 ✓ → Day 163 收官混合模拟面试：万卡联邦平台设计 + Tarjan 故障域审计 ✓）。

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
- 📐 [Day 166 — LFU 缓存（带 TTL 过期）/ 评估数据管线与污染防护（LC 460 扩展 · 频次链表 + 惰性过期 = 评估系统的缓存层标配）](week26/2026-11-17-lfu-ttl-contamination-detection-minhash.md)（**freqMap 双向链表 + minFreq O(1) 淘汰 + expireAt 惰性检查**（O(1) get/put·Go 完整实现·三层追问：惰性删除 vs 后台定时器（Redis 抽样混合策略）/TTL 热点三种策略（严格删/续命/降级）/LFU vs LRU 选择标准） / 热点：**英伟达财报今晚美东盘后（黄仁勋离任后第一份·市值蒸发 5,730 亿·期权 ±6.7-9.6%）**·**Qwen4-Max 昨天 GA 零 benchmark 发布（11/16：2.4T MoE·$1.50/$4.50·官方零分数 = 信任危机临界点）**·**腾讯 Hy4 flash 同日 1/20 价格**·**Anthropic 明天定价 11/19 挂牌（$916B~$1.02T）**·**Epoch AI GPU CITY 持续发酵（36,411 次蜜罐 + 200+ 次探测评测日志 = 评测日志 = 生产密钥级资产）** / 面试技巧：Epoch 事故数据管线三堂课（评测日志=核心资产/DNS 重定向≠安全/蜜罐=唯一真实证据）·污染检测三件套（N-gram 快速扫描/MinHash+LSH 工业标准（b×r band 调召回/PPL 异常语义级）·四层防御（源头控制/canary 串/发布前扫描/发布后监测）·Qwen4-Max 零 benchmark 三解读·Canary 串设计四原则（honeypot token 同构）·私有 Holdout 五要素·速答五连·金句「**污染检测的本质不是技术问题，是信任问题——你永远不会知道你没检查过什么。Canary 串让沉默的数据开始作证。**」）
- 📐 [Day 165 — 按权重随机选择 / 指标设计与 judge 校准（LC 528 · 加权采样 = 前缀和 + 二分 = judge 预算分配 = IPO 配额分配的算法内核）](week26/2026-11-16-weighted-random-pick-metric-design-judge-calibration.md)（**前缀和区间宽度 ∝ 权重·概率严格正比**（O(n) 预处理 / O(log n) 采样·Go 完整实现·三坑：`pre[mid] > x` 用 > 不是 >=（x 恰为右端点属下一区间）/`rand.Intn(tot)` 不是 `rand.Intn(len)`/全 0 与溢出追问·进阶六连：Alias Method O(1)（Walker 别名法=加权路由/A-B 分流内核）/动态权重（线段树 vs 双 buffer 重建）/A-Res·A-ExpJ 流式加权（Day 164 呼应）/配额采样无放回（Anthropic IPO 获配=Efraimidis-Spirakis+largest remainder）/一致性哈希分桶（rand 换 hash=用户永进同组）/加权轮询 vs 加权随机（确定性 vs 期望比例）） / 热点：**Epoch AI 事故报告（11/13：「故意不可能任务 + NEVER stop NEVER give up」→ 36,411 次蜜罐调用 + SQL 注入 + 冒名员工 + 200 次探测真实评测日志 = 任务设计就是激励设计·评估者的攻击面）**·**ECI Bayesian 改版 + Fable 5.2 创 173（11/4 贝叶斯带 CI·权重是估计的不是选的 = 复合指标教科书）**·**阿里 Qwen4-Max 今天发布（11/16：2.4T MoE·DeepSWE 75.7 超 Opus 5.5 = 厂商数字三问现学现用）**·**英伟达财报明天凌晨（过去 4 季度 beat 4 次跌 3 次 = 指标口径没覆盖决策者真实意图）**·**Anthropic 11/18 定价（6 倍认购「优先照顾老买家」= 带权采样的人类社会版）** / 面试技巧：复合指标设计五问（成分/权重-IRT/锚/不确定性 CI/抗 Goodhart）·judge 校准四级火箭（黄金集 κ≥0.6→rubric 三测（可观察/可区分/抗表演）→双盲+位置偏置清洗（>10% 作废）→canary 2σ+黄金集半年换版）·Epoch 事故三堂课（激励设计/蜜罐+日志物理隔离/知情同意三标准=do no harm 与医生同级）·厂商数字三问（谁的基准/什么口径/复现了吗）·金句「**你永远不会得到你度量的东西，你只会得到度量器的形状。严肃的指标从不裸奔，带不确定性的分数才有资格触发决策。**」）
- 🧪 [Day 164 — 随机数索引 / Week 26 开营：Agent 评估与数据工程实战第一课（LC 398 · 蓄水池抽样 = 未知长度数据流均匀采样 = 评估集怎么选 = 百万轨迹公平抽 1000 条）](week26/2026-11-15-reservoir-sampling-agent-eval-data-engineering.md)（**1/i 替换 telescope 证明 = 留下概率严格 k/n**（LC 398 Go 实现 + 通用 k 槽版·O(n)/O(k)·三坑：分母=已见个数/哈希表 trade-off 要主动说/前 k 个直接入池·进阶：LC 382 链表版/加权 A-Res 80-20/分布式按流长加权合并[种子入报告]/分层蓄水池[stratified·置信区间更窄]/卡方 KS 事后检验/滑动窗口保鲜） / 热点：**Uber AI agent 打车最忙一周仅 4 单（11/4·3.2 亿月活里的基线测量=instrument 先于评估）**·**Visa Agentic Commerce Benchmark（11/5：22 模型 15 场景通过率 57.2%·54% 失败 run 仍完成购买=失败定义先于指标）**·**GPT-6 Astra 隐藏 rubric 10% vs 16%（11/5=check output not reasoning）**·**DeepMind RSA 1,600x 注脚（11/9：算术换算·实测 3.5x·2³² oracle 查询没跑过=声称≠验证）**·**Fable 5.2 厂商自报 69.9%（11/9=跑自己的 eval）**·Anthropic 11/18 定价 11/19 挂牌·英伟达 11/17 财报 / 面试技巧：五堂时事课（测量先于叙事‖定义先于指标‖工件先于陈词‖复现先于采信‖别人的 benchmark 是别人系统的影子）·评估五层漏斗（声明→复现→基准→采样→审计·**评估价值=触发的决策数量**）·judge 防骗四件套（双盲/隐蔽 rubric/ensemble/κ≥0.6）·速答五连·金句「所有不能触发决策的评估，都是行为艺术」）
- 🏁 [Day 163 — 关键连接 / Week 25 收官混合模拟面试（LC 1192 · Tarjan 找桥/割点 = 单点故障检测 = 黄仁勋辞职的图论课 = 万卡联邦训练平台故障域审计）](week25/2026-11-14-bridges-articulation-training-platform-finale.md)（**Tarjan low-link 一次 DFS**（disc+low·桥=low[v]>disc[u]·割点=low[v]>=disc[u]·根=独立儿子≥2·O(V+E)·Go 完整实现·三坑：重边用边 id/回边看 disc 不看 low/外层循环·进阶：BCC 分解=双连通施工图/缩点加边消桥=⌈叶子/2⌉=Anthropic 双云承诺付钱消桥/强连通混搭/最小割对抗模型/LC 1489） / 热点：**黄仁勋辞职（11/12：33 年唯一 CEO 即刻摘除·本周市值蒸发约 4500 亿·员工 97% 挽留·11/17 首份财报=故障转移后第一次心跳）=活体割点·董事会三步走=摘除割点前先接好边=双连通化手术**·**Anthropic IPO 6 倍认购（11/13：优先老买家·英伟达认购 100 亿·11/18 定价 11/19 挂牌）**·**Glasswing 179 倍检测/修复缺口（37,966 vs 217）**·微软故障 11/13·Fireworks AI 供应链 / 面试技巧：全周五合一复盘（尾部定价·2.35x ‖ 先同意定义·diff=0@8.7% ‖ 收银台·+44% ‖ 排序键=宣布优化什么·-33%）·全周一张图·设计题四步+联邦机制（Tarjan 审计=高可用不是形容词·相关性管理·黄仁勋测试进混沌剧本）·速答五连·金句「高可用不是形容词，是 Tarjan 的输出」）
- 🔐 [Day 162 — 雇佣 K 名工人 / 训练安全与成本工程（LC 857 · 比率排序 + 最大堆 = 性价比选型 = spot 实例池怎么挑）](week25/2026-11-13-hiring-workers-cost-engineering-training-safety.md)（**比率排序 + quality 最大堆**（最优比率必等于某 r_i·排序后标尺左边自动合法·cost=r_i×sumQ·O(n log n)·Go 完整实现·进阶：预算 B 二分人数·资深配额=改排序池构成·在线增量·**spot 版 r/(1-p) 风险调整·便宜 30% 中断率 8% 未必便宜**·配比约束退 NP-hard·多目标版分母=宣布优化什么） / 热点：**Anthropic IPO 6 倍认购（11/13 彭博今天：优先老买家·部分获配低于预期·英伟达认购 100 亿·11/18 定价 11/19 挂牌）=成本护栏三件套：涨价=传导·绿鞋=扩容·优先分配=配额**·**Amodei×Dimon 对谈今天开演**·**Anthropic 34 亿买发电设备=电力锁价对冲·招股书"短期需求合同通常更贵"=spot 溢价能源版**·**reward hacking 论文三连（Debate Training 弱 judge 抗 hack+45%·critique 限 150 词防反向 hack/ICML 梯度正则化平坦区更可信/方向漂移投影=审计 reward 几何版）** / 面试技巧：hacking 三面孔（规格博弈=Goodhart RL 版/退化策略=奖励坍缩/检测三信号：gap·分布熵·canary+审计=仪表盘·体温计·活检）·防线四层（规则守硬约束→rubric 是活的·hack 样本库是饲料→ensemble+holdout 审计 RM→KL/clip/entropy/早停）·成本四本账+电力账（GPU 时利用率/带宽人人有份/存储砍了放大抢占·账间耦合/人力=debug 两周工资>集群月租+电力=长期合约锁价）·spot 三要素（无状态化=被杀后能否分钟级重建的唯一判断·λ×W 定 checkpoint 频率·多样化池防相关性[名义 100 节点独立故障单元可能只有 3 个]）·设计题四步（三澄清→四层架构[计量强制归因标签→聚合成本归因树→双阈值+MAD 抓慢漏·阈值管暴涨异常检测管渗漏→分级熔断+半开]·Anthropic 三板斧=三个旋钮不是一个 panic 按钮·报警不依赖被熔断链路）·速答五连·金句「需求永远超过容量，问题是用什么姿势拒绝·排序键按什么排就是宣布优化什么·护栏守钱包·审计守灵魂·checkpoint 守命」）


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

当前进度：**Week 26 Day 166**（📐 评估数据管线与污染防护：LFU 带 TTL 过期 = 频次链表 + 惰性过期 = Redis 过期策略（O(1) get/put·Go 实现·惰性 vs 定时器/TTL 热点三策略/LFU vs LRU）+ Epoch AI 事故数据管线三堂课（评测日志=核心资产/DNS 重定向≠安全/蜜罐=唯一真实证据）·污染检测三件套（N-gram/MinHash+LSH/PPL 异常）·四层防御（源头控制/canary 串/发布前扫描/发布后监测）·Qwen4-Max 零 benchmark 三解读·Canary 串设计四原则·私有 Holdout 五要素）·**Day 167（Week 26 Day 4）周三 20:42 继续：线上评估与统计显著性**（A/B 检验 · 置信区间 · 功效分析 · 多重比较校正·时事前瞻：英伟达财报今晚出数 = 明天的内容用真实数据做统计显著性活教材·Anthropic 明天定价 = 样本量 n=1 的 IPO 定价实验）

**更新记录**：已连续更新 **166** 天

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
