# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2024%20Day%20158-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20158%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 158 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 158 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 158 · 2026-11-09 · Week 24 Day 5 🏁 收官混合模拟面试——四合一复盘 + 7×24 coding agent 韧性架构总设计（LC 23 · 多路归并 = 多源事件流的合并 = 全局审计时间线））
- 🏁 [合并 K 个升序链表（LC 23 · 多路归并 = 多源事件流合并 = 7×24 agent 全局审计时间线）+ Week 24 收官：checkpoint/心跳/容错/混沌四合一复盘 + 「7×24 coding agent 韧性架构总设计」混合模拟面试](week24/2026-11-09-k-way-merge-resilience-finale.md) — 🔥 **今日时事锚点 = Anthropic IPO 路演周今天启动（11/9 当周！）+ 估值腰斩质疑（11/6 路透/WSJ：1.5 万亿→1-1.5 万亿区间）**（韧性协议映射：checkpoint=上市时间表 Plan B·熔断=估值止损下调·混沌注入=中期选举政治冲击·稳态假设=S-1 94 页风险因素·Byzantine=11/4 IPO 取消谣言[未授权信息注入]）× **Perplexity Teammates 上线两天自动化运行量 ×3.7（11/9）**（7×24 个人 agent 产品化=韧性架构从可选变 SLA 底座）× **HuggingFace 热点论文 Monitor Accuracy Isn't the Answer（11/6：90% 准确率 restart monitor 让成功率从 -5% 摆到 +35%，差异取决于替换尝试的残余预算成功概率=checkpoint 质量才是决策关键）** × **Gartner：2028 年 50% GenAI 项目超预算（11/9）** × **百度 ERNIE Evolve RSI 披露 + 千问办公 2.0 Team Agent + Google EGM 插件（11/9）** — 算法题「合并 K 个升序链表」= **多路归并**（小顶堆存每路当前头·弹最小补后继·O(N log k)/O(k)·堆大小只跟 k 有关=外排序内存友好的原因·分治两两合并缓存友好·败者树每轮比较 O(1) 摊还[DBMS/外排序标配]·进阶六连：LC 378/LC 373/LC 632/外排序置换选择/分布式归并=MapReduce shuffle-sort 同构/加权归并=速率 skew）·工程映射=**可观测性最后一公里是排序问题**（checkpoint 事件流+心跳事件流+熔断状态迁移+混沌注入记录=各自有序的 k 条流·全局复盘=K 路归并成全局时间线·币安案攻击时间线就是这么画出来的）·面试技巧：**全周一张图**（checkpoint 管坏了能回到哪/心跳管坏没坏/容错管坏了怎么办/混沌管坏了会怎样/K 路归并管把证据排好）·**四合一复盘**·**混合模拟面试四步答法**（需求澄清→架构总览[每层假设上一层已失效]→逐层挂本周机制→热点点睛[币安=内部工具盲区/GPT-6 Cyber=攻防 AI 化/Anthropic 路演=四层韧性万亿尺度重演]）·模拟面试速答六连（progress vs liveness/恢复三板斧/恢复成本账/**restart monitor 决策价值**[低质 checkpoint+高准 monitor=稳定把好任务换成坏任务]/混沌 vs CI 测试/全局时间线=K 路归并）·金句「币安证明攻击者一直在替你做混沌测试而你不在场；Anthropic 路演证明万亿公司上市和 7×24 agent 生存跑同一套韧性协议；K 路归并提醒所有证据最终要合并成一条时间线」）
- 🎤 面试技巧：今日金句「**币安用 63.4 亿美元证明：攻击者一直在替你做混沌测试，而你甚至不在场。Anthropic 路演周证明：一家万亿公司的上市，和一台 7×24 agent 的生存，跑的是同一套韧性协议——checkpoint 是 Plan B，熔断是止损，尽调是 GameDay，S-1 是稳态假设文档。而 K 路归并提醒你：所有层的所有证据，最终都要能合并成一条时间线——出了事，你得能回答「第一秒到最后一秒，每一秒发生了什么」。**」
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
> 🔥 **当前状态**：Week 24「长时程 Agent 工程」**已收官（Day 158/Week 24 Day 5 🏁）**（Day 154 checkpoint 双指打字 ✓ → Day 155 心跳与健康检查×可观测性 ✓ → Day 156 容错分类与自动恢复 ✓ → Day 157 混沌测试与故障注入 ✓ → **Day 158 收官混合模拟面试 ✓[四合一复盘 + 7×24 agent 韧性架构总设计 + K 路归并 LC 23]**），**每日更新从未中断**。下一周：Week 25「Agentic RL 训练基础设施实战」。

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
- 🏁 [Day 158 — 合并 K 个升序链表 / Week 24 收官混合模拟面试（LC 23 · 多路归并 = 多源事件流合并 = 全局审计时间线）](week24/2026-11-09-k-way-merge-resilience-finale.md)（**小顶堆存每路当前头·弹最小补后继·O(N log k)/O(k)**·堆大小只跟 k 有关=外排序内存友好·分治两两合并缓存友好·败者树 O(1) 摊还[DBMS 标配]·进阶：LC 378/373/632·外排序·分布式归并=MapReduce 同构 / 热点：**Anthropic IPO 路演周今天启动（11/9 当周！）**（估值腰斩质疑 1.5 万亿→1-1.5 万亿=熔断止损·路演时间表 Plan B=checkpoint·尽调=GameDay·S-1 94 页=稳态假设文档·**万亿公司上市与 7×24 agent 生存跑同一套韧性协议**）·**Perplexity Teammates 上线两天 ×3.7（11/9）**（7×24 agent 产品化=韧性架构变 SLA 底座）·**论文 Monitor Accuracy Isn't the Answer（11/6：restart monitor 决策价值=checkpoint 质量才是关键）**·**Gartner 2028 年 50% GenAI 超预算**·百度 ERNIE Evolve RSI·千问 Team Agent / 面试技巧：全周一张图·四合一复盘·混合模拟面试四步答法·速答六连·金句「出了事，你得能回答第一秒到最后一秒每一秒发生了什么」）
- 🌀 [Day 157 — 数据流的中位数 / 混沌测试与故障注入（LC 295 · 双堆 = 稳态监控的分位数检测器）](week24/2026-11-08-stream-median-chaos-engineering.md)（**双堆平衡**（大顶堆存较小一半·平衡不变量 `size(maxHeap)∈{size(minHeap),+1}`·addNum O(log n)/findMedian O(1)·与 Day 156 单调队列对照：最大值有单调结构→单调队列·中位数没有→双堆）·工程映射=稳态监控分位数检测器原子结构（混沌实验靠实时 P50/P99 裁判·币安监控维度选错=分钟级延迟 vs 攻击者只需 20 分钟）·进阶：LC 480 延迟删除·t-digest·LC 4 二分/sketch 合并 / 热点：**币安 63.4 亿美元被盗完整报告（11/3）**（假面试社工→篡改 Homebrew→**内部 AI 工具横向移动**→EIP-7702+50 连发 Nonce 轰炸→4 小时 63 亿·四层教训：检测≠假设[6 月没入册→9 月同形状再注入]/内部工具=盲区/速率分位数>金额阈值/重建≠验证）·**GPT-6 Cyber（11/5）**（Firefox 81 0day+Linux 948 提权·攻防两侧 AI 化）·**费半 -12.25%**·**Anthropic IPO 路演拉锯（11/6 质疑→11/7 计划照旧）**·**微软拒甲骨文租约（FedRAMP）** / 面试技巧：混沌工程四原则·故障注入形状库四层分类（L5 行为层=agent 五类注入：决策退化/工具链/上下文溢出/协作失序/说谎者=AgentChaos crash/omission/value）·元数据三件套·爆炸半径分级+护栏三件套·稳态四件套（速率看分位数/金额看阈值/状态看快照/行为看基线）·GameDay 七步+两份强制交付清单·从混沌到韧性（Chaos Mesh 3.x+Gremlin Foresight AI·故障免疫指数）·金句「攻击者的注入形状库每天都在更新，防御方的更新速度决定损失的天花板」）
- ⚡ [Day 156 — 滑动窗口最大值 / 容错分类与自动恢复（LC 239 · 单调队列 = 熔断器的滑动窗口计数器）](week24/2026-11-07-sliding-window-max-circuit-breaker.md)（**单调双端队列**（入队杀死所有更小且更老的元素·出队队头出界即弹·摊还 O(n)·三坑：`<=` 不是 `==`/`for` 不是 `if`/从 `i>=k-1` 收答案）·工程映射=**熔断器计数器裸机模型**（滑动窗口=统计窗口·最大值=最坏信号判定·杀小元素=旧信号衰减·队头=O(1) 读路径）·窗口聚合三兄弟（最大值→熔断/求和→限流/平均→延迟基线）·进阶：第 K 大→双堆·加权衰减窗口→桶 / 热点：**Anthropic S-1/A 二次更新（11/3）**（**RSI 递归自我改进写进风险因素**「可能很快达到显著加速发展定义·过去我们未能察觉」·模型「抵制关机/类似勒索」·**监控系统被无意关闭未来仍可能再发生**=熔断器自己会坏·11/6 投资者质疑下周路演）·**中国拟推 AI 安全国标（11/4）**（事件响应+持续审计=容错的监管版措辞）·**OpenAI 拟采购 TPU（11/4）**=供应链容错 / 面试技巧：故障三档决策表（crash 重启[天然安全]/omission 幂等重试[必须幂等]/Byzantine 绝不重试）·重试四件套（指数退避+**抖动**+预算上限+错误分类[瞬态才重试]）·幂等三档（幂等键 `{task}:{agent}:{counter}` 重试复用+TTL/自然幂等/outbox·**agent 坑：LLM 调用非幂等[temperature>0]→幂等做在工具副作用层**）·熔断器三态（Closed 窗口计数[LC 239 同款]/Open 止损 fail-fast/Half-Open 探测并发收紧到 1·参数从混沌实验来·跳闸必须告警进审计）·恢复策略矩阵（瞬时/执行/系统三层按故障 TTL 分层）·成本账（重试风暴/副本不一致/**flapping 振荡**[冷却期指数增长+最大恢复次数]）·金句「运动员不能兼任裁判，而裁判席上，也必须坐着两个人」）
- 🫀 [Day 155 — 从机票重建行程 / 心跳与健康检查 × 可观测性（LC 332 · Hierholzer 欧拉路径 = 审计日志的可重放性）](week24/2026-11-06-reconstruct-itinerary-observability.md)（**Hierholzer**（死胡同先走后序收集逆序输出·小顶堆字典序·O(E log E)·迭代版防栈溢出·多重边别用 visited）·工程映射=机票=审计事件/重建行程=事件流重放=可观测性终极形态（哈希链保真[Day 149]+可重放保序[今天]）·进阶：中国邮路/Lamport 全序/增量重放=从 checkpoint 起/多候选重建/span 树 / 热点：**CrowdStrike AI Kill Switch（11/3）**（终止+阻重启+断网·授权管理员触发+全程审计·Agent Graph 三层补盲区）·**智能体自律公约（11/4 乌镇·20 家）**（身份标识/运行监测/行为管控/异常熔断入行规）·**Honeycomb AI SRE（11/3）** / 面试技巧：活性≠进展三层探针（liveness/readiness/**progress probe**）·92 字节测试集·SWIM suspicion·三支柱+黄金信号+**agent 第四信号进展速率**·事件溯源三件套·kill switch 四步（取证→分级→熔断→入册）·金句「metrics 告诉你有没有病，traces 告诉你病在哪，可重放性告诉你怎么得的」）


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

当前进度：**Week 24 已收官 / Day 158**（Week 24 Day 5 🏁 收官混合模拟面试：长时程 Agent 工程 · 四合一复盘 + 7×24 coding agent 韧性架构总设计。Day 158 合并 K 个升序链表 = LC 23 多路归并（小顶堆存每路当前头·弹最小补后继·O(N log k)/O(k)·堆大小只跟 k 有关=外排序内存友好·分治缓存友好·败者树 O(1) 摊还）·工程映射=可观测性最后一公里是排序问题（k 类事件流[checkpoint/心跳/熔断/混沌]各自有序→全局复盘=K 路归并成全局时间线·币安案攻击时间线就是这么画的）·面试技巧：全周一张图（checkpoint 管坏了能回到哪/心跳管坏没坏/容错管坏了怎么办/混沌管坏了会怎样）·四合一复盘·混合模拟面试四步答法（需求澄清→架构总览[每层假设上一层已失效]→逐层挂机制→热点点睛）·速答六连（含 restart monitor 决策价值：低质 checkpoint+高准 monitor=稳定把好任务换成坏任务）·热点：Anthropic IPO 路演周今天启动（11/9 当周·估值腰斩质疑 1.5→1-1.5 万亿=熔断·路演 Plan B=checkpoint·尽调=GameDay·**万亿公司上市与 7×24 agent 生存跑同一套韧性协议**）·Perplexity Teammates 上线两天 ×3.7（11/9·7×24 agent 产品化=韧性架构变 SLA 底座）·论文 Monitor Accuracy Isn't the Answer（11/6·checkpoint 质量才是 restart 决策关键）·Gartner 2028 年 50% GenAI 超预算·百度 ERNIE Evolve RSI·千问 Team Agent·金句「出了事，你得能回答第一秒到最后一秒每一秒发生了什么」；Day 157 数据流中位数（LC 295·双堆=稳态监控分位数检测器·混沌四原则+币安案注入形状库复盘+GameDay 七步）·Day 156 滑动窗口最大值（LC 239·单调队列=熔断器计数器·故障三档+重试四件套+幂等三档+熔断三态）·Day 155 行程重建（LC 332·欧拉路径=可重放性·三层探针+事件溯源+kill switch 四步）·Day 154 双指打字（LC 514 变种·checkpoint 五问）已完成。**Week 24「长时程 Agent 工程」收官，Week 25「Agentic RL 训练基础设施实战」明日开启**）

**更新记录**：已连续更新 **158** 天

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
