# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2023%20Day%20152-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20152%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 152 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 152 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 152 · 2026-11-03 · Week 23 Day 5 🧪 Multi-Agent 工程实战：评测与红队规模化——基准污染危机 + 行为回归集 + 投毒样本库（LC 207 · 拓扑排序 = 评测流水线任务编排）+ METR 式行为归因 / 红队四件套 / 数据集版本化五军规）
- 🧪 [评测流水线能跑通吗（LC 207 · 拓扑排序 = 依赖调度 = 评测管道编排）+ 评测与红队规模化](week23/2026-11-03-evaluation-redteam-topological-sort.md) — 🔥 **今日时事锚点 = 基准污染危机总爆发（LiveCodeBench / LiveBench / SWE-bench Live·本周社区再复盘）**（静态基准全面失守：MMLU/HumanEval/SWE-bench Verified 均污染·行业共识收敛 date-windowed evaluation·"what's your post-cutoff score" 成采购标准问句·金句「Read the cutoff, or read fiction」）× **回归检测≠覆盖度（MDPI Electronics 10/9·High Coverage Does Not Guarantee Regression Detection）**（100% 输入覆盖率挡不住选错行为维度·回归集第一原则=行为维度覆盖 > 输入覆盖）× **SWE-bench Verified 10 月榜单更新（10/28·116 模型·scaffold 差异 10-15 分方差·采购三问）** × **模型升级隐性回归（FutureAGI 清单）**（总分涨 5 分但 StepEfficiency -12·省 70% token 但法律 cohort Groundedness 崩·单聚合仪表板隐藏的维度退化）× **WC2026 Agents 零污染基准（104 场比赛全部在训练 cutoff 后·赔率市场做经济锚定）** × **Meta-Harness（Claude Code 当 proposer 自动搜 harness·trace 中位 50.0 vs scores-only 34.6=evaluator 信息完备度决定优化上限）** × **Compiled AI 七类评测框架（token 效率/延迟/确定性/安全三闸门）** × **Anthropic S-1 路演倒计时（11/9 当周路演·11/16 当周 IPO·6% 蒸馏收入=红队流水线财务版）** × **GPT-6.2 Astra Codex 影子（11/1·canary 灰度=评测的产品形态）** × **OpenAI 10/30 扩大行为监控产品线** — 算法题「课程表」= **Kahn 拓扑排序**（入度=未满足依赖数·入度 0 入队=立即可执行·出队次数 < V = 有环 = 评测管道死锁·O(V+E)·DFS 三色标记备选·Kahn 出队序列=合法执行顺序·加优先级堆=Airflow 核心机制）·进阶六连（LC 210 返回顺序=CI task scheduler·并行波次=关键路径=wall-clock 下限·DFS 栈=deadlock detector·最小 Feedback Vertex Set NP-hard·增量 Kahn=动态吸收新依赖·加权版=带权关键路径=ETA 估计器）·面试技巧：METR 行为归因五步（收集→对齐→归因→四分法定性→报告）·红队四件套（自动化红队/行为回归集/投毒样本库/对抗进化）·数据集版本化五军规（hash 寻址/时间窗切片/Schema 是版本一部分/Lineage 可追溯/Split 不泄漏）·评测诚实性四纪律（CI/CD 化·三维报告/噪声地板/evaluator 自校准）·模拟面试五连·金句「评测系统最大的谎言不是数据造假，是『总分涨了』」）
- 🎤 面试技巧：今日金句「**评测系统最大的谎言不是数据造假，是『总分涨了』。总分涨 5 分的同时，步数效率掉了 12 分、法律场景的 groundedness 崩了、某个 cohort 的拒绝率翻倍——但聚合仪表板上一切静好。基准污染危机告诉我们：不标注 cutoff 日期的分数是小说不是数据；MDPI 的回归审计告诉我们：100% 的输入覆盖率挡不住选错行为维度；而 METR 的行为归因告诉我们：最危险的行为不是『做错了什么』，是『做对了的事服务于错的目的』。所以当面试官说『怎么评测 AI 系统』时，他真正在问的是：你的评测集版本化了吗、你的回归集覆盖的是输入还是行为、你的红队是手艺还是流水线、你敢不敢在报告里写——这个分数在哪个维度上，其实变差了。**」
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
- 🧪 [Day 152 — 评测流水线能跑通吗 / 课程表（LC 207 · 拓扑排序 = 依赖调度 = 评测管道编排）+ 评测与红队规模化](week23/2026-11-03-evaluation-redteam-topological-sort.md)（**Kahn 拓扑排序**（入度=未满足依赖·入度 0 入队·出队次数 < V = 有环 = 管道死锁·O(V+E)·DFS 三色备选）·进阶：LC 210=CI task scheduler·关键路径=wall-clock 下限·DFS 栈=deadlock detector·加权版=ETA 估计器 / 热点：**基准污染危机总爆发**（LiveCodeBench/SWE-bench Live·date-windowed evaluation·"Read the cutoff, or read fiction"）·**回归检测≠覆盖度**（MDPI 10/9·行为维度覆盖 > 输入覆盖）·**SWE-bench Verified 10 月榜单**（10/28·116 模型·scaffold 方差 10-15 分）·**模型升级隐性回归**（总分 +5 但 StepEfficiency -12）·**Meta-Harness**（trace 50.0 vs scores-only 34.6）·**S-1 路演倒计时**（11/9 路演·11/16 IPO·6% 蒸馏收入=红队财务版） / METR 行为归因五步·红队四件套（自动化红队/行为回归集/投毒样本库/对抗进化）·数据集版本化五军规·评测诚实性四纪律·金句「评测系统最大的谎言是『总分涨了』」）
- 🛡️ [Day 151 — 信任图里的冗余边 / 群体容错与拜占庭行为（LC 684 · 并查集 = 信任图环检测 = collusion 定位器）](week23/2026-11-02-group-fault-tolerance-byzantine-union-find.md)（**并查集环检测**（树=信任网络最小安全形态·find(u)==find(v)⟹成环·路径压缩+按秩合并·O(n·α(n))·摊还）·进阶：LC 685 有向版·反馈边集 NP-hard·签名的边才允许 union / 热点：**2026 agent 安全三连复盘**（arXiv 2610.12463v1·10/30）·**S-1 失控 agent 风险进 SEC**（财新 10/31·80 页·11/16 IPO）·**DeepSeek 自主 Super-Agent**（11/1）·**UBS 12,700 员工 agent**（1%=127 个坏节点） / 故障分类学三档·f<n/3 口试推导·METR 全案五步·混沌四注入·金句「当 f 个 agent 说谎时，剩下的 n−f 个还能不能给出正确答案」）
- 💸 [Day 150 — 任务联盟的最小通信成本 / 多 agent 预算与结算（LC 1584 · Prim MST）+ token 经济学](week23/2026-11-01-multi-agent-budget-settlement-mst.md)（**Prim 堆优化**（minDist 松弛+lazy deletion·cut property·Prim=联盟扩张 vs Kruskal=市场结算·O(n²log n)）·进阶：Steiner NP-hard 平面 PTAS/删最重 k-1 条边=任务分包/MST=最小瓶颈树/边权=价目表相对结构变才重算 / 热点：**Anthropic S-1 的 80.2% 按 token 计量计费（财新 10/31 封面）**（H1 $162.86 亿·按量 $130.64 亿·Claude Code top 5%=20 倍·NDR 900%·RRR 新指标·11/16 当周 IPO·ANT·$1.5-2 万亿）·**2026 计费大迁移**（GitHub Copilot 6/1 AI Credits·OpenAI 8 月 Enterprise 转 token·Meta WhatsApp 8/1）·**x402 基金会 10 月新章**（10/1 首任执行董事·57 成员·AWS Bedrock AgentCore per-session 上限·Visa TAP 三件套） / 账本树三原则（task_id 五元组·管理税归集·消息按边记账）·签名前四元组+签名后三件套（idempotency key·三方对账先外后内）·燃烧顺序口诀（沉没→临期积分→缓存 1/20→旗舰→批处理）·S-1 五启示·成本失控排查五步·金句「预算是产品，水位是设计」）
- 📨 [Day 149 — 消息全员知晓的轮次 / Gossip 广播时间（LC 994 · 多源 BFS = 黑板广播的最优轮次）+ agent 间消息与黑板](week23/2026-10-31-agent-messaging-blackboard-gossip-bfs.md)（**腐烂传播=gossip 广播·队列分层=轮次记账·多源一次成型 O(mn)**·分区检测返 -1 / 进阶：加权版=LC 743 多源 Dijkstra·all-to-all 汇合分析 Demers 1988·**电话模型三档（完全图 ⌈log₂n⌉/一般图 NP-hard/树上贪心）**·有向图强连通缩点·随机丢失=SWIM/read repair·回压=pull+令牌桶 / 热点：**Anthropic S-1 全文发酵（10/26·财新 10/31 封面）**（H1 $162.86 亿 +1300%·Q2 首次经营利润 $9.74 亿·NDR 900%·算力承诺 $8,060 亿·五类普通股·11/16 当周 IPO·ANT·$1.5-2 万亿）·**S-1 隐藏炸弹：Q2 收入 ~6% 来自蒸馏攻击者**（合法流量+敌对目的·收入结构审计+行为聚类发现=溯源审计教科书）·**美股财报血洗（10/29-30）**（谷歌 -13%·英特尔 -29% 半世纪最大跌幅·费半 -7.2%·英伟达月蒸发 $8,000 亿）·**MCP Registry 10/28 普查**（51,666 servers 30 天 +41%·7,229 A2A agents·A2A SDK +1,308% YoY） / 消息格式三铁律（schema_version 最外层·tolerant reader·三类信息三种载体）·schema 漂移事故三连+死信=正常路径·黑板四问·黑板 vs 队列一句话·SwarmChat 收敛三件套·溯源三要素+哈希链防 tampering+审计=第三方可重算·gossip vs 黑板五维取舍表·模拟面试五连·金句「不审计的通道，迟早在为对手打工」）

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

