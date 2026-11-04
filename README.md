# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2023%20Day%20153-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20153%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 153 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 153 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 153 · 2026-11-04 · Week 23 Day 6 🎉 Multi-Agent 工程实战串讲收官：全周一张图 + 混合模拟面试「50-agent 代码工厂」六连问 + Week 24 长时程 Agent 工程预告（LC 329 · 记忆化搜索 = 回归测试最小覆盖集 = 严格递增的能力爬坡路径））
- 🎉 [矩阵中的最长递增路径（LC 329 · 记忆化搜索 = 回归测试最小覆盖集）+ Week 23 收官混合模拟面试](week23/2026-11-04-week23-finale-longest-increasing-path.md) — 🔥 **今日时事锚点 = Kimi K3.1 Pro 完整权重今天到期（10/24 发布·11/4 前放权重=今天！）**（3 万亿参数全球最大开源权重模型·100 万上下文·Terminal-Bench 4.0 **65.7% vs Opus 5.5 66.4 仅差 0.7 分**·API $2/$10=GPT-6 Astra 的 1/5·**内部测试学走路：持续分析实验结果→定位问题→迭代优化，从几步倒地→交替迈步→连续行走 11.2 秒，修正肩部力臂 36 处错误**——这就是严格递增的能力爬坡路径：每次迭代严格更好[无环]、修过的错误不重犯[记忆化]）× **Anthropic IPO 路演 T-5 天（tmtpost 11/3：一张 8950 亿美元的账单）**（11/9 当周路演·11/16 IPO·Nasdaq ANT·不可撤销算力承诺逼近 9,000 亿·云渠道占营收 48%[2023 年仅 11%]·Q3 增速放缓 140%+→45%·客户/供应商/投资者/竞争对手四重身份同一家公司）× **Kimi 游戏开发与创意工作解决方案（11/2 发布）**（K3.1+Kimi Code+Kimi Work Agent 直接操作 Unity/虚幻引擎/Blender·交付可继续编辑的文件+清晰操作记录=审计日志产品形态）× **DeepSeek 搜索 API 11/1 正式计费**（5 元/千次·agent 工具链每一环都有价目表了） — 算法题「矩阵中的最长递增路径」= **记忆化 DFS**（严格递增天然无环=不需要 visited[面试官最爱挖的坑]·memo[i][j]=从状态出发的最长连续改进链·**memo 表=回归测试最小覆盖集：不重测/不漏测/每状态一条**·O(mn)/O(mn)·DAG 最长路径通用四步模板：状态→转移→memo→全图取 max）·**双解收官**：Kahn 剥层=Day 152 LC 207 的第二种解法（出度 0 是山顶·反向剥 k 层=k·自顶向下 vs 自底向上一体两面）·进阶六连（非严格版 Tarjan 缩点[背题党筛除坑]·输出路径=memo 值-1 逆推=回归集按序执行清单·分层并行=CI 波次调度）·面试技巧：**全周一张图**（148 协调 1579→149 消息 994→150 预算 1584→151 容错 684→152 评测 207→153 收官 329：协议管说话/账本管花钱/容错管使坏/评测管证明/记忆化管别坏回去）·**混合模拟面试「50-agent 代码工厂」六连问**（Q1 协调：supervisor 三必答件[schema 校验/预算硬顶/终止条件]→Q2 消息：三类信息三种载体+聊爆聊死排查→Q3 预算：账本树+给任务拨预算不给 agent 拨→Q4 容错：三分类治理+单节点日志分不出幻觉/注入/作恶按最坏处理→Q5 评测：评测协议先行+敢写「这个维度变差了」→Q6 收口 P0：溯源[哈希链验证据]→画像[合法行为里的敌意模式=6% 蒸馏同款]→归因[定性四分]→处置[kill switch]→入册[投毒库+回归集]）·高频速答六连·金句「设计决定系统能不能跑，记忆化决定它会不会变好」）
- 🎤 面试技巧：今日金句「**多智能体工程收官的一句话：设计决定系统能不能跑，记忆化决定它会不会变好。K3.1 Pro 用 36 处力臂修正换来 11.2 秒的连续行走，靠的不是某一次神力——是每一步都严格好于上一步，且修过的错误永不再犯。系统的演进不是把格子全部点亮（那是 BFS 的广度），而是找到那条最长的、严格递增的爬坡路径：每一步都比上一步高，一旦绕回低处（环），之前的修正就全部作废。所以 memo 表不是优化技巧，是工程纪律——它是回归测试的最小覆盖集，是『变好』这件事的持久化层。当面试官问『怎么让多 agent 系统持续可靠地变好』，他真正在问的是：你的改进有没有严格递增的形状、修过的问题入册了吗、你的系统里有没有环——以及，上一次回归，是哪条路径断了。**」
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
> 🔥 **当前状态**：Week 23 收官完成 🎉 → **Week 24「长时程 Agent 工程」启动**（状态持久化与 checkpoint / 心跳与健康检查 / 可观测性 / 容错分类与自动恢复 / 混沌测试与故障注入——K3.1 Pro 权重开源后的 agent 长任务实测 / Anthropic IPO 后披露 / Kimi 游戏方案多步骤工作流），**每日更新从未中断**。

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
- 🎉 [Day 153 — 矩阵中的最长递增路径 / Week 23 收官混合模拟面试（LC 329 · 记忆化搜索 = 回归测试最小覆盖集）](week23/2026-11-04-week23-finale-longest-increasing-path.md)（**记忆化 DFS**（严格递增天然无环=不需要 visited·memo 表=回归测试最小覆盖集[不重测/不漏测/每状态一条]·O(mn)）·双解收官：Kahn 剥层=Day 152 的第二种解法·进阶：非严格版 Tarjan 缩点 / 输出路径=memo-1 逆推 / 分层并行=CI 波次 / 热点：**Kimi K3.1 Pro 权重今天到期（11/4！）**（3T 全球最大开源·Terminal-Bench 65.7 vs Opus 66.4·内部测试学走路：36 处力臂修正→连续行走 11.2 秒=严格递增爬坡路径）·**Anthropic IPO 路演 T-5 天**（8950 亿算力账单·云渠道 48%·Q3 增速放缓至 45%）·**Kimi 游戏方案 11/2**（Agent 操作 Unity/UE/Blender+操作记录=审计日志产品形态）·**DeepSeek 搜索 API 11/1 计费** / 全周一张图[1579→994→1584→684→207→329]·「50-agent 代码工厂」六连问[协调三必答件/三类载体/账本树/三分类治理/评测协议先行/溯源→归因→处置→入册]·金句「设计决定系统能不能跑，记忆化决定它会不会变好」）
- 🧪 [Day 152 — 评测流水线能跑通吗 / 课程表（LC 207 · 拓扑排序 = 依赖调度 = 评测管道编排）+ 评测与红队规模化](week23/2026-11-03-evaluation-redteam-topological-sort.md)（**Kahn 拓扑排序**（入度=未满足依赖·入度 0 入队·出队次数 < V = 有环 = 管道死锁·O(V+E)·DFS 三色备选）·进阶：LC 210=CI task scheduler·关键路径=wall-clock 下限·DFS 栈=deadlock detector·加权版=ETA 估计器 / 热点：**基准污染危机总爆发**（LiveCodeBench/SWE-bench Live·date-windowed evaluation·"Read the cutoff, or read fiction"）·**回归检测≠覆盖度**（MDPI 10/9·行为维度覆盖 > 输入覆盖）·**SWE-bench Verified 10 月榜单**（10/28·116 模型·scaffold 方差 10-15 分）·**模型升级隐性回归**（总分 +5 但 StepEfficiency -12）·**Meta-Harness**（trace 50.0 vs scores-only 34.6）·**S-1 路演倒计时**（11/9 路演·11/16 IPO·6% 蒸馏收入=红队财务版） / METR 行为归因五步·红队四件套（自动化红队/行为回归集/投毒样本库/对抗进化）·数据集版本化五军规·评测诚实性四纪律·金句「评测系统最大的谎言是『总分涨了』」）
- 🛡️ [Day 151 — 信任图里的冗余边 / 群体容错与拜占庭行为（LC 684 · 并查集 = 信任图环检测 = collusion 定位器）](week23/2026-11-02-group-fault-tolerance-byzantine-union-find.md)（**并查集环检测**（树=信任网络最小安全形态·find(u)==find(v)⟹成环·路径压缩+按秩合并·O(n·α(n))·摊还）·进阶：LC 685 有向版·反馈边集 NP-hard·签名的边才允许 union / 热点：**2026 agent 安全三连复盘**（arXiv 2610.12463v1·10/30）·**S-1 失控 agent 风险进 SEC**（财新 10/31·80 页·11/16 IPO）·**DeepSeek 自主 Super-Agent**（11/1）·**UBS 12,700 员工 agent**（1%=127 个坏节点） / 故障分类学三档·f<n/3 口试推导·METR 全案五步·混沌四注入·金句「当 f 个 agent 说谎时，剩下的 n−f 个还能不能给出正确答案」）
- 💸 [Day 150 — 任务联盟的最小通信成本 / 多 agent 预算与结算（LC 1584 · Prim MST）+ token 经济学](week23/2026-11-01-multi-agent-budget-settlement-mst.md)（**Prim 堆优化**（minDist 松弛+lazy deletion·cut property·Prim=联盟扩张 vs Kruskal=市场结算·O(n²log n)）·进阶：Steiner NP-hard 平面 PTAS/删最重 k-1 条边=任务分包/MST=最小瓶颈树/边权=价目表相对结构变才重算 / 热点：**Anthropic S-1 的 80.2% 按 token 计量计费（财新 10/31 封面）**（H1 $162.86 亿·按量 $130.64 亿·Claude Code top 5%=20 倍·NDR 900%·RRR 新指标·11/16 当周 IPO·ANT·$1.5-2 万亿）·**2026 计费大迁移**（GitHub Copilot 6/1 AI Credits·OpenAI 8 月 Enterprise 转 token·Meta WhatsApp 8/1）·**x402 基金会 10 月新章**（10/1 首任执行董事·57 成员·AWS Bedrock AgentCore per-session 上限·Visa TAP 三件套） / 账本树三原则（task_id 五元组·管理税归集·消息按边记账）·签名前四元组+签名后三件套（idempotency key·三方对账先外后内）·燃烧顺序口诀（沉没→临期积分→缓存 1/20→旗舰→批处理）·S-1 五启示·成本失控排查五步·金句「预算是产品，水位是设计」）

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

