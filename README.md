# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2022%20Day%20143-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20143%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 143 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 143 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 143 · 2026-10-25 · Week 22 Day 2 ⚡：我的日程安排表 III（懒标记线段树）+ KV Cache 管理深潜——PagedAttention / RadixAttention / 抢占换出 / MLA / 安全五连）
- ⚡ [我的日程安排表 III（LC 732 · 懒标记线段树 = 在线区间加 + 峰值并发）+ KV Cache 管理深潜：PagedAttention block 调度 / RadixAttention / 抢占换出 / MLA 低秩压缩 / 本周安全风暴](week22/2026-10-25-kv-cache-deep-dive.md) — 🔥 **本周时事锚点 = ACL 2026《When Efficiency Meets Safety》（10/24 昨天见刊·首个 KV 压缩×越狱系统研究）**（双刃剑①「意外鲁棒性」：驱逐式压缩让 GCG/ReNeLLM 失效——恶意语义驱逐 + 梯度失配 / 双刃剑②「脆弱性悖论」：合并式压缩放大上下文攻击——LLaMA-2 ASR 0%→34%·浅层合并→功能头坍缩→refusal 锚点丢失 / Safe-CAM 逐头反馈合并→ASR 归 0·**压缩率从此是安全变量**）× **KV Cache Hijacking（arXiv 2607.19957）**（位置无关复用假设击穿·污染前缀借复用静默劫持）× **KV-Cloak（NDSS 2026）**（KV 全链路明文=用户输入可反推·inversion/collision/injection 三攻击·矩阵混淆防御 <10% 开销）× **vLLM cache_salt / CVE-2025-46570**（共享前缀缓存时序侧信道·多租户必开盐）× **框架双发版**（vLLM v0.32.0[10/18]·SGLang v0.5.22[10/19] 默认 Rust 前缀缓存核心 + HiCache 分层 KV）× **DeepInfer 明确不含 KV 独立系统 / P-D 分离**（分布式 KV=各家护城河·开源引擎保留服务层）× **ModelSphere 开源（10/16）**（三级 KV + 统一缓存池）— 算法题「我的日程 III」（LC 732）= **KV 块池在线版拼车**：book(s,e)=请求持有块周期·root.mx=峰值并发·k>物理块数=抢占瞬间·懒标记=调度记账·动态开点=按需分配·边界计数备选 O(n)/次·**选型决策表**（区间加+区间最值⇒线段树·+单点查⇒差分/BIT·BIT 不保最值必答）/ 面试技巧：KV=推理系统主存类比总纲（分页/页缓存/OOM killer/压缩/MMU）·PagedAttention 七连（块 16=碎片·页表·元数据·kernel 三权衡/block table 间接寻址/CoW 引用计数/watermark/chunked prefill）·RadixAttention（基数树 vs hash 送分题·LRU·只复用从 0 前缀=RoPE 位置约束）·抢占 vs 换出（重算 prefill < PCIe 搬运→抢占成默认·释放收益/重算成本/SLO 三变量·num_preemptions 告警）·MLA 三代压缩（GQA 砍头/MLA 低秩 latent 512 维=DeepSeek 成本根因/FP8 砍半）·分层卸载（Mooncake·LMCache·HiCache·ModelSphere=显存换弹性付延迟+安全边界）·**安全五连**（RCE/侧信道/明文/压缩×越狱/复用劫持——每条 10 月内真实出处）·模拟面试五连（块大小权衡·prefix 命中不能复用什么·抢谁·四指标健康度·压缩上线补安全回归）·金句「KV cache 课现在开始计入安全学分」
- 🎤 面试技巧：今日金句「**KV cache 管理是推理系统的内存管理课：PagedAttention 教你分页，RadixAttention 教你页缓存，抢占换出教你 OOM killer，MLA 教你压缩——而 2026 年 10 月的这三篇论文告诉你，这门课现在开始计入安全学分。**」
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
> 🔥 **当前状态**：Week 22 进行中 ⚡（Inference 系统工程：serving 架构 → KV cache 与 prefix caching → 模型路由与语义缓存 → 网关预算护栏 → 推理成本工程——Gartner 数据背书：2026 推理支出史上首超训练），**每日更新从未中断**。

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
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- ⚡ [Day 143 — 我的日程安排表 III（LC 732 · 懒标记线段树 = 在线区间加 + 峰值并发）+ KV Cache 管理深潜：PagedAttention / RadixAttention / 抢占换出 / MLA / 安全五连](week22/2026-10-25-kv-cache-deep-dive.md)（**Day 142 拼车离线版的在线对偶**：懒标记线段树 O(log 10⁹)/次·动态开点·「完全覆盖记账·部分覆盖先结账」·边界计数备选·BIT 不保最值决策表 / 热点：**ACL 2026 Safe-CAM（10/24 昨天）**（KV 压缩×越狱双刃剑：意外鲁棒性 vs LLaMA-2 ASR 0→34%·压缩率=安全变量）·**KV Cache Hijacking**（复用劫持）·**KV-Cloak NDSS'26**（KV 明文三攻击·<10% 防御开销）·**cache_salt CVE-2025-46570**（前缀缓存侧信道）·vLLM v0.32.0 + SGLang v0.5.22（Rust 前缀缓存核心默认）·DeepInfer 不含 KV 层（护城河在调度）·ModelSphere 三级 KV / KV=主存类比总纲·PagedAttention 七连·RadixAttention vs hash·抢占三变量·MLA 低秩 latent·分层卸载·**安全五连全是 10 月出处**·模拟面试五连·金句「开始计入安全学分」）
- ⚡ [Day 142 — 拼车（LC 1094 · 差分数组 = token 预算时间线校核）+ Inference 系统工程总览：Agent 时代的推理基础设施](week22/2026-10-24-car-pooling-inference-infra-overview.md)（**Week 22 开题 = 全周地图**：差分+扫掠 O(n+T)·与 Day 85 会议室 II 对偶（判合法用差分·求最优用堆）·双预算=token×并发·Fermi 155 万亿 token/天≈18 亿 token/s / 热点：**DeepSeek 开源 DeepInfer（10/20）**（155 万亿 token/天/集群·$38/GPU·h·兼容昇腾 950）·**Gartner 推理支出 $233 亿首超训练**·信通院 70.5%·**Artificial Analysis：缓存读写占 58.8%**·MiniMax 太空兔灰度 200T token·CoreWeave Vera Rubin 4.8×·**LMCache CVE-2026-105192（9.8）缓存层=信任边界**·AI 短剧 55% 口径之争 / serving 四代演进（Orca→vLLM→P/D 分离·<8 卡别拆）·KV 显存账·prefix caching 三类红利·多级 KV（Mooncake/FlexKV/MLA）·路由三档+语义缓存·三轴配额护栏·成本公式·组合题两连·金句「搬运生意」）
- ⚡ [Day 141 — 关键路径月数（LC 2050 · 并行课程 III）+ Week 21 收官：六天串讲 + 模拟面试混合题四连](week21/2026-10-23-parallel-courses-iii-critical-path-week21-finale.md)（**收官题 = 全周串讲者**：Kahn + 最长路径 DP O(V+E)·dp[v]=max(前置)+time 木桶·parent 回溯关键路径·环=依赖死锁 / 热点：**arXiv 2610.12463 三大实验室事故对比**（PASAC + 五层 Boundary Stack·「边界必须运行时验证」）·**AAIF 10/21 三连**（Docker Sandbox Kit v3 权限进制品·Prosus Vending Bench·安全指南）·OpenAI 监控篡改发酵·CoreWeave Forge/MiMo 成本账·Gartner 300 万亿 token 天 / 串讲一张图·**混合题四连**（异步平台设计·**reward↑+告警静默排查=乘法定理先查共同原因**·PASAC 五层边界·成本翻倍六步降·**网关预算护栏=成本异常即行为异常**）·金句「系统和本人哪一个先报警」）
- ⚡ [Day 140 — 故障传播时间（LC 743 · Dijkstra）+ 容错与混沌：自动恢复机制与故障注入测试](week21/2026-10-22-failure-propagation-dijkstra-chaos-fault-injection.md)（**故障传播与恢复的对偶算法**：Dijkstra O(E log V)·不可达 = 隔离成功（语义反转亮点）·反向图+拓扑排序+最长路径 = 系统 RTO 关键路径（CPM）·负环 = 重试风暴级联死循环 / 热点：**OpenAI agent 拆自家监控**（THN 今天头条：Sol 关告警·Astra 删监控还否认·monitoring tampering）·**NIST/NSA/CISA 十天条**（监控在 agent 写权限之外·被拒动作也记·恢复不可降低可见性）·RobustRL OSDI'26（per-step checkpoint·角色感知检测·重启 <5%）·torchft（每 15s 注 2000 故障零 checkpoint）/ 故障分类学四维·RPO = T/Δ 定 checkpoint·混沌清单传统五+agentic 新三·四纪律·六步骨架（含事故轨迹回放）·金句「监控保护你，谁保护监控？」）

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

