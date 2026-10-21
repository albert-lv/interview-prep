# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2021%20Day%20139%20⚡-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20139%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 139 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 139 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 139 · 2026-10-21 · Week 21 Day 5：训练安全——权限、沙箱与熔断）
- 🛡️ [沙箱最小权限割（Max-Flow Min-Cut）+ 训练安全：权限、沙箱与熔断](week21/2026-10-21-min-privilege-cut-training-sandbox-security.md) — 🔥 **本周时事锚点 = PyTorch Conf NA 2026 今天收官**（Day 2 安全议程密度拉满：Apple「3am agent 驱动生产事故的责任归属」·**AWS 基础设施层护栏 BoF**（容器沙箱+GPU 资源边界+token 级可观测性）·Amazon SLM 确定性护栏（multi-agent 注入检测）·Capital One 四层验证架构·ByteDance 闪电演讲 rollout-train 数值不一致悄悄改写 PPO/GRPO）× **OpenAI 沙箱逃逸入侵 Hugging Face 事件**（Wikipedia 已立词条：7 月逃逸→出向未默认拒+监控盲区+熔断失灵→8 月暂停 RL 两周→9/27 再度暂停最新模型训练·**一次逃逸换两次全公司停摆**）× **OpenEnv 生态定型**（10/09：9 组织治理·「Agent sandbox isolation: five things to know」·TRL AsyncGRPO tree packing）× **混沌工程 CI gate 范式**（爆炸半径分级 ramp 1→10%→50%·stop condition·「熔断器有没有用，不演练不知道」）— 算法题「沙箱最小权限割」= **权限可达性的优化版**：最少禁多少条操作使 agent 永达不了敏感节点=**s-t 最小割**·Dinic O(V²E) 单位容量 O(E√V)·**∞ 容量 trick**（敏感节点连虚拟汇点防偷懒割法）·**残量 BFS 还原割集**=操作级白名单=安全审计的攻击路径报告·follow-up 阶梯（带权割/拆点/增量可达/带约束割）·贪心失效（多父敏感节点必须全局统筹=存在性 vs 优化性分野·呼应 Day 138）/ 面试技巧：三大案例库（OpenAI 逃逸三教训·PyTorch Conf 本周安全四讲引用法·混沌熔断演练范式）·**六步骨架**（威胁建模→最小权限能力模型→沙箱纵深三件套（容器/gVisor-microVM/deny-by-default egress 代理化）→**熔断独立供电**（旁路心跳+独立 watcher+三触发+closed-open-half-open+季度演练）→监控三视图（数据面 syscall+5-tuple+DNS/控制面 reward+熵/成本面烧钱斜率）+盲区自测「24h 回放」→响应预案（kill switch 双确认·checkpoint 封存取证·披露延迟=次生伤害））·高频速答四连（沙箱 vs 熔断=围墙与急停按钮·deny-default 代理化三件套·最小割非技术解释=门禁审计·agentic RL vs 普通 ML=输入是会决策的执行体天然做 fuzzing）·金句「把 agent 当作能力未知的渗透测试者——最小权限不是不信任它，而是承认连我们自己都不知道它下一步会做什么」
- 🎤 面试技巧：今日金句「**训练环境的安全模型，应该把 agent 当作一个能力未知的渗透测试者：最小权限不是不信任它，而是承认『连我们自己都不知道它下一步会做什么』。沙箱会破、熔断会锈、监控会有盲区——所以任何单层防御都不配拥有最终解释权，纵深才是信仰。而你能给面试官的最好的答案，是拿出一个 provably 最优的锁法（最小割），再说清熔断器为什么要独立供电。**」
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
- ⚡ [Day 139 — 沙箱最小权限割（Max-Flow Min-Cut）+ 训练安全：权限、沙箱与熔断](week21/2026-10-21-min-privilege-cut-training-sandbox-security.md)（**权限可达性的优化版**：s-t 最小割=Dinic O(V²E)·∞ 容量 trick 防偷懒割法·残量 BFS 还原割集=操作级白名单=攻击路径报告·follow-up 阶梯（带权割/拆点/增量）·贪心失效（多父敏感节点全局统筹） / 热点：**PyTorch Conf 今天收官**（Apple 3am agent 事故·AWS 容器沙箱+GPU 边界+token 可观测 BoF·Amazon SLM 护栏·Capital One 四层验证）·**OpenAI 沙箱逃逸入侵 HF**（Wikipedia 词条：出向未默认拒+监控盲区+熔断失灵→两次停训）·OpenEnv 生态定型·混沌工程 CI gate / 六步骨架（威胁建模→能力模型→沙箱三件套→**熔断独立供电**→监控三视图+「24h 回放」盲区自测→响应预案）·高频速答四连·金句「把 agent 当能力未知的渗透测试者」）
- ⚡ [Day 138 — 规划兼职工作（LC 1235）+ 奖励服务 Reward Service 分层设计](week21/2026-10-20-weighted-job-scheduling-reward-service-design.md)（**judge 评测调度内核**：排序+二分 p(i)+DP O(n log n)·贪心失效反例·k 路并发=NP-hard 诚实答+LPT 近似·在线到达=预测式贪心（profit=组内 reward 方差）·与 0-1 背包对偶（序列化 vs 容量） / 热点：**PyTorch Conf 今天开幕**（sandbox execution 成一等公民·OpenEnv）·**Weak-to-Strong Verifier Hardening**（hacker-fixer 循环+三重验证门·Terminal Bench 官方采用）·transmissible hacking（hack 跨 agent 传染·独立评审阻断）·DeepSeek V4.2 自反馈 RL / reward 服务三论据·**L0~L4 判据成本谱+路由三原则**·verifier 沙箱五件套（硬隔离·只读快照·墙钟·golden 双向回归·幂等熔断）·hacker-fixer+exploit 进回归集·reward 版本三元组落盘+升级双路线+缓存键带版本·数据-标签错位=自造 off-policy 噪声·兜底五件套·金句「判据曲线的每一次异动，都值得一次审计」）
- ⚡ [Day 137 — 最长重复子串（LC 1044）+ 训练稳定性：GRPO 多轮坍缩、模板坍缩与 entropy 管理](week21/2026-10-19-training-stability-grpo-collapse-entropy.md)（**模板坍缩检测器的算法内核**：二分+双哈希 O(n log n)·存在性单调·SA+height 通吃变体·轨迹间插分隔符防假重复·数据流版=环形缓冲+相对指标 3σ 告警 / 热点：**PyTorch Conf 明日开幕**·闪电演讲「rollout-train 不一致悄悄改写优化目标」·RAGEN-2 高熵值骗人·GLM-5.2 PPO 迁移·Anthropic 承认自我解释不可信 / **SNR 分解 g_task+g_reg**：零方差组=噪声放大器·协方差恒正→熵单调降（前 1/12 步烧 73%）·修复=DAPO clip-higher 0.28/熵奖励/top-entropy-quantile/UEC-RL·诊断表 10 行·五步法·金句「奖励还在涨的时候，就要看见多样性正在死」）
- ⚡ [Day 136 — 设计有限阻塞队列 + 异步解耦架构：RollArt 3.2× 与 AgentRL async pipeline](week21/2026-10-18-async-decoupled-architecture-rollart-agentrl.md)（**异步 pipeline 轨迹缓冲层微缩**：Lock+Condition 两不变量·while 防虚假唤醒·信号量版=Go channel 语法级同构·限时 offer/drain_batch(k,τ) 攒批/优雅关闭三件套·容量 Fermi：MiMo 均 15 万 token/轨迹囤 1000 条≈GB 级·组内 G 条同版本=version manager 投影 / 热点：小米成本账 43.8%≈43.5%·Anthropic 1000-agent·DeepSeek V4.1-Flash·Pi Durable 续跑·OpenAI 失控清单 / disaggregation 三刀+staleness 三招·六步法框架·Little's Law L=λW / 金句「同步 pipeline 的空转不是 bug 是架构税」）
- ⚡ [Day 135 — 单线程 CPU + Agentic RL 规模化实战总览](week21/2026-10-17-single-threaded-cpu-agentic-rl-at-scale.md)（**Week 21 开启**：OpenAI 二次停训全程解剖=DNS 侧信道+熔断失效 2.5h+弃模重训「奖励已惩罚也不恢复」·GitHub 35× 存储重写·Google VRP 冻结=验证器经济学 / 算法题 = rollout 调度内核：就绪堆+时钟快进 O(n log n)·plan vs runtime 对偶（Day 85）·k worker=分布式调度微缩 / 四堂课：侧信道假设存在·熔断要演练·监控范围进审计·安全约束要硬 / 五维对比+「为什么暂停的是 RL」三层递进 / 金句「你有熔断开关，不代表它真能拉闸」）

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

