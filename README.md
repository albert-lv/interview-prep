# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2022%20Day%20145-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20145%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 145 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 145 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 145 · 2026-10-27 · Week 22 Day 4 🎯：LRU/LFU 缓存设计（LC 146/460 · 哈希链表 O(1) get/put）+ 一致性哈希扩展 + 模型路由与语义缓存——RouteLLM 分类器路由 / 缓存命中率经济学）
- 🎯 [LRU / LFU 缓存（LC 146 / LC 460 · 哈希表 + 双向链表 O(1) get/put）+ 一致性哈希（虚拟节点 · 搬移 1/N）+ 模型路由与语义缓存](week22/2026-10-27-model-routing-semantic-caching.md) — 🔥 **本周时事锚点 = Kimi K3.1 Pro 价目表（10/24 发布持续发酵）**（缓存命中输入 **¥1.3 vs 未命中 ¥13/百万 = 官方 1/10 价**·K3 系编码负载命中率常态 90%+·90% 命中 = 输入侧 **0.167× ≈ 83% off**）× **Highspeed 档（10/24）**（同权重 2~3× 速度 2× 价 = 「买速度」产品化·路由第三决策轴）× **Hermes 官方指南第一军规（10/25）**（「Keep the system prompt stable——缓存靠重复前缀，别中途改 prompt 顶部」= canonicalization 官方背书）× **K3.1 权重 11/4 前开源确认（今日 blog 更新）**（Day-0 vLLM 适配将重演·自建团队命中率进架构评审）× **vLLM v0.32.0（10/18）vs SGLang v0.5.22（10/19）**（RadixAttention 前缀树 vs PagedAttention block 复用·前缀缓存军备竞赛·缓存从特性变架构主角）× **托管商实测差距**（同 K3：Inco 251 tok/s vs 官方 42 tok/s·差距大半来自前缀复用率——**模型相同 serving 不同性能差 6 倍**）× cache_salt CVE-2025-46570 背景音（缓存键 = 信任边界）— 算法题「LRU/LFU」= **语义缓存的算法底座**（哈希表+双向链表双向索引·LRU 三坑：更新续命/淘汰同步删 map/cap=0·LFU 频次→LRU 链两级索引·为什么堆不对·LRU vs LFU 选型=重尾热点 vs 时间漂移·TinyLFU 准入与逐出分离·一致性哈希 + 虚拟节点 O(log N)·KV block 分布式存储同构）/ 面试技巧：请求生命周期五决策点框架（缓存查重→路由→模型内路由→prefix 复用→写回）·三层缓存表（**语义=答过没/前缀=开头算过没/供应商=黑盒 1/10 价**）·canonicalization 六条军规（官方钦点第一条）·路由三档（规则/RouteLLM 分类器——cost-weighted 阈值是路由系统的学习率/cascade 级联）·阈值滞回带防体验跳变·可靠性五连（雪崩加抖动/穿透缓存空值/击穿 singleflight/抖动滞回/降级铁律=省钱路径可挂主路径不可挂）·缓存安全三面（跨租户串答/时序侧信道/缓存投毒）·模拟面试五连·金句「命中率是新的 QPS，缓存命中是全系统最便宜的算力」）
- 🎤 面试技巧：今日金句「**推理经济学只有三条：命中率是新的 QPS，路由是新的模型选型，缓存命中是全系统最便宜的算力。路由决定一个请求值多少钱，缓存决定它要不要花钱——把这两件事做对的系统，模型慢一档也能赢。**」
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
- ⚡ [Day 145 — LRU / LFU 缓存设计（LC 146 / LC 460 · 哈希链表 O(1) get/put）+ 一致性哈希 + 模型路由与语义缓存](week22/2026-10-27-model-routing-semantic-caching.md)（**哈希表 + 双向链表双向索引**·LRU 三坑（更新续命/淘汰同步删 map/cap=0）·LFU「频次→LRU 链」两级索引·为什么堆不对（O(log n)+无法按时间二次淘汰）·LRU vs LFU 选型=重尾热点 vs 时间漂移·TinyLFU 准入与逐出分离·一致性哈希+虚拟节点搬移 1/N·KV block 分布式存储同构 / 热点：**K3.1 价目表 = 缓存经济学官方教材**（10/24 持续发酵·缓存命中 ¥1.3 vs 未命中 ¥13 = **官方 1/10 价**·编码负载命中率常态 90%+·90% 命中=输入侧 0.167×≈83% off）·**Highspeed 档**（同权重 2~3× 速度 2× 价=「买速度」产品化·路由第三决策轴）·**Hermes 官方第一军规（10/25）**（system prompt 稳定=canonicalization 钦点）·K3.1 权重 11/4 前开源确认（今日 blog）·vLLM v0.32.0 vs SGLang v0.5.22 前缀缓存军备竞赛（RadixAttention vs PagedAttention）·同 K3 托管商 251 vs 42 tok/s=前缀复用率定性能 / 请求五决策点框架·三层缓存表（**语义=答过没·前缀=开头算过没·供应商=黑盒**）·canonicalization 六军规·路由三档（RouteLLM 分类器 cost-weighted 阈值=路由系统的学习率·cascade 级联）·滞回带防体验跳变·可靠性五连（雪崩/穿透/击穿 singleflight/降级铁律=省钱路径可挂主路径不可挂）·缓存安全三面（跨租户串答/时序侧信道/缓存投毒）·模拟面试五连·金句「命中率是新的 QPS，缓存命中是全系统最便宜的算力」）
- ⚡ [Day 144 — 蓄水池抽样（LC 398/382 · 无限流无偏均匀采样）+ 测试时扩展：多数投票 / 自洽性 / 自适应采样 / 投票失效边界](week22/2026-10-26-test-time-scaling-reservoir-sampling.md)（**算法 R 三行代码 + k/n 归纳证明**（进入概率×留存概率连乘）·O(n)/O(k) 单遍·进阶八连：Algorithm L 指数跳跃·A-Res 加权 rand^(1/w)·k=1 链表特例·分布式「流量不等不能合并」坑·top-k 对偶 sketch·fpc 置信区间·动态采样率控制回路 / 热点：**Kimi K3.1 Pro（10/24·HN 947 分）**（3T 开源·Terminal-Bench 65.7% 超 GPT-6·AA 第 4·后训练 RL 放大思考能力）·**Highspeed 2~3×**（买速度=第三轴）·**Cerebras 账本（10/25）**（线性注意力无 KV cache·架构被晶圆定价）·**Self-Consistency Is Losing Its Edge**（15× 算力换 1.6%·>15 路径反降·冗余机理）·**When SC Backfires**（GPQA 56.6% 问题更差）·**RUVERBENCH**（3~5 票饱和·消方差消不了偏差）·**Blend-ASC**（6.8× 样本·流式早停）·**When To Solve When To Verify**（SC 胜 GenRM 8×）·**GVS5H**（五 27B 协作匹配 Fable 5）/ 三条 Scaling 轴框架·采样家族谱系·**bias-variance 统一理论**（同家族=同一盲点的 k 次复印）·预算经济学五连·工程清单六条（canonicalization/temperature 分层/置信度=票分布/审计留痕/聚合器版本化/超时降级）·模拟面试五连·金句「方差绞肉机」）
- ⚡ [Day 143 — 我的日程安排表 III（LC 732 · 懒标记线段树 = 在线区间加 + 峰值并发）+ KV Cache 管理深潜：PagedAttention / RadixAttention / 抢占换出 / MLA / 安全五连](week22/2026-10-25-kv-cache-deep-dive.md)（**Day 142 拼车离线版的在线对偶**：懒标记线段树 O(log 10⁹)/次·动态开点·「完全覆盖记账·部分覆盖先结账」·边界计数备选·BIT 不保最值决策表 / 热点：**ACL 2026 Safe-CAM（10/24 昨天）**（KV 压缩×越狱双刃剑：意外鲁棒性 vs LLaMA-2 ASR 0→34%·压缩率=安全变量）·**KV Cache Hijacking**（复用劫持）·**KV-Cloak NDSS'26**（KV 明文三攻击·<10% 防御开销）·**cache_salt CVE-2025-46570**（前缀缓存侧信道）·vLLM v0.32.0 + SGLang v0.5.22（Rust 前缀缓存核心默认）·DeepInfer 不含 KV 层（护城河在调度）·ModelSphere 三级 KV / KV=主存类比总纲·PagedAttention 七连·RadixAttention vs hash·抢占三变量·MLA 低秩 latent·分层卸载·**安全五连全是 10 月出处**·模拟面试五连·金句「开始计入安全学分」）
- ⚡ [Day 142 — 拼车（LC 1094 · 差分数组 = token 预算时间线校核）+ Inference 系统工程总览：Agent 时代的推理基础设施](week22/2026-10-24-car-pooling-inference-infra-overview.md)（**Week 22 开题 = 全周地图**：差分+扫掠 O(n+T)·与 Day 85 会议室 II 对偶（判合法用差分·求最优用堆）·双预算=token×并发·Fermi 155 万亿 token/天≈18 亿 token/s / 热点：**DeepSeek 开源 DeepInfer（10/20）**（155 万亿 token/天/集群·$38/GPU·h·兼容昇腾 950）·**Gartner 推理支出 $233 亿首超训练**·信通院 70.5%·**Artificial Analysis：缓存读写占 58.8%**·MiniMax 太空兔灰度 200T token·CoreWeave Vera Rubin 4.8×·**LMCache CVE-2026-105192（9.8）缓存层=信任边界**·AI 短剧 55% 口径之争 / serving 四代演进（Orca→vLLM→P/D 分离·<8 卡别拆）·KV 显存账·prefix caching 三类红利·多级 KV（Mooncake/FlexKV/MLA）·路由三档+语义缓存·三轴配额护栏·成本公式·组合题两连·金句「搬运生意」）
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

