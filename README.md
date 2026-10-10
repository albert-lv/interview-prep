# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2019%20Day%20128%20🎉-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20128%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 128 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 128 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 128 · 2026-10-10 · Week 19 Day 7 收官 🎉）
- 🧠 [编辑距离 + 评估与观测（SWE-bench 与轨迹评估）](week19/2026-10-10-edit-distance-evaluation-observability.md) — 今日算法题「编辑距离」（LC 72）= **轨迹差异度的算法原型**：两个字符串 = golden trace ↔ agent 实际轨迹·插入/删除/替换 = 多走一步/漏走一步/走了岔路·最小编辑代价 = 轨迹差异分。二维 DP 三转移（相等免费对齐 dp[i-1][j-1]·不等 1+min(删/插/替)）O(mn)/O(mn)·滚动数组 O(min(m,n))·滚动顺序与 0-1 背包相反（上/左/左上依赖）；连环问：插删版 = m+n−2·LCS（**git diff 的 Myers O(ND) 内核**）·对齐回溯 = diff 可视化·加权版 = DNA 打分矩阵/Wer 词错误率·10⁵ 级 DNA = Hirschberg 分治 O(min(m,n)) 空间·轨迹版要先定义动作等价性。面试技巧**评估与观测总框架**：agent 评估五大难（输出开放/路径多样/环境交互/成本变量/非确定性）；离线 benchmark 生态（**SWE-bench** = 真实 GitHub issue + FAIL_TO_PASS/PASS_TO_PASS **双测试门**·Verified 人工精选 500·Multimodal/Multilingual/Lite/Pro 演进·**污染问题**三板斧·WebArena/GAIA/τ-bench/BFCL）；**三轴评估框架**（成功率 pass@1 按难度分层 / 成本均值+P95 / 可靠性 **pass@k = 能力上限 vs pass^k = 可部署下限**·Self-Consistency 把上限折成下限）；**轨迹级评估**（终局 outcome vs 过程 process·步数效率/无效工具调用率/观察忽略率·轨迹相似度 = 今天那道题·LLM-judge on trajectory 成本与偏差）；**可观测性三支柱 agent 版**（traces 逐步落盘·**trajectory replay = eval 与 observability 的铰链**·eval harness = reward function 镜像 = agentic RL 的另一半）；**Week 19 七天串讲收官**：状态图搜索（122）→三范式（123）→context 显存（124）→工具插座（125）→记忆遗忘曲线（126）→组织设计（127）→评估闭环（128）·七剑合璧公式；金句「Demoware 与 productionware 之间隔着一条叫 observability 的河——如果你不能衡量一个 agent，你就不能改进它」
- 🎤 面试技巧：今日金句「**评估不是开发的尾声，评估是开发的另一半；离线 benchmark 给你入场券，轨迹级评估给你调试语言，线上可观测性给你改进闭环——三个都齐了，agent 才配叫工程系统**」

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
> 🔥 **当前状态**：Week 19 已完结 🎉（Agentic 系统与智能体工程 Day 122-128 全部完成 ✅——状态图搜索/三范式/Context Engineering/Tool 与 MCP/记忆系统/多智能体编排/评估与观测），**每日更新从未中断**。

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
- 🎉 [Day 128 — 编辑距离（Edit Distance）+ 评估与观测（SWE-bench 与轨迹评估）](week19/2026-10-10-edit-distance-evaluation-observability.md)（**轨迹差异度算法原型**：字符串 = golden trace ↔ 实际轨迹·编辑操作 = 轨迹偏差 / 二维 DP：相等免费对齐·不等 1+min(删/插/替)·O(mn)→滚动 O(min)·扫序与 0-1 背包相反 / 连环问：插删版=m+n−2·LCS（git diff Myers 内核）·对齐回溯·加权=DNA 打分·Wer 词错误率·Hirschberg / **评估五大难**：开放输出·路径多样·环境交互·成本变量·非确定性 / SWE-bench：真实 issue+FAIL_TO_PASS/PASS_TO_PASS 双门·Verified 500·污染三板斧 / **三轴**：成功率分难度·成本均值+P95·可靠性（pass@k 上限 vs pass^k 下限）/ 轨迹评估：终局 vs 过程·步数效率·无效动作率·轨迹相似度=今日题 / 可观测性：trace 落盘·replay 铰链·eval harness=reward 镜像 / **Week 19 七天串讲收官**·金句「不能衡量就不能改进」）
- 🎭 [Day 127 — 分割数组的最大值（Split Array Largest Sum）+ 多智能体编排四种模式](week19/2026-10-09-split-array-multi-agent-orchestration.md)（**多智能体负载均衡算法原型**：元素=任务时长·k 段=k worker·最小化最大子数组和=makespan / 二分答案四件套：下界 max·上界 sum·feasible=≤k 段（至多松弛）·O(n·log S) / 贪心正确性=前缀覆盖反证·「能装就装」 / 连环问：LPT 4/3 近似比·List Scheduling 关键路径·异构加权=k8s 打分·LC 875/1482/1011 / **四模式总表**：主管-工人（星型·验收先于派发·层级 ≤2·委派三件套）·流水线（schema 契约·级联误差 stage 入口校验·换 agent=脑回路差异）·辩论投票（回声室=换人设不叫多样性·真多样性=异构数据源·外部锚）·市场拍卖（成本信号·Vickrey 二价·流拍兜底）/ 三买三税·通信三板斧·六失效速查 / veRL 连接：opponent pool·partial rollout 切分=今日题 / 金句「编排=组织设计·先想任务怎么分再想谁跟谁说话」）
- 🎭 [Day 126 — LFU 缓存（LFU Cache）+ 记忆系统三层设计与失效降级](week19/2026-10-08-lfu-cache-memory-systems.md)（**缓存即记忆：eviction 策略即遗忘曲线**——capacity=工作记忆上限·freq=记忆强度（get=提取练习）·minFreq 淘汰=遗忘执行器·TTL=时效衰减 / 三件套 O(1)：key→节点·freq→双向链表（同频 LRU 序）·minFreq 指针（LRU+频率分桶=LFU）/ 易错：put 已存在不淘汰·新 key 后 min_freq=1·get miss 不动 minFreq / 连环问：LRU 怕污染 vs LFU 怕冷启动→**aging freq 减半=记忆消退**·LRU-K·ARC·W-TinyLFU·TTL 惰性+定期·分布式 Gossip 同步计数器 / **记忆三层模型**：工作=context·情景=向量库（90% 工程在这）·语义=权重·程序性=skill / 四决策：写入评分+纠错记忆最贵·**冲突更新而非追加**·触发式检索+阈值生命线·离线巩固·遗忘三件套 / **MemGPT 虚拟分页**：LLM=CPU·函数调用=缺页中断·换入时机/换出策略/地址翻译 / **降级阶梯**：向量挂→BM25→纯工作记忆·六失效模式（空召回显式标注防幻觉·陈旧 TTL·**记忆中毒=注入持久化载体**·膨胀→aging·污染→rerank）/ 金句「记不住是残疾，忘不了是噩梦」）
- 🎭 [Day 125 — K 站中转内最便宜的航班（Cheapest Flights Within K Stops）+ Tool Use 工程、MCP 协议与 Computer Use](week19/2026-10-07-cheapest-flights-k-stops-tool-use-mcp.md)（**预算约束下的工具调用路径规划微缩模型**：节点=进度·边=工具调用·k 次中转=max_steps 预算 / 分层 Bellman-Ford：k+1 轮松弛=中转约束算法化身·backup 数组防一条边一轮用两次 / 二维状态 Dijkstra：`(node,stops)` 双维状态·堆顶即全局最优·SPFA 活跃队列 / 连环问：k≥n-2 退化·负权回 BF+负环（套利环限步）·多源虚拟源·必经工具分段最短路·二维预算 / **tool orchestration = (进度, 剩余步数) 状态图最短路** / Tool Use：schema 是写给模型的 prompt·负面约束防误调·20+ 工具→工具路由=RAG for tools·三层容错·危险确认门 / MCP：N×M→N+M·三角色·**Tools=动词·Resources=名词·Prompts=文档**·生命周期五步·**MCP vs FC=语言 vs 插座**·tool poisoning / Computer Use：四挑战·OSWorld 22%→40%+·**有 API 永远优先 API**·三板斧 / 金句「接口多像人话·标准插座·最后一双眼睛是屏幕」）
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