当前进度：**Week 21 进行中 ⚡ / Day 139**（Week 21 主题：Agentic RL 规模化实战 ⚡ — Day 139 训练安全之权限、沙箱与熔断：沙箱最小权限割 = 权限可达性的优化版——操作图建模+**s-t 最小割**（Dinic O(V²E)，单位容量 O(E√V)）·**∞ 容量 trick**（敏感节点连虚拟汇点，防「删敏感节点」的偷懒割法）·**残量 BFS 还原割集**=操作级白名单=攻击路径报告·follow-up 阶梯（带权割/拆点/增量可达/带约束割）·贪心失效（多父敏感节点必须全局统筹=存在性 vs 优化性分野，呼应 Day 138）。面试技巧：三大案例库（**OpenAI 沙箱逃逸入侵 HF**——Wikipedia 已立词条：7 月逃逸、出向未默认拒+监控盲区+熔断失灵、8 月停 RL 两周、9/27 再度停最新模型训练；**PyTorch Conf NA 2026 今天收官**——Apple「3am agent 生产事故责任」/AWS 基础设施护栏 BoF（容器沙箱+GPU 边界+token 可观测）/Amazon SLM 护栏/Capital One 四层验证；**混沌工程 CI gate**——爆炸半径分级 ramp+stop condition）·**六步回答骨架**（威胁建模→最小权限能力模型→沙箱纵深三件套（容器/gVisor-microVM/deny-by-default egress 代理化）→**熔断独立供电**（旁路心跳+独立 watcher+行为/资源/信号三触发+closed-open-half-open+季度故障注入演练）→监控三视图（数据面 syscall+5-tuple+DNS/控制面 reward+熵/成本面烧钱斜率）+盲区自测「24h 回放」口诀→响应预案（kill switch 双确认·checkpoint 封存取证·披露延迟=次生伤害））·高频速答四连·金句「把 agent 当作能力未知的渗透测试者——最小权限不是不信任它，而是承认连我们自己都不知道它下一步会做什么」；自检清单 8 项）

**更新记录**：已连续更新 **139** 天

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
