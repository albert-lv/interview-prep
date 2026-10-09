# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2019%20Day%20127%20🎭-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20127%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 127 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 127 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 127 · 2026-10-09 · Week 19 Day 6 🎭）
- 🧠 [分割数组的最大值 + 多智能体编排四种模式](week19/2026-10-09-split-array-multi-agent-orchestration.md) — 今日算法题「分割数组的最大值」（LC 410）= **多智能体负载均衡的算法原型**：数组元素=任务时长·分成 k 个连续子数组=任务分给 k 个 worker·最小化最大子数组和=最小化最慢 worker 完工时间（makespan minimization）。核心套路「最小化最大值 + 可行性单调 = 二分答案」：feasible(limit)=能否切成 ≤k 段（**至多是关键松弛**）每段和 ≤limit，下界 max(nums) 上界 sum(nums)，O(n·log S)；贪心正确性反证（前缀覆盖论证：贪心每段结束位置不早于任何合法划分，段数只会更少）；连环问：LPT 最长处理时间优先（近似比 4/3−1/(3m)，P||Cmax 是 NP-hard）·依赖 DAG→List Scheduling 关键路径优先·异构 worker→加权 limit=k8s 打分模型·二分四件套（LC 875/1482/1011）。面试技巧**多智能体编排四种模式总表**（主管-工人=科层制/流水线=车间/辩论投票=评审会/市场拍卖=竞标场）：①主管-工人（星型拓扑·验收标准先于派发·层级 ≤2 红线·委派三件套=目标+约束+验收·致命伤=中心 context 膨胀）②流水线（阶段靠 schema 契约连接非自然语言·级联误差=stage 入口校验打回·换 agent 唯一理由=脑回路差异大）③辩论投票（Self-Consistency 组织版·**回声室=同底模型换人设不叫多样性，真多样性=异构数据源/工具/验证器**·裁决三档=多数投票/judge agent/历史胜率加权·必须有外部锚防 Kamoi 式掉点）④市场拍卖（静态分配失衡时用·出价必须含成本信号·二价拍卖 Vickrey 促真实披露·流拍任务兜底）；先答"为什么不是单 agent"（三买三税：上下文隔离/并行提速/认知多样性 ↔ 通信成本/协调开销/误差传播）；通信三板斧（消息传递/黑板共享·写冲突=单 writer 或 LWW/发布订阅）；六失效模式速查（循环委托/中心过载/回声室/级联误差/成本失控/死锁互等各配缓解）；框架速览（LangGraph=图/AutoGen=对话/CrewAI=角色/Swarm=路由·选型四问=可观测性/失败隔离/成本熔断/状态管理）；veRL 连接（opponent pool 防坍缩·partial rollout 任务切分=今日负载均衡题）；金句「多智能体编排的本质是组织设计——你不是在写代码，你是在当 CEO 设计汇报线；communication structure follows task structure」
- 🎤 面试技巧：今日金句「**所有组织设计的铁律在 agent 系统同样成立：先想清楚任务怎么分，再决定谁跟谁说话；而任务分不均，加多少 agent 都是白搭——10 个 worker 的完成时间取决于最慢的那个**」

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
> 🔥 **当前状态**：Week 19 已开启 🎭（Agentic 系统与智能体工程，Day 122 打开转盘锁 + 总览日 ✅），Week 18 已完结，**每日更新从未中断**。

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
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- 🎭 [Day 127 — 分割数组的最大值（Split Array Largest Sum）+ 多智能体编排四种模式](week19/2026-10-09-split-array-multi-agent-orchestration.md)（**多智能体负载均衡算法原型**：元素=任务时长·k 段=k worker·最小化最大子数组和=makespan / 二分答案四件套：下界 max·上界 sum·feasible=≤k 段（至多松弛）·O(n·log S) / 贪心正确性=前缀覆盖反证·「能装就装」 / 连环问：LPT 4/3 近似比·List Scheduling 关键路径·异构加权=k8s 打分·LC 875/1482/1011 / **四模式总表**：主管-工人（星型·验收先于派发·层级 ≤2·委派三件套）·流水线（schema 契约·级联误差 stage 入口校验·换 agent=脑回路差异）·辩论投票（回声室=换人设不叫多样性·真多样性=异构数据源·外部锚）·市场拍卖（成本信号·Vickrey 二价·流拍兜底）/ 三买三税·通信三板斧·六失效速查 / veRL 连接：opponent pool·partial rollout 切分=今日题 / 金句「编排=组织设计·先想任务怎么分再想谁跟谁说话」）
- 🎭 [Day 126 — LFU 缓存（LFU Cache）+ 记忆系统三层设计与失效降级](week19/2026-10-08-lfu-cache-memory-systems.md)（**缓存即记忆：eviction 策略即遗忘曲线**——capacity=工作记忆上限·freq=记忆强度（get=提取练习）·minFreq 淘汰=遗忘执行器·TTL=时效衰减 / 三件套 O(1)：key→节点·freq→双向链表（同频 LRU 序）·minFreq 指针（LRU+频率分桶=LFU）/ 易错：put 已存在不淘汰·新 key 后 min_freq=1·get miss 不动 minFreq / 连环问：LRU 怕污染 vs LFU 怕冷启动→**aging freq 减半=记忆消退**·LRU-K·ARC·W-TinyLFU·TTL 惰性+定期·分布式 Gossip 同步计数器 / **记忆三层模型**：工作=context·情景=向量库（90% 工程在这）·语义=权重·程序性=skill / 四决策：写入评分+纠错记忆最贵·**冲突更新而非追加**·触发式检索+阈值生命线·离线巩固·遗忘三件套 / **MemGPT 虚拟分页**：LLM=CPU·函数调用=缺页中断·换入时机/换出策略/地址翻译 / **降级阶梯**：向量挂→BM25→纯工作记忆·六失效模式（空召回显式标注防幻觉·陈旧 TTL·**记忆中毒=注入持久化载体**·膨胀→aging·污染→rerank）/ 金句「记不住是残疾，忘不了是噩梦」）
- 🎭 [Day 125 — K 站中转内最便宜的航班（Cheapest Flights Within K Stops）+ Tool Use 工程、MCP 协议与 Computer Use](week19/2026-10-07-cheapest-flights-k-stops-tool-use-mcp.md)（**预算约束下的工具调用路径规划微缩模型**：节点=进度·边=工具调用·k 次中转=max_steps 预算 / 分层 Bellman-Ford：k+1 轮松弛=中转约束算法化身·backup 数组防一条边一轮用两次 / 二维状态 Dijkstra：`(node,stops)` 双维状态·堆顶即全局最优·SPFA 活跃队列 / 连环问：k≥n-2 退化·负权回 BF+负环（套利环限步）·多源虚拟源·必经工具分段最短路·二维预算 / **tool orchestration = (进度, 剩余步数) 状态图最短路** / Tool Use：schema 是写给模型的 prompt·负面约束防误调·20+ 工具→工具路由=RAG for tools·三层容错·危险确认门 / MCP：N×M→N+M·三角色·**Tools=动词·Resources=名词·Prompts=文档**·生命周期五步·**MCP vs FC=语言 vs 插座**·tool poisoning / Computer Use：四挑战·OSWorld 22%→40%+·**有 API 永远优先 API**·三板斧 / 金句「接口多像人话·标准插座·最后一双眼睛是屏幕」）
- 🎭 [Day 124 — 最小覆盖子串（Minimum Window Substring）+ Context Engineering 与长上下文管理](week19/2026-10-06-minimum-window-substring-context-engineering.md)（**窗口只增不减·compaction 合法性下界=任务 coverage（最小覆盖原则）** / need·cnt·missing 三件套：missing 数种类不数次数=防重复字符污染·合法性判定 O(1) / 贪心正确性=右端点固定短窗不劣·数据流在线版·丢字符有代价=约束最优化（压缩成本进账本） / **context 是 agent 的新显存** / context rot 四大机制：指令冲突·Lost in the Middle·error compounding 语境版·信噪比稀释 / 成本结构：输入 token 是乘数·tool schema 隐形大户·多智能体乘法爆炸 / **四策略总表**：截断（失忆）·滑动窗口（同构最小覆盖）·摘要压缩（有损+幻觉）·结构化压缩（tool result elision）·生产=叠加态（/compact+滑窗兜底+CLAUDE.md 重建） / **外部化三件套**：sub-agent 打包只回传结论=最小覆盖工程版·文件外置（随机访问换线性存储）·滚动 state.md（append-only→read-modify-write） / prompt caching 账本：前缀严格一致·schema 稳定化·KV 省显存 vs caching 省钱包 / RAG vs 长窗口三问判据·裁剪口诀「丢了能否低成本重新拿到」·单轮四账相加（70%+ 是 tool output=管控杠杆） / 金句「Truncation 是失忆，summarization 是听说，structured compaction 是整理，sub-agent 是让别人去记」）
> 📅 **每天 20:42 自动更新**,[查看全部历史 →](https://github.com/albert-lv/interview-prep/commits/main)

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

当前进度：**Week 19 进行中 🎭 / Day 127**（Week 19 主题：Agentic 系统与智能体工程 🎭 — Day 127 多智能体编排四种模式日：LC 410 分割数组的最大值 = 多智能体负载均衡算法原型——数组元素=任务时长、分成 k 个连续子数组=任务分给 k 个 worker、最小化最大子数组和=最小化最慢 worker 完工时间（makespan minimization）；核心套路「最小化最大值+可行性单调=二分答案」：feasible(limit)=能否切成 ≤k 段每段和 ≤limit（至多是关键松弛）、下界 max(nums) 上界 sum(nums)、O(n·log S)，贪心正确性=前缀覆盖反证（贪心每段结束位置不早于任何合法划分，段数只会更少）；连环问：LPT 最长处理时间优先（近似比 4/3−1/(3m)，P||Cmax 是 NP-hard）·依赖 DAG→List Scheduling 关键路径优先·异构 worker→加权 limit（k8s 打分模型）·二分四件套（LC 875/1482/1011）。面试技巧：**四种编排模式总表**（主管-工人=科层制/流水线=车间/辩论投票=评审会/市场拍卖=竞标场）——①主管-工人：星型拓扑·验收标准先于派发·层级 ≤2 红线·委派三件套=目标+约束+验收·致命伤=中心 context 膨胀（结论回传+结构化汇报）；②流水线：阶段间靠 schema 契约连接非自然语言·级联误差=stage 入口校验+打回重出·换 agent 的唯一理由=脑回路差异大；③辩论投票：Self-Consistency 的组织版·**回声室=同底模型换人设不叫多样性，真多样性=异构数据源/工具/验证器**·裁决三档=多数投票/judge agent/历史胜率加权·必须有外部锚（执行/检索）防 Kamoi 式掉点；④市场拍卖：静态分配失衡时用·出价必须含成本信号·二价拍卖 Vickrey 促真实披露·流拍任务兜底；**先答"为什么不是单 agent"**（三买三税：上下文隔离/并行提速/认知多样性 ↔ 通信成本/协调开销/误差传播）；通信三板斧：消息传递/黑板共享（写冲突=单 writer 或 LWW）/发布订阅；六失效模式速查表（循环委托→委派深度计数+每跳产出工件·中心过载→结论回传·回声室→异构验证器·级联误差→stage 校验·成本失控→全局预算熔断·死锁互等→超时缺省推进）；框架速览（LangGraph=图/AutoGen=对话/CrewAI=角色/Swarm=路由·选型四问=可观测性/失败隔离/成本熔断/状态管理）；veRL 连接（opponent pool 防坍缩·partial rollout 任务切分=今日负载均衡题·群体 reward 归因=信用分配社交版开放问题）；金句「多智能体编排的本质是组织设计——你不是在写代码，你是在当 CEO 设计汇报线；communication structure follows task structure」🎭）

**更新记录**：已连续更新 **127** 天，每日 20:42 自动推送。

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
