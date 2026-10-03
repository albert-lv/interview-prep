# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2018%20Day%20121%20🏆-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20121%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 121 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 120 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 121 · 2026-10-03 · Week 18 Day 7 🏆 收官日）
- 🏆 [24 点游戏 + Week 18 推理模型与 Test-Time Compute 综合复习（主线公式 · 10 道连环问通关 · 24 点×test-time compute 同构收官）](week18/2026-10-03-24-game-week18-review.md) — 收官算法题「24 点」是 ToT 的成名战场（Day 117 callback）：**减而治之**——每次取有序对枚举 6 种运算放回递归，天然枚举所有括号结构（4 张牌 3 层合并，括号被递归隐式展开），浮点 ε 比较 + 除零保护 + 排序记忆化；核心同构：**每棵递归子树 = 推理树节点，每条合并路径 = CoT，`==24` 终局检查 = ORM**——搜索空间小 + 验证器便宜 → 暴力搜索就是推理；5 张牌以上才需要剪枝评估（PRM 雏形）、目标分解先验（pUCT 的 P(s,a)）。面试技巧 Week 18 综合复习：**一条主线公式「正确率 = 搜索 × 验证 × 算力分配」串六天**——o1 打开 Scaling Law 第二根轴（Day 115），预算花在搜索策略（BoN/Self-Refine/ToT-MCTS，Day 117/118）、验证器选型（规则→执行→PRM→LLM-Judge 成本谱，Day 116/120）、compute-optimal 分配（Day 115）+ 系统侧账本（KV cache/prefix caching/PD 分离，Day 119）；10 道高频连环问通关（o1 本质/验证比生成容易/1−(1−p)^N/PRM 数据三路/MCTS 四步+LLM 化四改造/Self-Consistency 失效条件/KV 显存口算+GRPO 白嫖/五大偏差/Goodhart 四幕剧/系统设计四步框架）；收官金句「**答案能被便宜验证的问题，暴力搜索就是推理；不能被便宜验证的问题，才需要 verifier 经济学**」
- 🎤 面试技巧：收尾话术模板一段话覆盖全周（两根轴→三问→三策略→四类验证器→算力分配→系统账本→经济学），Week 18 完结撒花 🎉

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
> 🔥 **当前状态**：Week 18 已完结 🏆（推理模型与 Test-Time Compute，Day 115 解数独 + Test-Time Compute 总览 ✅ → Day 121 24 点 + 综合复习收官 ✅），Week 17 已完结，**每日更新从未中断**。

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
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- 🏆 [Day 121 — 24 点游戏（24 Game）+ Week 18 综合复习：推理模型与 Test-Time Compute](week18/2026-10-03-24-game-week18-review.md)（减而治之：有序对 6 运算枚举·括号被递归结构隐式枚举·浮点 ε+除零保护+排序记忆化 / 核心同构：递归子树=推理树节点·合并路径=CoT·`==24`=ORM·**搜索空间小+验证器便宜→暴力搜索就是推理** / 5 张牌以上要剪枝评估=PRM 雏形·目标分解先验=pUCT P(s,a) / 主线公式「正确率=搜索×验证×算力分配」串六天：o1 两根轴·搜索三策略·验证器四类成本谱·compute-optimal·系统账本 / 10 道连环问通关：o1 本质·验证比生成容易·1−(1−p)^N·PRM 数据三路·MCTS 四步+LLM 化·Self-Consistency 失效·KV 显存口算+GRPO 白嫖·五偏差·Goodhart 四幕剧·系统设计四步 / 收官金句「答案能被便宜验证的问题暴力搜索就是推理」·Week 18 完结 🎉）
- 🔬 [Day 120 — 验证二叉搜索树（Validate BST）+ Verifier 生态与 LLM-as-Judge](week18/2026-10-02-validate-bst-verifier-ecosystem-llm-as-judge.md)（局部检查必翻车：`[5,4,6,null,null,3,7]` 反例·3 小于祖先 5 藏在 6 的右子树 / 值域递归 `validate(node,low,high)` 把全局不变量路径化 · 中序严格递增等价判定 O(n)/O(h · 哨兵 long 防 int 溢出 / Recover BST 两次逆序对 · 第 k 小 · 最大 BST 子树 · 10⁹ 节点并行验证值域随任务序列化 / **局部检查=语法验证 · 值域传递=语义验证：验证器档次取决于携带多少上下文** / Verifier 全家福：规则验语法→执行验行为→PRM 验过程→LLM-Judge 验语义·越贵越易被 Goodhart / 三范式：pointwise 分数通胀 · pairwise 稳必须 order swap · reference-guided 有锚 / 五偏差：位置·冗长·自我偏好·格式谄媚·分数漂移+各自缓解 / MT-Bench：GPT-4 judge 与人类一致 85%+·安全与长链验证场景失效 / Goodhart 四幕剧：静态→训练信号(RLAIF)→政策讨好→judge 失效 / 修复三板斧：refresh·多 judge 集成·held-out+人类校准 / judge 工程 checklist 十条 · 路由原则=每题配最便宜的够用验证器 veRL RewardWorker 落地）
- 🔬 [Day 119 — 最长重复子数组（Maximum Length of Repeated Subarray）+ 长上下文与推理系统工程（KV Cache · Prefix Caching · 延迟-质量权衡）](week18/2026-10-01-maximum-length-of-repeated-subarray-inference-systems.md)（LCS 连续版对偶：`dp[i][j]` = 以 i-1/j-1 结尾的公共**后缀**长度，不匹配**清零**而非继承 = 与子序列唯一差别 / 滚动数组 j 倒序（与 0-1 背包同因）/ 进阶：二分长度+滚动哈希 O((n+m)log min) / 核心隐喻：DP 折叠重叠子结构 ↔ Prefix Caching 折叠共享前缀·Day 1 闭环 / KV 显存公式 2×层×KV头×头维×字节×长度（7B fp16≈512KB/token·4K≈2GB·GQA 砍头）/ PagedAttention = OS 虚拟内存：16-token 块+block table+按需+COW 碎片<4% / Prefix caching 块级内容哈希命中跳过 prefill·GRPO prefill O(G·L)→O(L) 省 30~60%·多轮对话白嫖 / PD 分离：prefill 算力型 ∥ decode 带宽型·KV 按层 RDMA / 长上下文三座大山 O(L²)·O(L)·O(L) 与登山杖 / 延迟-质量交换所：serving 买延迟（纯赚三件套）↔ test-time 买质量（核心汇率））
- 🔬 [Day 118 — 爬楼梯的最少成本（Min Cost Climbing Stairs）+ 自我验证与反思（Self-Consistency / Self-Refine / 验证者悖论）](week18/2026-09-30-min-cost-climbing-stairs-self-verification.md)（Day 1 对偶回归：同一状态机计数加法 vs 最优取 min / `f(i)=cost[i]+min(f(i-1),f(i-2))` 初值踏上付费答案登顶免费 / k 级扩展 = 滑动窗口最小值+单调队列 O(n) / 暴力枚举 2ⁿ 爬法 = 采样全集投票·DP 折叠成 O(n) 精确计算「能 DP 别投票」 / Self-Consistency 独立性假设+1−(1−p)^N+系统性错误一致地错 / 改进：verifier 加权·Early-Stopping 省 ~40% / Kamoi 2024 冷水：intrinsic self-correction GSM8K 95%→82.9% / 验证者悖论三段式+三出路：外包解耦·RL 涌现 R1 实证·rStar 互证 / Reflexion verbal RL·HumanEval 91% 档 / CoVe 四步独立作答防锚定 / Self-Rewarding 自评→DPO 迭代·分布坍缩风险）
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

当前进度：**Week 18 完结 🏆 / Day 121**（Week 18 主题：推理模型与 Test-Time Compute 🔬 — Day 121 收官：24 点游戏 = ToT 成名战场，减而治之有序对 6 运算枚举天然覆盖全部括号结构，核心同构「递归子树=推理树节点·合并路径=CoT·`==24`=ORM」——搜索空间小+验证器便宜→暴力搜索就是推理；面试技巧 Week 18 综合复习——主线公式「正确率=搜索×验证×算力分配」串六天：o1 打开 Scaling Law 第二根轴、搜索三策略（BoN/Self-Refine/ToT-MCTS）、验证器四类成本谱（规则→执行→PRM→LLM-Judge 越贵越易被 Goodhart）、compute-optimal 按难度分配、系统侧账本（KV cache/prefix caching/PD 分离，GRPO 组共享前缀白嫖 30~60%）；10 道连环问通关（o1 本质/验证比生成容易/1−(1−p)^N/PRM 数据三路/MCTS 四步+LLM 化/Self-Consistency 失效条件/KV 显存口算/五大偏差/Goodhart 四幕剧+修复三板斧/系统设计四步框架）；收官金句「答案能被便宜验证的问题，暴力搜索就是推理；不能被便宜验证的问题，才需要 verifier 经济学」🎉）

**更新记录**：已连续更新 **121** 天，每日 20:42 自动推送。

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
