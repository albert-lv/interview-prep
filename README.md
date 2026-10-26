# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2022%20Day%20144-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20144%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 144 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 144 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 144 · 2026-10-26 · Week 22 Day 3 ⚡：蓄水池抽样（LC 398/382 · 无限流无偏均匀采样）+ 测试时扩展 Test-Time Scaling——多数投票 / 自洽性 / 自适应采样）
- ⚡ [蓄水池抽样（LC 398 随机数索引 / LC 382 链表随机节点 · 无限流无偏均匀采样）+ 测试时扩展：多数投票 / 自洽性 / 自适应采样 / 投票失效边界 / 蓄水池工程抽检](week22/2026-10-26-test-time-scaling-reservoir-sampling.md) — 🔥 **本周时事锚点 = Kimi K3.1 Pro 发布（10/24 凌晨·两天前·HN 947 分）**（3 万亿级开源模型·Terminal-Bench 4.0 得分 65.7% 超 GPT-6 Astra·AA 智能指数全球第 4·价格仅 1/5·**K3 底座上的大规模后训练 RL = 思考能力靠推理时计算放大**）× **K3.1 Pro Highspeed 同步发布（10/24）**（同等智能 2~3× 速度——test-time compute 的第三种花法：**买速度**，三个轴预算可互换·架构定汇率）× **Cerebras 万亿模型部署账本（10/25）**（59 片晶圆装 Kimi K3·线性注意力无 KV cache·每层硬件占用省 1/3·**架构被晶圆数量定价**·直接改写采样成本公式的常数项）× **《Self-Consistency Is Losing Its Edge》（arXiv 2511.00751）**（Gemini 2.5 Pro MATH-500 98%→99.6% 花 15× 算力·Flash-Lite >15 路径**准确率反降**·冗余机理：投票磨平方差磨不平偏差）× **《When Self-Consistency Backfires》（arXiv 2608.11403）**（GPQA 上投票让 Qwen2.5-7B 在 56.6% 问题上**更差**·小模型高方差投票≈随机抽签）× **RUVERBENCH**（judge 自投票 3~5 票饱和·系统性偏差投 100 票消不掉——回收 Day 138 L3 跨家族判据设计理由）× **NeurIPS《Blend-ASC》**（动态分配采样·省 6.8× 样本·无超参=流式早停正确打开方式）× **《When To Solve, When To Verify》**（SC 胜 GenRM 需 8× 算力才追平·难题占比高才倒向验证侧）× **GVS5H（10/25 HN 热榜）**（五 Qwen3.8-27B 协作匹配 Fable 5·堆多样性 vs 堆单点）× HTTP2Shell 第二周（推理网关 HTTP 客户端同在攻击路径）— 算法题「蓄水池抽样」= **agent 轨迹无限流无偏抽检器**：算法 R 三行代码 + k/n 归纳证明（进入概率 × 留存概率连乘）·O(n) 时间 O(k) 空间单遍·为什么朴素不行连环问（rand()%n 不知 n·k/n 概率需全局协调·哈希粘性=确定性适合 tracing·水塘=无偏恰好 k 个）·**进阶阶梯八连**（Algorithm L 指数跳跃 O(k log(n/k))·A-Res 加权 key=rand^(1/w)·k=1 链表特例·分布式分层合并「流量不等不能直接合并」坑·top-k 对偶 Count-Min/Space-Saving·置信区间 fpc·带删除兜底·动态采样率控制回路）·工程映射三条线闭环（Day 138 轨迹抽检无偏否则瞎 / Day 142 网关 10 万 QPS tracing O(1) / Day 136 队列实时审计）/ 面试技巧：三条 Scaling 轴框架（并行宽度/串行深度/单次速度·K3.1 三轴同演）·采样家族谱系（SC/BoN/加权排序投票/Pairwise-Guided Logit Adjustment 10/13/USC/verifier 引导/Agent 轨迹聚合失效→Self-Choice）·**投票失效三场景 + bias-variance 统一理论**（太强冗余负收益/太弱≈随机/系统性偏差·同家族路径=同一盲点的 k 次复印）·预算经济学五连（3~5 票原则·Blend-ASC 6.8×·难度分布决定生成 vs 验证·k 路成本 = k×单发×(1−前缀复用率) + KV×n·错误代价不对称才买）·工程清单六条（canonicalization 先于投票·temperature 分层·置信度=票分布·审计留痕·聚合器版本化·超时降级保 p99）·模拟面试五连（SC vs BoN 选型·跨家族为什么强·k 取多少满分答·10 万 QPS 网关采样聚合架构·蓄水池做无偏审计）·金句「多数投票是方差绞肉机」
- 🎤 面试技巧：今日金句「**多数投票是一台方差绞肉机：它把采样噪声磨平，却对系统性偏差无能为力——同一家族模型投出的多数，只是同一个盲点的 k 次复印。投票的收益在第三到第五条路径就结算完毕，后面的采样只是在给冗余充值。而 2026 年的工程答案不是不投票，是「分层买」：3~5 票打底，异构破偏差，verifier 攻难题，早停防浪费——测试时计算不是超参数，是一门预算学。**」
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
- ⚡ [Day 144 — 蓄水池抽样（LC 398/382 · 无限流无偏均匀采样）+ 测试时扩展：多数投票 / 自洽性 / 自适应采样 / 投票失效边界](week22/2026-10-26-test-time-scaling-reservoir-sampling.md)（**算法 R 三行代码 + k/n 归纳证明**（进入概率×留存概率连乘）·O(n)/O(k) 单遍·进阶八连：Algorithm L 指数跳跃·A-Res 加权 rand^(1/w)·k=1 链表特例·分布式「流量不等不能合并」坑·top-k 对偶 sketch·fpc 置信区间·动态采样率控制回路 / 热点：**Kimi K3.1 Pro（10/24·HN 947 分）**（3T 开源·Terminal-Bench 65.7% 超 GPT-6·AA 第 4·后训练 RL 放大思考能力）·**Highspeed 2~3×**（买速度=第三轴）·**Cerebras 账本（10/25）**（线性注意力无 KV cache·架构被晶圆定价）·**Self-Consistency Is Losing Its Edge**（15× 算力换 1.6%·>15 路径反降·冗余机理）·**When SC Backfires**（GPQA 56.6% 问题更差）·**RUVERBENCH**（3~5 票饱和·消方差消不了偏差）·**Blend-ASC**（6.8× 样本·流式早停）·**When To Solve When To Verify**（SC 胜 GenRM 8×）·**GVS5H**（五 27B 协作匹配 Fable 5）/ 三条 Scaling 轴框架·采样家族谱系·**bias-variance 统一理论**（同家族=同一盲点的 k 次复印）·预算经济学五连·工程清单六条（canonicalization/temperature 分层/置信度=票分布/审计留痕/聚合器版本化/超时降级）·模拟面试五连·金句「方差绞肉机」）
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