当前进度：**Week 23 进行中 🚀 / Day 152**（Week 23 Day 5：评测与红队规模化。Day 152 课程表 = LC 207 Kahn 拓扑排序（入度=未满足依赖·入度 0 入队=立即可执行·出队次数 < V = 有环 = 评测管道死锁·O(V+E)·DFS 三色标记备选[灰=回边=环]·Kahn 出队序列=合法执行顺序·加优先级堆=Airflow 核心·进阶：LC 210=CI task scheduler·关键路径=wall-clock 下限·DFS 栈=deadlock detector·最小 Feedback Vertex Set NP-hard·增量 Kahn·加权版=ETA 估计器）·面试技巧：METR 行为归因五步（收集→对齐[跨 run]→归因[时间相关>因果链>排除法]→四分法定性[有意/系统性/偶发/涌现·涌现不可修只可监控]→报告[第三方可重算]）·红队四件套（自动化红队[攻击生成器也是 LLM·GANGAN/PAIR/TAP·红队与生产隔离]·行为回归集[维度覆盖>输入覆盖·子指标分列·diff 可解释]·投毒样本库[分类学/版本化/自动化触发]·对抗进化[元数据驱动资源分配]）·数据集版本化五军规（hash 寻址四维版本号/时间窗切片=抗污染唯一解/Schema 是版本一部分/Lineage 可追溯/Split 不泄漏）·评测诚实性四纪律（CI/CD 化·三维报告[挂钟×token×通过率]·噪声地板常态化·evaluator 自校准）·热点：基准污染危机总爆发（LiveCodeBench/SWE-bench Live·"Read the cutoff, or read fiction"）·回归检测≠覆盖度（MDPI 10/9）·SWE-bench Verified 10 月榜单（10/28·116 模型）·模型升级隐性回归（总分+5 但 StepEfficiency -12）·Meta-Harness（trace 50.0 vs scores-only 34.6）·WC2026 零污染基准·S-1 路演倒计时（11/9 路演·11/16 IPO·6% 蒸馏收入）·GPT-6.2 Astra Codex 影子（11/1）·OpenAI 行为监控产品线（10/30）·金句「评测系统最大的谎言是『总分涨了』」；自检清单 10 项；**Week 23 计划：Day 6/7 串讲收官**）

**更新记录**：已连续更新 **152** 天

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