当前进度：**Week 23 收官完成 🎉 / Day 153**（Week 23 Day 6 收官日：Multi-Agent 工程实战串讲收官。Day 153 课程表 = LC 329 矩阵中的最长递增路径（记忆化搜索 = 回归测试最小覆盖集：严格递增天然无环=不需要 visited·memo[i][j]=从状态出发的最长连续改进链·memo 表三性质=不重测/不漏测/每状态一条·O(mn)·DAG 最长路径通用四步模板=状态→转移→memo→全图取 max·Kahn 剥层双解=Day 152 的第二种解法·非严格版要先 Tarjan 缩点·输出路径=memo-1 逆推·分层并行=CI 波次调度）·面试技巧：全周一张图（148 协调 LC 1579 双并查集 → 149 消息 LC 994 多源 BFS → 150 预算 LC 1584 Prim MST → 151 容错 LC 684 并查集环检测 → 152 评测 LC 207 Kahn → 153 收官 LC 329 记忆化：协议管说话/账本管花钱/容错管使坏/评测管证明/记忆化管别坏回去）·混合模拟面试「50-agent 代码工厂」六连问（Q1 协调：supervisor 三必答件·Q2 消息：三类信息三种载体·Q3 预算：账本树+给任务拨预算·Q4 容错：三分类治理·Q5 评测：协议先行·Q6 收口 P0：溯源→画像→归因→处置→入册）·高频速答六连·热点：Kimi K3.1 Pro 完整权重 11/4 到期（3T 全球最大开源·学走路案例=严格递增路径）·Anthropic IPO 路演 T-5 天（8950 亿算力账单·云渠道 48%）·Kimi 游戏方案 11/2（Agent 操作 Unity/UE/Blender）·DeepSeek 搜索 API 11/1 计费·金句「设计决定系统能不能跑，记忆化决定它会不会变好」；自检清单 10 项；**Week 24「长时程 Agent 工程」启动：状态持久化与 checkpoint / 心跳与健康检查 / 可观测性 / 容错分类与自动恢复 / 混沌测试与故障注入**）

**更新记录**：已连续更新 **153** 天

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