当前进度：**Week 22 进行中 ⚡ / Day 144**（Week 22 Day 3：测试时扩展 Test-Time Scaling。Day 144 蓄水池抽样 = LC 398/382（无限流无偏均匀采样 = agent 轨迹/推理请求流的无偏抽检器）：算法 R 三行代码·k/n 归纳证明（进入概率 × 留存概率连乘）·O(n) 时间 O(k) 空间单遍·朴素不行连环问（rand()%n 不知 n·哈希粘性=确定性 vs 水塘=无偏恰好 k 个·分场景选型）·进阶八连（Algorithm L 指数跳跃 O(k log(n/k))·A-Res 加权 key=rand^(1/w)·k=1 链表特例·分布式分层合并坑·top-k 对偶 Count-Min/Space-Saving·fpc 置信区间·带删除兜底·动态采样率控制回路）·工程闭环三条线（Day 138 轨迹抽检无偏 / Day 142 10 万 QPS tracing O(1) / Day 136 队列实时审计）。面试技巧：三条 Scaling 轴（并行宽度/串行深度/单次速度·K3.1 Pro 三轴同演·Highspeed 买速度·Cerebras 线性注意力改写常数项）·采样家族谱系（SC/BoN/加权投票/Pairwise-Guided/USC/verifier 引导/Agent 轨迹聚合失效→Self-Choice）·**投票失效三场景 + bias-variance 统一理论**（太强冗余负收益[arXiv 2511.00751·15× 算力换 1.6%·>15 路径反降]/太弱≈随机[2608.11403·GPQA 56.6% 更差]/系统性偏差[RUVERBENCH 3~5 票饱和]·同家族路径=同一盲点的 k 次复印·跨家族 PoLL 7× 更便宜=Day 138 L3 判据依据）·预算经济学（3~5 票原则·Blend-ASC 6.8×·难度分布决定生成 vs 验证[GenRM 需 8× 追平 SC]·k 路成本 = k×单发×(1−前缀复用率)+KV×n·错误代价不对称才买）·工程清单六条（canonicalization 先于投票·temperature 分层·置信度=票分布·审计留痕·聚合器版本化·超时降级保 p99）·模拟面试五连·金句「方差绞肉机」；自检清单 10 项；Day 145 预告：模型路由与语义缓存（候选：LRU/LFU 或一致性哈希））

**更新记录**：已连续更新 **144** 天

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
