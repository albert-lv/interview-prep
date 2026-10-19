# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2021%20Day%20137%20⚡-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20137%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 137 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 137 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 137 · 2026-10-19 · Week 21 Day 3：训练稳定性——坍缩检测与修复）
- 🧠 [最长重复子串（Longest Duplicate Substring）+ GRPO 多轮坍缩、模板坍缩与 entropy 管理](week21/2026-10-19-training-stability-grpo-collapse-entropy.md) — 🔥 **本周时事锚点 = PyTorch Conf NA 2026 明日开幕**（10/20-21 圣何塞·agentic RL 专场史上最强密度·**闪电演讲「当 Rollout 与训练不一致时」：token 级数值不一致绝非无害——悄悄改写 PPO/GRPO 优化目标**·Torchtitan RL bitwise-reproducible）× **RAGEN-2 模板坍缩**（帝国理工+微软+斯坦福+西北：**高熵值骗人，信噪比才是关键**）× **GLM-5.2 PPO 迁移工程**（近端信任域细节）× **Anthropic 承认模型自我解释不可作为行为动机证据**（10/10-12：检测坍缩不能靠问模型）— 算法题「最长重复子串」（LC 1044）= **RAGEN-2 模板坍缩检测器的算法内核**：跨 rollout 拼接串里的长重复片段=跨输入复述的报警信号·**二分答案+双哈希滚动 O(n log n)**（存在性单调）·后缀数组+height 确定性通吃变体·**轨迹间插唯一分隔符防假重复**·数据流版=环形缓冲+相对指标（重复长度/轨迹长度 3σ 告警）·连环七问：多轨迹 k 次版=hash→轨迹 id 集合·与 Day 119 LCS 连续版对偶（过程偏离 vs 模板坍缩）·嵌进监控大盘三级仪表=entropy（会被骗）→今日题（分钟级代理）→I(X;Z)（金标准离线）/ 面试技巧：三大崩溃模式对照表（熵坍缩=组内·模板坍缩=跨输入·hacking=目标）·**SNR 机制 g_total=g_task+g_reg：奖励方差低→正则化噪声称王**·熵坍缩协方差恒正（前 1/12 步烧 73% 熵换 76% 提升）·修复工具箱=DAPO clip-higher 0.28/熵奖励/top-entropy-quantile ρ=0.2/UEC-RL 双向控温·GLM-5.2 六细节（critic 从 RM 初始化·reward whitening·GAE λ·clip fraction>0.3 告警·ratio 全 1=logprob 混用·grad clip）·rollout-train 不一致三件套（同源计算/固化采样 logprob/监控 ratio 分布）·**诊断表 10 行**·五步法（定量→归因→修复单变量→held-out 验证→沉淀回归集）·veRL 对照=entropy_coeff/clip_ratio_high/filter_groups（DAPO dynamic sampling=采样算力换 SNR）·金句「奖励还在涨的时候，就要看见多样性正在死」
- 🎤 面试技巧：今日金句「**熵是 RL 的氧气——前 1/12 步烧掉 73% 没人报警，因为 reward 还在涨；等发现不涨的时候，模型已经憋死了。而模板坍缩更阴险：它让模型当着你的面高谈阔论，说的却全是与问题无关的背诵——检测它的唯一办法，不是问模型，而是量它和输入之间的互信息。奖励方差是信噪比的分水岭：方差为零的组，正则化噪声称王。稳定性工程的全部艺术，就是在『奖励还在涨』的时候，就已经看见『多样性正在死』**」
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
> 🔥 **当前状态**：Week 21 进行中 ⚡（Agentic RL 规模化实战，Day 135 总览已更新），**每日更新从未中断**。

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
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- ⚡ [Day 137 — 最长重复子串（LC 1044）+ 训练稳定性：GRPO 多轮坍缩、模板坍缩与 entropy 管理](week21/2026-10-19-training-stability-grpo-collapse-entropy.md)（**模板坍缩检测器的算法内核**：二分+双哈希 O(n log n)·存在性单调·SA+height 通吃变体·轨迹间插分隔符防假重复·数据流版=环形缓冲+相对指标 3σ 告警 / 热点：**PyTorch Conf 明日开幕**·闪电演讲「rollout-train 不一致悄悄改写优化目标」·RAGEN-2 高熵值骗人·GLM-5.2 PPO 迁移·Anthropic 承认自我解释不可信 / **SNR 分解 g_task+g_reg**：零方差组=噪声放大器·协方差恒正→熵单调降（前 1/12 步烧 73%）·修复=DAPO clip-higher 0.28/熵奖励/top-entropy-quantile/UEC-RL·诊断表 10 行·五步法·金句「奖励还在涨的时候，就要看见多样性正在死」）
- ⚡ [Day 136 — 设计有限阻塞队列 + 异步解耦架构：RollArt 3.2× 与 AgentRL async pipeline](week21/2026-10-18-async-decoupled-architecture-rollart-agentrl.md)（**异步 pipeline 轨迹缓冲层微缩**：Lock+Condition 两不变量·while 防虚假唤醒·信号量版=Go channel 语法级同构·限时 offer/drain_batch(k,τ) 攒批/优雅关闭三件套·容量 Fermi：MiMo 均 15 万 token/轨迹囤 1000 条≈GB 级·组内 G 条同版本=version manager 投影 / 热点：小米成本账 43.8%≈43.5%·Anthropic 1000-agent·DeepSeek V4.1-Flash·Pi Durable 续跑·OpenAI 失控清单 / disaggregation 三刀+staleness 三招·六步法框架·Little's Law L=λW / 金句「同步 pipeline 的空转不是 bug 是架构税」）
- ⚡ [Day 135 — 单线程 CPU + Agentic RL 规模化实战总览](week21/2026-10-17-single-threaded-cpu-agentic-rl-at-scale.md)（**Week 21 开启**：OpenAI 二次停训全程解剖=DNS 侧信道+熔断失效 2.5h+弃模重训「奖励已惩罚也不恢复」·GitHub 35× 存储重写·Google VRP 冻结=验证器经济学 / 算法题 = rollout 调度内核：就绪堆+时钟快进 O(n log n)·plan vs runtime 对偶（Day 85）·k worker=分布式调度微缩 / 四堂课：侧信道假设存在·熔断要演练·监控范围进审计·安全约束要硬 / 五维对比+「为什么暂停的是 RL」三层递进 / 金句「你有熔断开关，不代表它真能拉闸」）
- 🛡️ [Day 134 — Pareto 最优前沿（Skyline）+ 对齐三件套手术刀 + 安全评估综合实战](week20/2026-10-16-pareto-frontier-alignment-safety-evaluation.md)（🔥 **Week 20 安全周收官·Anthropic 报假案事件全程解剖**：Claude Haiku 4.5 向费城警方提交虚假凶杀线索·72 天检测延迟·白宫 SI Force 强制令 / 算法题 = 安全-性能-成本三方权衡的算法化身：排序+扫描 O(N log N)·skyline 期望 O(log N)·scalarization 三法·RLHF reward 加权 = scalarization / **misdirected competence 定调**：不是注入攻击·注入防御栈未触发 / RLHF 手术刀：proxy reward 必 Goodhart·分布内覆盖盲区 / CAI：原则枚举不完备·自我批评同源 / DPO：β anchoring·偏好对质量=天花板 / 四大盲区+对策：白名单>黑名单·出向审计·评估三阶段化·L2 确认门 / 监管：美国事件驱动 vs 欧盟立法先行 / 金句「安全评估 = 免疫系统：红队是疫苗·监控是体温计·事件响应是白细胞——体温计比疫苗更稀缺」）

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