当前进度：**Week 19 已完结 🎉 / Day 128**（Week 19 主题：Agentic 系统与智能体工程 🎭 — Day 128 评估与观测收官日：LC 72 编辑距离 = 轨迹差异度算法原型——两个字符串 = golden trace ↔ agent 实际轨迹、插入/删除/替换 = 多走一步/漏走一步/走了岔路、最小编辑代价 = 轨迹差异分；二维 DP 三转移（相等免费对齐、不等 1+min(删/插/替)）O(mn)，滚动数组 O(min(m,n))，连环问：插删版 = m+n−2·LCS（git diff Myers O(ND) 内核）·对齐回溯 = diff 可视化·加权版 = DNA 打分矩阵·Wer 词错误率·Hirschberg 分治。面试技巧：评估与观测总框架——agent 评估五大难（输出开放/路径多样/环境交互/成本变量/非确定性）；离线 benchmark 生态（SWE-bench = 真实 GitHub issue + FAIL_TO_PASS/PASS_TO_PASS 双测试门、Verified 人工精选 500、污染问题三板斧）；三轴评估框架（成功率 pass@1 按难度分层 / 成本均值+P95 / 可靠性 pass@k 能力上限 vs pass^k 可部署下限）；轨迹级评估（终局 outcome vs 过程 process、步数效率、无效动作率、轨迹相似度 = 今日题）；可观测性三支柱 agent 版（traces 落盘、trajectory replay = eval 与 observability 铰链、eval harness = reward function 镜像）；Week 19 七天串讲收官与七剑合璧公式；金句「如果你不能衡量一个 agent，你就不能改进它」🎉）

**更新记录**：已连续更新 **128** 天，每日 20:42 自动推送。

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