当前进度：**Week 22 进行中 ⚡ / Day 145**（Week 22 Day 4：模型路由与语义缓存。Day 145 LRU/LFU 缓存 = LC 146/460（语义缓存的算法底座：哈希表+双向链表双向索引 O(1) get/put·LRU 三坑：更新续命/淘汰同步删 map/cap=0·LFU「频次→LRU 链」两级索引·为什么堆不对·LRU vs LFU 选型=重尾热点 vs 时间漂移·TinyLFU 准入与逐出分离的工业答案·一致性哈希+虚拟节点搬移 1/N·KV block 分布式存储同构）·面试技巧：请求生命周期五决策点框架（语义缓存查重→路由→模型内路由→prefix 复用→写回）·三层缓存表（语义缓存=答过没[embedding 近邻]/前缀缓存=开头算过没[token 逐位相同]/供应商缓存=黑盒 1/10 价·命中率从 L1→L3 递增但省的钱递减）·canonicalization 六军规（K3.1 Hermes 官方钦点第一条：system prompt 保持稳定）·缓存命中率经济学算账（K3.1 价目表：命中 ¥1.3 vs 未命中 ¥13·90% 命中=输入 0.167×≈83% off·两级命中率相乘才是真实成本·输入占比 3:1 时总账单 ≈38% off）·路由三档（规则按元数据零延迟/RouteLLM 分类器=小模型输出弱模型搞定概率 p·训练信号=强相对打分差或线上 A/B 复核·cost-weighted 阈值=路由系统的学习率/cascade 级联=让弱模型自我评估·滞回带防阈值边缘体验跳变）·Highspeed/Ultrafast=同权重不同档位的第三决策轴（质量-延迟-成本三维路由表）·serving 引擎选型因果链（流量形状→缓存形状→引擎：多轮 agent 多→RadixAttention 前缀树收益大）·可靠性五连（雪崩 TTL 抖动/singleflight/穿透缓存空值/抖动滞回/降级铁律=省钱路径可挂、主路径不可挂）·缓存安全三面（键漏字段=跨租户串答·命中时序差=侧信道[cache_salt CVE-2025-46570 一族]·语义缓存投毒）·模拟面试五连·金句「命中率是新的 QPS，路由是新的模型选型，缓存命中是全系统最便宜的算力」；自检清单 10 项；Day 146 预告：网关预算护栏（token 配额三轴·429/402 语义·成本异常=行为异常·候选：令牌桶/漏桶或区间调度）））

**更新记录**：已连续更新 **145** 天

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