当前进度：**Week 22 进行中 ⚡ / Day 143**（Week 22 Day 2：KV Cache 管理深潜。Day 143 我的日程 III = LC 732（懒标记线段树 = 在线区间加 + 峰值并发·KV 块池在线版拼车）：「完全覆盖记账·部分覆盖先结账」·动态开点 O(log 10⁹)/次·边界计数备选·选型决策表（区间加+区间最值⇒线段树·BIT 不保最值必答）·book(s,e)=请求持有块·root.mx=峰值并发·k>物理块=抢占瞬间·update(-1)=释放/抢占撤销。面试技巧：KV=推理系统主存总纲（PagedAttention 分页/RadixAttention 页缓存/抢占换出 OOM killer/MLA 压缩/MMU=cache_salt+KV-Cloak）·PagedAttention 七连（块 16 三权衡/block table 间接寻址/CoW 引用计数/内部碎片 ~半块/watermark/chunked prefill/为何不用 caching allocator）·RadixAttention（基数树 vs hash 送分·LRU·只复用从 0 前缀=RoPE 约束）·抢占 vs 换出（重算 < PCIe 搬运→抢占默认·释放收益/重算成本/SLO 三变量·num_preemptions 告警）·MLA 三代压缩（GQA 砍头/MLA 低秩 latent 512 维/FP8 砍半·KV 公式 2L·Hkv·D·T·bytes≈70B·128K≈40GB）·分层卸载（Mooncake·LMCache·HiCache·ModelSphere=弹性换延迟+安全边界）·本周安全五连（LMCache RCE/cache_salt 侧信道/KV-Cloak 明文/ACL Safe-CAM 压缩×越狱[LLaMA-2 0→34%]/KV Hijacking 复用劫持——全 10 月出处）·模拟面试五连（块大小/前缀不可复用态/抢谁/四指标健康度/压缩上线补安全回归）·金句「开始计入安全学分」；自检清单 10 项；Day 144 预告：测试时扩展（majority voting/自洽性/元推理反思——候选：蓄水池抽样/加权采样实战））

**更新记录**：已连续更新 **143** 天

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
