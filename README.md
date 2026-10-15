# 🎯 Interview Prep - 每天一道题,六周拿下大厂 Offer

[![Progress](https://img.shields.io/badge/进度-Week%2020%20Day%20133%20🛡️-blue)](./memory/interview-prep.md)
[![Daily Update](https://img.shields.io/badge/🔥%20每日更新-已连续%20133%20天-success)](https://github.com/albert-lv/interview-prep/commits/main)
[![Last Commit](https://img.shields.io/github/last-commit/albert-lv/interview-prep/main?label=上次更新)](https://github.com/albert-lv/interview-prep/commits/main)
[![Topics](https://img.shields.io/badge/覆盖-算法%20%7C%20OS%20%7C%20网络%20%7C%20系统设计-orange)]()

> **不是收藏夹吃灰,是真的每天都在更新。**
>
> 每天 20:42 自动推送:一道算法题 + 一页面试速查。跟着走,6 周后你会感谢自己。
>
> 🎉 **连续更新 133 天，从未中断！**

---

## 📌 这是什么?

一个 **每天更新、结构清晰、直接可用** 的面试准备仓库。

| 特性 | 说明 |
|---|---|
| 🔄 **每日双更** | 早上 Agent 工具技巧，晚上算法 + 面试考点，**已经连续更新 133 天** |
| 📅 **6 周系统计划** | 不是零散刷题,按主题递进(DP → 数据结构 → 网络 → 系统设计) |
| 🎤 **面试导向** | 每道题带「面试官会怎么问」+「一句话速答」 |
| ✅ **可运行代码** | 不是伪代码,是能直接 `gcc` 或 `go run` 的 |

### 今日更新（Day 133 · 2026-10-15 · Week 20 Day 5 🛡️）
- 🧠 [ddmin 最小化算法（Delta Debugging）+ 对抗样本与越狱防御实战](week20/2026-10-15-ddmin-adversarial-training-sanitization.md) — 今日算法题「ddmin」= **红队归因分析的第一刀**：10 万成功样本先最小化再聚类去重，10 万 → 20 个独立漏洞——没有最小化，聚类被噪声淹没 / 对「子集格」做二分：complement 测试命中就收缩·未命中粒度倍增·字符级终止 = 1-minimal / 最坏 O(k²) 次 test·触发单元小接近线性·LLM 场景必须预算封顶 / 连环问：test 不单调→k=3 多数投票·与二分查找 = 链 vs 格·test 太贵→缓存+AC 预筛+LLM 提议校验（GCG 同范式）·多段输入→HDD 层级化·**1-minimal ≠ 全局最小**·最小化后接 k-means++ 聚类闭环。面试技巧 **对抗样本与越狱防御实战**：总纲 = **攻防经济学——攻击找一个点防御盖一个面**：攻击成本↑+爆炸半径↓+修复闭环快 / **对抗训练** = 攻击样本当 hardest 数据增强加厚边界·数据管道 = ddmin→聚类→每类采样→训练三元组（SFT 负样本/DPO rejected/GRPO 负奖励）·**配比是命门**（只喂攻击 = over-refusal 制造机·双指标护栏）·**训练集上的安全 ≠ 真安全**（held-out 攻击族评泛化）·**自适应攻击者假设**（知道你的防御再测一遍）·天花板 = 防已知范式→必须绑紫队飞轮 / **净化管线**：检测 = 识别攻击 vs **净化 = 让攻击失效**（把身份证照片重画一遍）/ 输入侧三件套：感知归一化（Day 130 复用）·**Paraphrase 防御**（GCG suffix 是分布外信号不是语义·改写保留语义抹掉扰动·改写器异构防被攻）·语义层重构（高敏 L3 专属）/ 三层收尾框架：对抗治已知的病·净化治未知的病·纵深治治不了的病 / veRL：成功轨迹 = 组内最低 reward 自动加权·软惩罚 vs **CPO 硬预算**（防 Goodhart 擦边）·trajectory store 一库三读
- 🎤 面试技巧：今日金句「**对抗训练加厚边界，净化管线洗掉信号，沙箱收敛半径——防御不是让攻击不可能，而是让攻击贵到不划算、小到有边界、快到能闭环**」
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
> 🔥 **当前状态**：Week 20 进行中 🛡️（Agent 安全与对齐工程，Day 133 已更新），**每日更新从未中断**。

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
- 🔴 [Day 133 — ddmin 最小化算法（Delta Debugging）+ 对抗样本与越狱防御实战](week20/2026-10-15-ddmin-adversarial-training-sanitization.md)（**红队归因分析的第一刀**：10 万成功样本先 ddmin 最小化再聚类去重，10 万 → 20 个独立漏洞——没有最小化，聚类被噪声淹没 / 对「子集格」二分：complement 命中收缩·未命中粒度倍增·字符级 = 1-minimal / 最坏 O(k²) 次 test·LLM 场景预算封顶 / 连环问：不单调→多数投票·链 vs 格·太贵→缓存+预筛+LLM 提议（GCG 同范式）·HDD 层级·**1-minimal ≠ minimal** / **对抗训练**：攻击样本 = hardest 数据增强·管道 = ddmin→聚类→训练三元组（SFT/DPO/GRPO）·**配比防 over-refusal**·held-out 攻击族评泛化·**自适应攻击者假设** / **净化管线**：检测 = 识别 vs **净化 = 让攻击失效**·三件套 = 归一化复用·**Paraphrase 防御**（suffix 是分布外信号非语义·改写器异构）·语义重构 / 三层框架：治已知·治未知·治治不了 / veRL：成功轨迹 = 最低 reward 自动加权·**CPO 硬预算**防 Goodhart / 金句「防御不是让攻击不可能，而是贵到不划算、小到有边界、快到能闭环」）
- 🔴 [Day 132 — 访问所有节点的最短路径（Shortest Path Visiting All Nodes）+ 红队自动化平台设计](week20/2026-10-14-shortest-path-visiting-all-nodes-red-team-automation.md)（**覆盖率引导模糊测试的状态搜索骨架**：节点=程序状态·mask=coverage bitmap·种子=(输入,mask)·全覆盖=最小变异代价 / 状态 (node,mask)·O(n²·2ⁿ)·起点同层入队·`|` 不用 `^` / 连环问：状态爆炸→coverage 最大化近似（AFL bitmap）·TSP=重访松弛决定可解性·变异谱系回溯·**含历史状态难双向化**·第一层并行分片·带权=Dijkstra 期望成本 / **红队流水线五段**：变异（语料三层+算子四级）→执行（Day 131 沙箱复用）→**判定**（拒绝≠成功·双指标护栏 ASR↑+完成率不掉）→归因（ddmin+聚类：10 万样本≈20 漏洞）→回流（回归集+训练+GRPO 难例）/ **GCG 骨架**：贪心坐标下降=梯度提议+前向校验+贪心固定·多模型联合 loss=可迁移 / 家族树：GCG·AutoDAN·PAIR·TAP·Crescendo 选型表 / 连环问：三轴报表·红队与评估同流水线·负样本进 rejected 侧·闭源三层打法·人管深度机器管广度·veRL 成功轨迹=免费难例 / 金句「手测 prompt 是抽奖，红队平台是开矿」）
- 🛡️ [Day 131 — 钥匙和房间（Keys and Rooms）+ 沙箱与权限工程](week20/2026-10-13-keys-and-rooms-sandbox-permissions.md)（**权限可达性分析的算法原型**：房间=资源·钥匙=权限·0 号房间=初始权限集·BFS 可达集=横向移动面 / O(V+E)·入队即标记·持有钥匙≠到过房间 / 连环问：权限缺口清单·**最小初始权限集**（外部钥匙贪心+闭包）·带权=Held-Karp·循环继承=三色素描·与 Day 123 迷宫对偶（持有物可逆性）·10⁹ 外存 BFS+bitset / **沙箱谱系**：chroot→jail→容器→gVisor→Kata/**Firecracker microVM**（精简设备模型=减攻击面·~125ms）→Wasm isolate / **权限模型五件套**+**capability vs ACL**（fd 是 capability 本体·ambient authority·confused deputy 解药）/ agent 五道施工线：ephemeral 沙箱（断网 allowlist+墙钟超时）·工具分级 L0~L3·凭证隔离（代持+JIT+蜜罐）·eBPF 网络兜底·确认门防疲劳三件套 / 连环问：逃逸五层纵深·JIT 防权限爬升=因果链校验 / veRL：rollout 可重置+判分器隔离 / 金句「对齐是概率·沙箱是边界·钥匙都该带 TTL」）
- 🛡️ [Day 130 — 多模式匹配检测器（Aho-Corasick 自动机）+ 间接注入与内容-指令分离工程](week20/2026-10-12-ac-automaton-indirect-injection.md)（**注入检测引擎的算法内核**：模式库=注入特征库·一次扫描报全部命中=审计全量过检·扫描代价与模式数无关=运维敢加规则 / Trie 建树+BFS 建 fail+output 合并三件套·O(Σ|p|) 预处理+O(n+z) 扫描 / 连环问：KMP→AC 谱系（next 数组=单模式版 fail）·变体归一化层（NFKC+零宽剥离+leet 映射）·模糊检测两段式（AC 锚点+局部编辑距离）·10⁶ 词库压缩（双数组 Trie·DAWG·Hyperscan SIMD）·热更新=不可变快照+原子切换·流式可断点续传 / **间接注入=agent 时代的 XSS**（SOP↔instruction hierarchy 类比）/ 内容-指令分离四件套（标注隔离·spotlighting·canary 蜜罐·出向动作审计·权限收敛）·闭合标签注入=缓解边界 / **检测器经济学**：拦截栈四层=Day 120 验证器成本谱复用·**误报不对称设计**（输入侧宁漏勿错·出向侧宁错勿漏）/ 产品线速览：Llama Guard 3·Prompt Shields·NeMo Guardrails·**AgentDojo 双指标**（攻击成功率↓+任务完成率不掉=全拒答假安全不算赢）/ veRL：注入攻防数据=GRPO 难例·CPO 约束优于裸惩罚 / 金句「内容-指令分离是准入证·注入一定会成功所以爆炸半径必须有限」）

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

当前进度：**Week 20 进行中 🛡️ / Day 133**（Week 20 主题：Agent 安全与对齐工程 🛡️ — Day 133：ddmin 最小化算法（Delta Debugging）= 红队归因分析的第一刀——10 万成功样本先最小化再聚类去重，10 万 → 20 个独立漏洞；对「子集格」二分：complement 测试命中收缩、未命中粒度倍增、字符级终止 = 1-minimal；最坏 O(k²) 次 test、触发单元小接近线性、LLM 场景必须预算封顶；连环问：test 不单调 → k=3 多数投票·与二分查找 = 链 vs 格·test 太贵 → 缓存+AC 预筛+LLM 提议校验（GCG 同范式）·多段输入 → HDD 层级化·1-minimal ≠ 全局最小（随机重启补救）·最小化后接 k-means++ 聚类闭环。面试技巧：对抗样本与越狱防御实战——总纲 = 攻防经济学（攻击找一个点、防御盖一个面：攻击成本↑+爆炸半径↓+修复闭环快）；对抗训练 = 攻击样本当 hardest 数据增强加厚边界，数据管道 = ddmin→聚类→每类采样→训练三元组（SFT 负样本/DPO rejected/GRPO 负奖励），配比是命门（只喂攻击 = over-refusal 制造机、双指标护栏），训练集上的安全 ≠ 真安全（held-out 攻击族评泛化），自适应攻击者假设（知道你的防御再测一遍），天花板 = 防已知范式不防新范式 → 绑紫队飞轮；净化管线：检测 = 识别攻击 vs 净化 = 让攻击失效，输入侧三件套 = 感知归一化（Day 130 复用）/Paraphrase 防御（GCG suffix 是分布外信号不是语义、改写保留语义抹掉扰动、改写器异构防被攻）/语义层重构（高敏 L3 专属）；三层收尾框架：对抗治已知的病、净化治未知的病、纵深治治不了的病；veRL：成功轨迹 = 组内最低 reward 自动加权，软惩罚 vs CPO 硬预算（防 Goodhart 擦边），trajectory store 一库三读；自检清单 10 项；金句「对抗训练加厚边界，净化管线洗掉信号，沙箱收敛半径——防御不是让攻击不可能，而是让攻击贵到不划算、小到有边界、快到能闭环」🛡️）

**更新记录**：已连续更新 **133** 天

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
