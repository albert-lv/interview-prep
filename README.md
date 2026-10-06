# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2019%20Day%20124%20🎭-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20124%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 124 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 124 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 124 · 2026-10-06 · Week 19 Day 3 🎭）
- 🪟 [最小覆盖子串 + Context Engineering 与长上下文管理](week19/2026-10-06-minimum-window-substring-context-engineering.md) — 今日算法题「最小覆盖子串」（LC 76）= 上下文压缩的算法原型：**窗口随任务只增不减，compaction 的合法性下界 = 任务 coverage（最小覆盖原则）**；need/cnt/missing 三件套把 O(1) 合法性判定变成引擎（missing 数**种类**不数次数 = 避免被重复字符污染的命门），贪心正确性 = 「右端点固定时短窗不劣」；连环问：数据流在线版（队列记位置跳必要最左）、anagram 超集变体、Unicode 哈希表降级不变逻辑、**丢字符有代价时转化为约束最优化（压缩成本要进账本，呼应延迟-质量交换所）**。面试技巧 Context Engineering 总纲：**「context 是 agent 的新显存——写代码管内存，写 agent 管上下文」**；三大失效机制（context rot：指令冲突/Lost in the Middle/error compounding 语境版/信噪比稀释；成本结构：输入 token 是乘数/tool schema 隐形大户/多智能体乘法爆炸；并发一致性：context=共享可变状态）；**四种策略总表**：截断（失忆·极低成本低保真）/滑动窗口（与最小覆盖子串同构·盲目截断丢 need 集合）/摘要压缩（有损+幻觉+不可逆）/结构化压缩（tool result elision=保真度高），生产真相=叠加态（Claude Code /compact=摘要+滑窗兜底+CLAUDE.md 重启式重建）；**结构化对策=外部化**：sub-agent 打包子任务只回传结论（10⁵→10³ token=最小覆盖的工程版）、文件系统外置（随机访问换线性存储）、滚动 state.md（append-only→read-modify-write，压缩从补救变保活）；Prompt Caching 账本（前缀严格一致/schema 稳定化/缓存断点与增量计费，KV cache 省显存 vs prompt caching 省钱包）；长文档放前指令放后+结构标记+约束两端重复；高频 Q&A：RAG vs 长上下文三问判据、摘要 vs 裁剪口诀「丢了能否低成本重新拿到？」、窗口=逻辑容量 vs KV=物理机制、多智能体上下文三条军规、context engineering vs prompt engineering 升维定义、**单轮开销四账相加（coding agent 历史 70%+ 是 tool output→管控 tool 输出是最有效杠杆）**
- 🎤 面试技巧：今日金句「**Truncation 是失忆，summarization 是听说，structured compaction 是整理，sub-agent 是让别人去记**——高级的上下文管理是让每条信息在正确的时间处于正确的位置，而不是让模型什么都记得」

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
- 🎭 [Day 124 — 最小覆盖子串（Minimum Window Substring）+ Context Engineering 与长上下文管理](week19/2026-10-06-minimum-window-substring-context-engineering.md)（**窗口只增不减·compaction 合法性下界=任务 coverage（最小覆盖原则）** / need·cnt·missing 三件套：missing 数种类不数次数=防重复字符污染·合法性判定 O(1) / 贪心正确性=右端点固定短窗不劣·数据流在线版·丢字符有代价=约束最优化（压缩成本进账本） / **context 是 agent 的新显存** / context rot 四大机制：指令冲突·Lost in the Middle·error compounding 语境版·信噪比稀释 / 成本结构：输入 token 是乘数·tool schema 隐形大户·多智能体乘法爆炸 / **四策略总表**：截断（失忆）·滑动窗口（同构最小覆盖）·摘要压缩（有损+幻觉）·结构化压缩（tool result elision）·生产=叠加态（/compact+滑窗兜底+CLAUDE.md 重建） / **外部化三件套**：sub-agent 打包只回传结论=最小覆盖工程版·文件外置（随机访问换线性存储）·滚动 state.md（append-only→read-modify-write） / prompt caching 账本：前缀严格一致·schema 稳定化·KV 省显存 vs caching 省钱包 / RAG vs 长窗口三问判据·裁剪口诀「丢了能否低成本重新拿到」·单轮四账相加（70%+ 是 tool output=管控杠杆） / 金句「Truncation 是失忆，summarization 是听说，structured compaction 是整理，sub-agent 是让别人去记」）
- 🎭 [Day 123 — 钥匙和迷宫（Shortest Path to Get All Keys）+ Agent 架构三范式深讲（ReAct / Plan-and-Execute / Reflexion）](week19/2026-10-05-shortest-path-all-keys-agent-three-paradigms.md)（状态增强 BFS 能力型原型：状态=`(行,列,钥匙mask)` 三元组·同一格带不同钥匙=不同状态·`(r,c)` 去重误杀"带钥匙二进宫"最优路径·贪心绕路反成最优 / 锁门=tool gating·**mask=agent state 第一课：状态必须包含你已拥有什么，而不只是你在哪** / k>6→A*·IDA*·多机器人=任务分配+各自规划·同族 LC 1293 预算型 / 三范式总表：ReAct=想一步做一步看反馈（error compounding·死循环·context rot）·P&E=规划一次执行到底（早期错全链路错·replanner 三件套=失败率阈值·verifier checkpoint·环境 diff）·Reflexion=复盘+episodic memory（verbal RL·起效前提=干净验证器） / 一句话区分：规划揉进步里 vs 一次性买断·生产=分层叠加态（Planner 低频→ReAct 执行→Reflexion 重试→全局预算） / 8 道连环问·**veRL 回调：失败轨迹=Reflexion 燃料**）
- 🎭 [Day 122 — 打开转盘锁（Open the Lock）+ Week 19 开启：Agentic 系统与智能体工程](week19/2026-10-04-open-the-lock-agentic-systems-intro.md)（状态图建模：状态=10⁴ 锁面·动作=8 邻居现场生成·deadends=删点合并 visited / BFS 分层展开第一次到达即最优·起点在 deadends 直接 -1·「停留才死经过不死」WA 点 / 状态爆炸三板斧：双向 BFS frontier 用 set 谁小扩谁 O(b^d)→O(b^(d/2))·A* h=海明距离可采纳·IDA* O(d) 内存 / 核心同构：状态=context·拨动=action·deadends=惩罚反馈·target=目标——**agent 规划=隐式状态图搜索，RL=不知图时的搜索策略，test-time compute=步间思考预算** / Week 19 总览：chatbot 是函数 agent 是进程·六议题地图（架构 Day 123·Context Eng Day 124·Tool/MCP Day 125·记忆 Day 126·多智能体 Day 127·评估 Day 128）·两大敌人=失控循环+上下文腐化）
- 🏆 [Day 121 — 24 点游戏（24 Game）+ Week 18 综合复习：推理模型与 Test-Time Compute](week18/2026-10-03-24-game-week18-review.md)（减而治之：有序对 6 运算枚举·括号被递归结构隐式枚举·浮点 ε+除零保护+排序记忆化 / 核心同构：递归子树=推理树节点·合并路径=CoT·`==24`=ORM·**搜索空间小+验证器便宜→暴力搜索就是推理** / 5 张牌以上要剪枝评估=PRM 雏形·目标分解先验=pUCT P(s,a) / 主线公式「正确率=搜索×验证×算力分配」串六天：o1 两根轴·搜索三策略·验证器四类成本谱·compute-optimal·系统账本 / 10 道连环问通关：o1 本质·验证比生成容易·1−(1−p)^N·PRM 数据三路·MCTS 四步+LLM 化·Self-Consistency 失效·KV 显存口算+GRPO 白嫖·五偏差·Goodhart 四幕剧·系统设计四步 / 收官金句「答案能被便宜验证的问题暴力搜索就是推理」·Week 18 完结 🎉）
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

