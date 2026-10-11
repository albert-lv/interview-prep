# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2020%20Day%20129%20🛡️-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20129%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 129 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 129 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 129 · 2026-10-11 · Week 20 Day 1 开启 🛡️）
- 🧠 [单词接龙 + Week 20 开启：Agent 安全与对齐工程](week20/2026-10-11-word-ladder-agent-security-intro.md) — 今日算法题「单词接龙」（LC 127）= **最短注入链的算法原型**：起始词 = 良性 prompt·目标词 = 攻击目标·每次改一个字母 = 逐步微调措辞·中间词必须在字典 = 每步都得绕过 guardrail 的合法状态检查·BFS 最短路 = 红队要找的最短攻击链；现场生成邻居 + **入队即标记**防重复入队 O(N·L·26)，wildcard 桶预处理优化；连环问：**双向 BFS（红蓝对抗 = 同一张状态图上的双向 BFS·小侧扩展）**·Word Ladder II 全路径 = **攻击面测绘**（分层 DAG + 前驱集合）·边带权 → Dijkstra = 期望成本最低攻击路径·违规 k 次容量 = 二维状态 BFS（Day 123 同款）·字符奇偶签名预检剪枝；GCG 攻击 = 今日题的连续松弛版（embedding 空间坐标下降搜最短变形）。面试技巧 **Agent 安全与对齐工程总览**：攻击面全景（直接注入/间接注入 = agent 头号天敌/工具层/记忆层/供应链 × 输入-模型-系统三层栈）；注入家族演化史（DAN → 编码越狱 → Crescendo 多轮渐进注入 → GCG 梯度攻击 → 泛化后缀）；confused deputy 与权限最小化；**纵深防御五层**（L1 输入 → L5 运行时·L4 沙箱让攻击终点落在无害区）；**对齐三件套**（RLHF 定口味·Constitutional 定规矩·DPO 做手术·警惕 over-refusal 这个 Goodhart 副产品）；agent 特有安全（不可逆操作闸门·爆炸半径预算·凭证隔离·轨迹级审计复用 Day 128）；红队与评估（**ASR 三轴报表**·红队流水线 = 单词接龙自动化·紫队飞轮 = 红蓝同飞轮）；veRL 连接（对抗训练 = 攻击分布上做 RLHF·Safe RL 约束 = CPO/Lagrangian 预算 vs 惩罚·宪法当 verifier = 安全版 PRM）；金句「蓝队砌墙红队找洞是同一张图两种 BFS·不追攻不破的幻觉·追代价高终点无害被攻破能回流」
- 🎤 面试技巧：今日金句「**LLM 安全的本质是把模型从评论家变成执行者之后，所有输出层的问题都升级成了行动层的事故——chatbot 说错话是公关事件，agent 做错事是资损事件**」

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
> 🔥 **当前状态**：Week 20 进行中 🛡️（Agent 安全与对齐工程，Day 129 已更新），**每日更新从未中断**。

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
agent-tips/   # 每日 Agent 工具技巧(Claude Code / Kimi Code / Windsurf)
memory/       # 进度追踪 & 学习笔记
```

**最新内容**（倒序）：
- 🛡️ [Day 129 — 单词接龙（Word Ladder）+ Week 20 开启：Agent 安全与对齐工程](week20/2026-10-11-word-ladder-agent-security-intro.md)（**最短注入链算法原型**：起始词=良性 prompt·目标词=攻击目标·每步改一个字母=逐步微调·中间词必须合法=绕过 guardrail / BFS 现场生成邻居·入队即标记 O(N·L·26)·wildcard 桶优化 / 连环问：**双向 BFS=红蓝对抗同图搜索**·Ladder II 全路径=攻击面测绘·带权→Dijkstra=期望成本最低攻击·违规 k 次=二维状态 BFS·奇偶签名预检 / 注入家族演化史：DAN→编码→Crescendo→GCG→泛化后缀 / 间接注入=agent 头号天敌·confused deputy / **纵深防御五层**·对齐三件套（RLHF 定口味·Constitutional 定规矩·DPO 做手术·over-refusal 警告）/ agent 特有安全=爆炸半径预算+轨迹审计 / **ASR 三轴报表**·红队流水线=单词接龙自动化·紫队飞轮 / veRL：对抗训练·Safe RL 约束=预算 vs 惩罚 / 金句「蓝队砌墙红队找洞同一张图两种 BFS」）
- 🎉 [Day 128 — 编辑距离（Edit Distance）+ 评估与观测（SWE-bench 与轨迹评估）](week19/2026-10-10-edit-distance-evaluation-observability.md)（**轨迹差异度算法原型**：字符串 = golden trace ↔ 实际轨迹·编辑操作 = 轨迹偏差 / 二维 DP：相等免费对齐·不等 1+min(删/插/替)·O(mn)→滚动 O(min)·扫序与 0-1 背包相反 / 连环问：插删版=m+n−2·LCS（git diff Myers 内核）·对齐回溯·加权=DNA 打分·Wer 词错误率·Hirschberg / **评估五大难**：开放输出·路径多样·环境交互·成本变量·非确定性 / SWE-bench：真实 issue+FAIL_TO_PASS/PASS_TO_PASS 双门·Verified 500·污染三板斧 / **三轴**：成功率分难度·成本均值+P95·可靠性（pass@k 上限 vs pass^k 下限）/ 轨迹评估：终局 vs 过程·步数效率·无效动作率·轨迹相似度=今日题 / 可观测性：trace 落盘·replay 铰链·eval harness=reward 镜像 / **Week 19 七天串讲收官**·金句「不能衡量就不能改进」）
- 🎭 [Day 127 — 分割数组的最大值（Split Array Largest Sum）+ 多智能体编排四种模式](week19/2026-10-09-split-array-multi-agent-orchestration.md)（**多智能体负载均衡算法原型**：元素=任务时长·k 段=k worker·最小化最大子数组和=makespan / 二分答案四件套：下界 max·上界 sum·feasible=≤k 段（至多松弛）·O(n·log S) / 贪心正确性=前缀覆盖反证·「能装就装」 / 连环问：LPT 4/3 近似比·List Scheduling 关键路径·异构加权=k8s 打分·LC 875/1482/1011 / **四模式总表**：主管-工人（星型·验收先于派发·层级 ≤2·委派三件套）·流水线（schema 契约·级联误差 stage 入口校验·换 agent=脑回路差异）·辩论投票（回声室=换人设不叫多样性·真多样性=异构数据源·外部锚）·市场拍卖（成本信号·Vickrey 二价·流拍兜底）/ 三买三税·通信三板斧·六失效速查 / veRL 连接：opponent pool·partial rollout 切分=今日题 / 金句「编排=组织设计·先想任务怎么分再想谁跟谁说话」）
- 🎭 [Day 126 — LFU 缓存（LFU Cache）+ 记忆系统三层设计与失效降级](week19/2026-10-08-lfu-cache-memory-systems.md)（**缓存即记忆：eviction 策略即遗忘曲线**——capacity=工作记忆上限·freq=记忆强度（get=提取练习）·minFreq 淘汰=遗忘执行器·TTL=时效衰减 / 三件套 O(1)：key→节点·freq→双向链表（同频 LRU 序）·minFreq 指针（LRU+频率分桶=LFU）/ 易错：put 已存在不淘汰·新 key 后 min_freq=1·get miss 不动 minFreq / 连环问：LRU 怕污染 vs LFU 怕冷启动→**aging freq 减半=记忆消退**·LRU-K·ARC·W-TinyLFU·TTL 惰性+定期·分布式 Gossip 同步计数器 / **记忆三层模型**：工作=context·情景=向量库（90% 工程在这）·语义=权重·程序性=skill / 四决策：写入评分+纠错记忆最贵·**冲突更新而非追加**·触发式检索+阈值生命线·离线巩固·遗忘三件套 / **MemGPT 虚拟分页**：LLM=CPU·函数调用=缺页中断·换入时机/换出策略/地址翻译 / **降级阶梯**：向量挂→BM25→纯工作记忆·六失效模式（空召回显式标注防幻觉·陈旧 TTL·**记忆中毒=注入持久化载体**·膨胀→aging·污染→rerank）/ 金句「记不住是残疾，忘不了是噩梦」）
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

当前进度：**Week 20 进行中 🛡️ / Day 129**（Week 20 主题：Agent 安全与对齐工程 🛡️ — Day 129 开场日：LC 127 单词接龙 = 最短注入链的算法原型——起始词 = 良性 prompt、目标词 = 攻击目标、每次改一个字母 = 逐步微调、中间词必须合法 = 绕过 guardrail、BFS 最短路 = 红队要找的最短攻击链；现场生成邻居 + 入队即标记 O(N·L·26)，wildcard 桶优化；连环问：双向 BFS = 红蓝对抗同一张状态图上的双向搜索·Word Ladder II 全路径 = 攻击面测绘（分层 DAG + 前驱集合）·边带权 → Dijkstra = 期望成本最低攻击路径·违规 k 次容量 = 二维状态 BFS。面试技巧：Agent 安全与对齐工程总览——攻击面全景（直接注入/间接注入 = agent 头号天敌/工具层/记忆层/供应链 × 输入-模型-系统三层栈）；注入家族演化史（DAN → 编码越狱 → Crescendo 多轮渐进注入 → GCG 梯度攻击 → 泛化后缀）；confused deputy 与权限最小化；纵深防御五层（L1 输入 → L5 运行时，L4 沙箱让攻击终点落在无害区）；对齐三件套（RLHF 定口味·Constitutional 定规矩·DPO 做手术，警惕 over-refusal 的 Goodhart 副产品）；agent 特有安全（不可逆操作闸门·爆炸半径预算·凭证隔离·轨迹级审计复用 Day 128）；红队与评估（ASR 三轴报表·红队流水线 = 单词接龙自动化·紫队飞轮）；veRL 连接（对抗训练 = 攻击分布上做 RLHF·Safe RL 约束 = CPO/Lagrangian 预算 vs 惩罚·宪法当 verifier = 安全版 PRM）；金句「蓝队砌墙红队找洞是同一张图两种 BFS」🛡️）

**更新记录**：已连续更新 **129** 天，每日 20:42 自动推送。

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