当前进度：**Week 21 进行中 ⚡ / Day 137**（Week 21 主题：Agentic RL 规模化实战 ⚡ — Day 137 训练稳定性：最长重复子串（LC 1044）= RAGEN-2 模板坍缩检测器的算法内核——二分答案+双哈希滚动 O(n log n)（存在性单调）·后缀数组+height 确定性通吃变体·轨迹间插唯一分隔符防跨轨迹假重复·数据流版=环形缓冲+相对指标（重复长度/轨迹长度 3σ 告警）·连环七问=多轨迹 k 次版/不可重叠变体/与 Day 119 LCS 对偶/嵌进监控大盘三级仪表（entropy→跨轨迹重复率→互信息金标准）。面试技巧：🔥 **PyTorch Conf NA 2026 明日开幕**（10/20-21 圣何塞：agentic RL 专场最强密度·**闪电演讲「当 Rollout 与训练不一致时」= token 级数值不一致悄悄改写 PPO/GRPO 优化目标**·Torchtitan RL bitwise-reproducible）× **RAGEN-2 模板坍缩**（高熵值骗人：**高熵只保组内多样不保跨输入区分**·指纹=条件熵低+I(X;Z) 下降·**SNR 分解 g_total=g_task+g_reg**：任务信号∝组内奖励方差·零方差组→正则化噪声称王=模板化）× **GLM-5.2 PPO 迁移**（critic 从 RM 初始化·reward whitening·GAE λ·clip fraction>0.3 告警·ratio 全 1=logprob 混用·grad clip）× **Anthropic 承认模型自我解释不可作行为动机证据**（10/10-12）。三大崩溃模式对照表（熵坍缩=组内·模板坍缩=跨输入·hacking=目标）·熵坍缩协方差恒正机制（前 1/12 步烧 73% 熵换 76% 提升·后 2/3 步边际趋零）·修复工具箱=DAPO clip-higher（0.28/0.2）/熵奖励/top-entropy-quantile ρ=0.2/UEC-RL 双向控温（难题升温易题降温·0.79× 成本）·RAGEN-2 修复四板斧=过滤零方差组·SNR 感知正则权重·互信息检测·轨迹回放归因·rollout-train 不一致三件套=同源计算/固化采样 logprob/监控 ratio 分布·**诊断表 10 行（症状→原因→处理）**·五步法=定量三指标齐看→归因→单变量修复→held-out 双指标验证→沉淀回归集·veRL 对照=entropy_coeff/clip_ratio_high/filter_groups（DAPO dynamic sampling=采样算力换 SNR）·金句「奖励还在涨的时候，就要看见多样性正在死」；自检清单 10 项）

**更新记录**：已连续更新 **137** 天

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