当前进度：**Week 19 进行中 🎭 / Day 124**（Week 19 主题：Agentic 系统与智能体工程 🎭 — Day 124 Context Engineering 日：最小覆盖子串 = 上下文压缩的算法原型——窗口只增不减，compaction 合法性下界=任务 coverage（最小覆盖原则），need/cnt/missing 三件套中 missing 数种类不数次数是 O(1) 判定命门，贪心正确性=「右端点固定时短窗不劣」；面试技巧 Context Engineering 总纲「context 是 agent 的新显存——写代码管内存，写 agent 管上下文」；三大失效机制（context rot 四机制：指令冲突/Lost in the Middle/error compounding 语境版/信噪比稀释；成本结构：输入 token 是乘数、tool schema 隐形大户、多智能体乘法爆炸；并发一致性：context=共享可变状态）；四策略总表（截断=失忆/滑动窗口=最小覆盖同构/摘要压缩=有损+幻觉/结构化压缩=tool result elision）生产真相=叠加态（/compact+滑窗兜底+CLAUDE.md 重启式重建）；外部化三件套：sub-agent 打包只回传结论=最小覆盖工程版（10⁵→10³ token）、文件系统外置（随机访问换线性存储）、滚动 state.md（append-only→read-modify-write 压缩从补救变保活）；prompt caching 账本（前缀严格一致/schema 稳定化/KV cache 省显存 vs prompt caching 省钱包）；RAG vs 长上下文三问判据、摘要 vs 裁剪口诀「丢了能否低成本重新拿到」、单轮开销四账相加（coding agent 历史 70%+ 是 tool output→管控 tool 输出是最有效杠杆）；金句「Truncation 是失忆，summarization 是听说，structured compaction 是整理，sub-agent 是让别人去记」🎭）

**更新记录**：已连续更新 **124** 天，每日 20:42 自动推送。

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
