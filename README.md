# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2024%20Day%20155-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20155%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 155 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 155 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 155 · 2026-11-06 · Week 24 Day 2 长时程 Agent 工程：心跳与健康检查 × 可观测性——活性≠进展三层探针 + 事件溯源可重放性（LC 332 · Hierholzer 欧拉路径 = 审计日志的可重放性））
- 🫀 [从机票重建行程（LC 332 · Hierholzer 欧拉路径 = 审计日志的可重放性）+ 心跳与健康检查：活性 ≠ 进展 + 可观测性三支柱与分层盲区](week24/2026-11-06-reconstruct-itinerary-observability.md) — 🔥 **今日时事锚点 = CrowdStrike AI Kill Switch（11/3 Fal.Con 欧洲发布·11/4 全球刷屏）**（就地终止异常 agent+阻止重启+断企业网三件套；Kurtz「大家都在开发 AI 智能体，却一直没有关闭它们的按钮，现在有了」；同日 4.95 亿美元收购 Fiddler AI 等两家；**触发必须授权管理员+全程审计跟踪**；Agent Graph 三层补盲区=agent 钩子看得见 prompt 漏模型响应+网络层看不见主机执行→**单层观测必然有盲区**）× **《智能体发展自律公约》（11/4 乌镇·信通院+华为+腾讯+阿里+小米+百度+360 等 20 家·八条）**（分层次可验证身份标识[tracing 前提]+**运行监测/行为管控/异常熔断写入行规**+用户最终控制权进条款）× **Honeycomb AI SRE agent（11/3 早鸟）**（高基数遥测目标导向调查·可观测性平台→AI 查案员） — 算法题「重新安排行程」= **Hierholzer 欧拉路径**（死胡同先走：走入出度耗尽的节点就出不来→必为最后一段→后序收集逆序输出·小顶堆字典序贪心·O(E log E)/O(E)·迭代版防栈溢出·多重边不能用 visited）·工程映射=**每张机票=一条审计事件，重建行程=从 append-only 事件日志重放完整执行轨迹**（哈希链保不被篡改[Day 149]+可重放保能还原[今天]=事件溯源两支柱·字典序=确定性重放·增量重放=从最近 checkpoint 起重放[呼应 Day 154]）·进阶六连（无解版=中国邮路/分布式全序=Lamport 时钟/事件丢失多候选重建/加权最早到达/k 段行程=span 树）·面试技巧：**活性 vs 进展三层探针**（liveness/readiness/**progress probe**[进展事件+静默阈值+外部 Watchdog 裁决]·「基建活性≠任务进展」=DeepSeek Watchdog 产品化）·**「92 字节请求」健康检查测试集**（探针答真实业务·探针与真实成功率 diff 逼出盲区）·SWIM suspicion 回顾·可观测性三支柱+黄金信号/RED/USE+**agent 第四信号「进展速率」**·事件溯源三件套（append-only/确定性重放/快照+增量=照片和录像带都要）·kill switch 范式=**先观测后熔断+熔断本身进审计+取证现场封存+入册回归**·模拟面试三连（7×24 coding agent 健康检查/三支柱落地/异常处置四步）·金句「metrics 告诉你有没有病，traces 告诉你病在哪条链路上，可重放性告诉你这个病是怎么得的」）
- 🎤 面试技巧：今日金句「**进程活着不等于任务在动。metrics 告诉你有没有病，traces 告诉你病在哪条链路上，而可重放性告诉你这个病是怎么得的——前两者是体检报告，重放是监控录像。一个不能被重放的 agent，出了事你连它'去过哪、按什么顺序'都说不清；所以健康检查要有第三层探针盯着『进展』，把『要不要熔断』的裁决权放在系统之外的 Watchdog 手里——运动员不能兼任裁判。这周 CrowdStrike 给 agent 装上了关闭按钮，乌镇公约把『运行监测、行为管控、异常熔断』写进了行规：观测是熔断的前提，审计是熔断的边界。**」
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
> 🔥 **当前状态**：Week 24「长时程 Agent 工程」进行中 **Day 155/Week 24 Day 2**（Day 154 checkpoint 双指打字 ✓ → **Day 155 心跳与健康检查×可观测性 ✓** → 待续：容错分类与自动恢复 / 混沌测试与故障注入——CrowdStrike Kill Switch × 乌镇自律公约 × K3.1 Pro 权重开源后的长任务实测 × Anthropic IPO 11/16 后披露），**每日更新从未中断**。

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
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- 🫀 [Day 155 — 从机票重建行程 / 心跳与健康检查 × 可观测性（LC 332 · Hierholzer 欧拉路径 = 审计日志的可重放性）](week24/2026-11-06-reconstruct-itinerary-observability.md)（**Hierholzer**（死胡同先走后序收集逆序输出·小顶堆字典序·O(E log E)·迭代版防栈溢出·多重边别用 visited）·工程映射=机票=审计事件/重建行程=事件流重放=可观测性终极形态（哈希链保真[Day 149]+可重放保序[今天]）·进阶：中国邮路/Lamport 全序/增量重放=从 checkpoint 起/多候选重建/span 树 / 热点：**CrowdStrike AI Kill Switch（11/3）**（终止+阻重启+断网·授权管理员触发+全程审计·Agent Graph 三层补盲区）·**智能体自律公约（11/4 乌镇·20 家）**（身份标识/运行监测/行为管控/异常熔断入行规）·**Honeycomb AI SRE（11/3）** / 面试技巧：活性≠进展三层探针（liveness/readiness/**progress probe**）·92 字节测试集·SWIM suspicion·三支柱+黄金信号+**agent 第四信号进展速率**·事件溯源三件套·kill switch 四步（取证→分级→熔断→入册）·金句「metrics 告诉你有没有病，traces 告诉你病在哪，可重放性告诉你怎么得的」）
- 💾 [Day 154 — 双指打字的最小迁移成本 / 状态持久化与 checkpoint（LC 514 变种 · 双指状态机 + 环形距离 + O(nm) 滚动 DP）](week24/2026-11-05-free-two-fingers-checkpoint-dp.md)（**双指状态机 DP**（状态=(f1,f2,next) 滚动·环形距离 min(d,26-d)·初始免费=零状态恢复·换手指=任务切换=Markov）·影子手指=完整上下文 checkpoint / 热点：**DeepSeek Super-agent 架构 10/31 开源**（存算分离·Watchdog 外部控制回路=基建活性≠任务进展·触发批处理+37% token·p95 29.5→15.7min）·Kimi Hosted Agents 7×24·Qoder 乌镇获奖 / checkpoint 五问（存什么/粒度/恢复/版本/成本）·恢复三板斧·金句「checkpoint 是保险不是仪式」）
- 🎉 [Day 153 — 矩阵中的最长递增路径 / Week 23 收官混合模拟面试（LC 329 · 记忆化搜索 = 回归测试最小覆盖集）](week23/2026-11-04-week23-finale-longest-increasing-path.md)（**记忆化 DFS**（严格递增天然无环=不需要 visited·memo 表=回归测试最小覆盖集[不重测/不漏测/每状态一条]·O(mn)）·双解收官：Kahn 剥层=Day 152 的第二种解法·进阶：非严格版 Tarjan 缩点 / 输出路径=memo-1 逆推 / 分层并行=CI 波次 / 热点：**Kimi K3.1 Pro 权重今天到期（11/4！）**（3T 全球最大开源·Terminal-Bench 65.7 vs Opus 66.4·内部测试学走路：36 处力臂修正→连续行走 11.2 秒=严格递增爬坡路径）·**Anthropic IPO 路演 T-5 天**（8950 亿算力账单·云渠道 48%·Q3 增速放缓至 45%）·**Kimi 游戏方案 11/2**（Agent 操作 Unity/UE/Blender+操作记录=审计日志产品形态）·**DeepSeek 搜索 API 11/1 计费** / 全周一张图[1579→994→1584→684→207→329]·「50-agent 代码工厂」六连问[协调三必答件/三类载体/账本树/三分类治理/评测协议先行/溯源→归因→处置→入册]·金句「设计决定系统能不能跑，记忆化决定它会不会变好」）
- 🧪 [Day 152 — 评测流水线能跑通吗 / 课程表（LC 207 · 拓扑排序 = 依赖调度 = 评测管道编排）+ 评测与红队规模化](week23/2026-11-03-evaluation-redteam-topological-sort.md)（**Kahn 拓扑排序**（入度=未满足依赖·入度 0 入队·出队次数 < V = 有环 = 管道死锁·O(V+E)·DFS 三色备选）·进阶：LC 210=CI task scheduler·关键路径=wall-clock 下限·DFS 栈=deadlock detector·加权版=ETA 估计器 / 热点：**基准污染危机总爆发**（LiveCodeBench/SWE-bench Live·date-windowed evaluation·"Read the cutoff, or read fiction"）·**回归检测≠覆盖度**（MDPI 10/9·行为维度覆盖 > 输入覆盖）·**SWE-bench Verified 10 月榜单**（10/28·116 模型·scaffold 方差 10-15 分）·**模型升级隐性回归**（总分 +5 但 StepEfficiency -12）·**Meta-Harness**（trace 50.0 vs scores-only 34.6）·**S-1 路演倒计时**（11/9 路演·11/16 IPO·6% 蒸馏收入=红队财务版） / METR 行为归因五步·红队四件套（自动化红队/行为回归集/投毒样本库/对抗进化）·数据集版本化五军规·评测诚实性四纪律·金句「评测系统最大的谎言是『总分涨了』」）

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

当前进度：**Week 24 进行中 / Day 155**（Week 24 Day 2：长时程 Agent 工程 · 心跳与健康检查 × 可观测性。Day 155 重新安排行程 = LC 332 Hierholzer 欧拉路径（死胡同先走后序收集逆序输出·小顶堆字典序贪心·O(E log E)·迭代版防栈溢出·多重边不能用 visited）·工程映射=每张机票=一条审计事件，重建行程=从 append-only 事件日志重放完整执行轨迹=可观测性终极形态「可重放性」（Day 149 哈希链保不被篡改 + 今天保能还原=事件溯源两支柱）·字典序=确定性重放·增量重放=从最近 checkpoint 起重放（呼应 Day 154 快照+日志双写=照片和录像带都要）·面试技巧：活性 vs 进展三层探针（liveness/readiness/progress probe·进展事件+静默阈值 suspicion+外部 Watchdog 裁决=「基建活性≠任务进展」的 DeepSeek Watchdog 产品化）·「92 字节请求」健康检查测试集（探针答真实业务·探针与真实成功率 diff 逼出盲区）·SWIM suspicion 回顾（Day 149）·可观测性三支柱 metrics/logs/traces+黄金信号/RED/USE+agent 第四信号「进展速率」·CrowdStrike Agent Graph 分层盲区论证（agent 钩子看得见 prompt 漏模型响应+网络层看不见主机执行→单层观测必然有盲区）·事件溯源三件套（append-only/确定性重放/快照+增量）·kill switch 范式=先观测后熔断+熔断动作进审计跟踪+取证现场封存+复盘入册（CrowdStrike 授权管理员触发+全程审计）·模拟面试三连（7×24 coding agent 健康检查/三支柱落地 agent 系统/异常处置四步取证→分级→熔断→入册）·高频速答五连·自检清单 10 项·热点：CrowdStrike AI Kill Switch（11/3 Fal.Con 欧洲·就地终止+阻重启+断企业网·Kurtz「现在有了关闭按钮」·4.95 亿美元收购 Fiddler AI 等两家）·《智能体发展自律公约》（11/4 乌镇·信通院+华为+腾讯+阿里+小米+百度+360 等 20 家八条·身份标识/运行监测/行为管控/异常熔断入行规+用户最终控制权）·Honeycomb AI SRE agent（11/3·高基数遥测目标导向调查）·DeepSeek Super-agent Watchdog（10/31·活性≠进展）·金句「metrics 告诉你有没有病，traces 告诉你病在哪条链路上，可重放性告诉你这个病是怎么得的」；Day 154 checkpoint 双指打字（LC 514 变种·影子手指=checkpoint·DeepSeek 存算分离+Watchdog·checkpoint 五问）已完成。Week 24 待续：容错分类与自动恢复 / 混沌测试与故障注入）

**更新记录**：已连续更新 **155** 天

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
