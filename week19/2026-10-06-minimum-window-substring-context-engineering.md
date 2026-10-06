# Day 124 — 最小覆盖子串 + Context Engineering 与长上下文管理

> **Week 19 · Day 3** 🎭 Agentic 系统与智能体工程
> 算法题：最小覆盖子串（Minimum Window Substring, LC 76）· 滑动窗口的"上下文压缩"原型
> 面试主题：Context Engineering——context 是 agent 的新显存

---

## 一、今日算法题：最小覆盖子串（Minimum Window Substring）

### 题目

给你两个字符串 `s` 和 `t`，返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""`。

```
示例 1:
输入: s = "ADOBECODEBANC", t = "ABC"
输出: "BANC"
解释: 最小子串 "BANC" 包含 A、B、C 各一次。

示例 2:
输入: s = "a", t = "aa"
输出: ""
解释: s 中只有一个 a，无法覆盖 t 的两个 a。
```

**约束**：`1 <= s.length, t.length <= 10⁵`，字符是字母。

### 为什么今天选这道题（主题同构）

Context Engineering 的核心动作就是：**窗口随任务不断膨胀（append-only），你必须丢信息才能不爆（eviction），丢多少取决于"最小充分覆盖"原则**。

- `r++` 追加字符 = agent 循环里不断 append 的 observation / tool output，上下文只增不减
- `l++` 收缩窗口 = compaction（压缩、裁剪、驱逐）
- `need 全覆盖` 的判定 = 保留的信息必须仍然支撑完成任务（任务相关性 coverage）
- "对每个 r 第一次达到覆盖就收缩" = 贪心最优——**右端点固定时，更短的窗口不会更差**

这就是 Sliding Window Compaction 的算法骨架：不是无脑截断（截断丢需求），而是"满足 coverage 前提下尽量短"。

### 思路：滑动窗口 + 欠账计数

**关键洞察**：合法性判定（t 的每个字符都被覆盖）必须 O(1)，否则整个算法退化。

1. 用 `need[128]` 记录 t 的字符需求（负债表），用 `cnt[128]` 记录当前窗口的持有量
2. 用 `missing` 记录**还没还清的字符种类数**（不是总次数，是种类）——这是 O(1) 合法判定的命门
3. `r` 扩张：进入窗口的字符若 `cnt < need`，则 `missing--`；然后 `cnt++`
4. 当 `missing == 0`（覆盖达成），开始收缩 `l`：
   - 若 `cnt[s[l]] > need[s[l]]`（冗余，丢了不破坏覆盖），`l++` 继续丢
   - 否则这是"必要字符"，停手，更新最优答案
5. 然后 `l++` 迈出必要字符、`missing++`，回到扩张阶段

**易错点三连**：
- `missing` 数的是**种类**不是次数——两个 `a` 只要 `cnt[a] >= need[a]` 就还清这个种类了
- t 中重复的字符按需求计数（`t="aa"` 时 need[a]=2），不是只记存在性
- 收缩时的最优记录要在"刚刚覆盖"的那一刻做，别把必要字符也丢了

### 代码（Python）

```python
def min_window(s: str, t: str) -> str:
    need = [0] * 128          # t 的需求（负债表）
    for c in t:
        need[ord(c)] += 1

    cnt = [0] * 128           # 当前窗口持有量
    missing = 0               # 未还清的字符【种类】数
    for i in range(128):
        if need[i] > 0:
            missing += 1

    l = 0
    best_len = float('inf')
    best_l = 0
    for r in range(len(s)):
        rc = ord(s[r])
        cnt[rc] += 1
        if need[rc] > 0 and cnt[rc] == need[rc]:
            missing -= 1      # 这个种类刚好还清

        while missing == 0:   # 覆盖达成，开始 compaction
            if r - l + 1 < best_len:
                best_len = r - l + 1
                best_l = l
            lc = ord(s[l])
            if need[lc] > 0 and cnt[lc] == need[lc]:
                missing += 1  # 必要字符不能丢，收缩到此为止
            cnt[lc] -= 1
            l += 1

    return s[best_l:best_l + best_len] if best_len != float('inf') else ""
```

Go 版本（面试现场加分项——数组当 map，比 map[int]int 快一个数量级）：

```go
func minWindow(s string, t string) string {
    need := [128]int{}
    for i := 0; i < len(t); i++ { need[t[i]]++ }
    cnt := [128]int{}
    missing, l := 0, 0
    for i := 0; i < 128; i++ { if need[i] > 0 { missing++ } }
    bestLen, bestL := math.MaxInt32, 0
    for r := 0; r < len(s); r++ {
        c := s[r]
        cnt[c]++
        if need[c] > 0 && cnt[c] == need[c] { missing-- }
        for missing == 0 {
            if r-l+1 < bestLen { bestLen, bestL = r-l+1, l }
            cl := s[l]
            if need[cl] > 0 && cnt[cl] == need[cl] { missing++ }
            cnt[cl]--
            l++
        }
    }
    if bestLen == math.MaxInt32 { return "" }
    return s[bestL : bestL+bestLen]
}
```

### 复杂度

- **时间 O(m + n)**：r 和 l 都各自最多走 n 步，每个字符被访问常数次（双指针不回退的均摊分析）
- **空间 O(k)**：k 为字符集大小（字母表为 128，故实际是 O(1)）

### 连环问（面试官会怎么追问）

**Q1：能不能用一次遍历的"先找包含再收缩"？**
可以，但本质还是双指针。常见写法是先扫一遍找覆盖区间再收缩，两遍扫不如标准双指针均摊一遍优雅。

**Q2：如果 t 里有 10⁶ 种不同字符（Unicode 全集），`need` 数组开不下怎么办？**
换哈希表 `map[rune]int`。但**判定合法性的 `missing` 计数逻辑不变**——这是与数据结构无关的核心。追问点：`missing` 必须数种类，数总次数会被重复字符污染。

**Q3：数据流版本——s 是无限的字符流，在线返回当前最小覆盖窗口？**
单调失效：流上的窗口右端点只进不退，维护 `need/cnt/missing` 的同时记录当前最优。**无法收缩已流过的左端点**，只能记录最优快照；若要实时返回"以当前时刻结尾"的最小覆盖，需要在 missing 归零时用队列记下各字符位置，直接跳到必要的最左位置。

**Q4：变体——覆盖 t 的每个字符【至少其出现次数】改成【字符种类存在即可】？**
`need` 全部置 1，`missing` = t 的去重种类数。逻辑完全同构，说明算法的核心引擎是"种类级负债表"。

**Q5：变体——s 中找最短的子串，使得它是 t 的某个排列的超集？**
即 t 的 anagram 覆盖，等价于本题。若允许欠账（窗口内比 need 多某些字符但总长 == len(t)），固定窗口长度为 len(t) 滑一遍，O(n·k) 比较或用 128 维计数数组 O(n)。

**Q6：与 Context Compaction 的映射再具体一点？**
- 把 `need` 看作"任务成功所必需的信息集合"，窗口是"当前上下文"
- `missing==0` 才能收缩 = 压缩不能破坏任务可完成性（**有损压缩的下界=任务 coverage**）
- `cnt[lc] == need[lc]` 的必要字符 = 不可驱逐信息（指令、目标、工具 schema），冗余的才能丢
- 这就是结构化压缩（structured compaction） vs 无脑截断（truncation）的本质差别

**Q7：证明贪心正确性——为什么每个 r 第一次覆盖时就收缩是对的？**
右端点 r 固定时，任何以 r 结尾的合法窗口，其左端点越大（窗口越短）不劣于左端点小的。因为覆盖判定只要求"持有 ≥ 需求"，丢冗余字符不破坏合法性。故每个 r 只需检查最短的合法窗口，全局最优必被枚举到。

**Q8：再进一步——如果丢字符有代价（丢第 i 个字符损失 w_i），求"覆盖 t 且总损失最小"的窗口？**
不再是长度最优化。两步：先求所有合法窗口（双指针），再在合法窗口内对"损失"求极值——但窗口数 O(n²) 退化。**正解是转化**：损失可建模为"保留价值最大"，变成带覆盖约束的最优化，可用分治 + 单调队列或 0-1 背包思想（窗口价值 = Σ保留 w − Σ丢弃 w 仅在窗口外损失），面试里答出"转化为约束最优化并放弃双指针"即可得分。实际工程对应：**压缩不是免费的，summary 成本/信息损失要进账本**（呼应 Day 119 延迟-质量交换所）。

---

## 二、面试技巧：Context Engineering 与长上下文管理

### 0. 一句话定位

> **Context 是 agent 的新显存：写代码要管内存，写 agent 要管上下文。**
> —— 这句话本身就值一次面试的开场。

"Context Engineering" 这个词在 2025 年被 Anthropic 的工程博客定型：**"the delicate art of filling the context window with the right information"**——往窗口里放什么、放多少、按什么顺序放、什么时候扔，是一等公民的工程学科，而不是 prompt 的附属品。

### 1. 为什么它是独立学科：三大失效机制

**① Context Rot（上下文腐烂）**
上下文不是"能塞下就无损"。长上下文中模型的注意力会被稀释，且腐烂是多机制的：
- **指令冲突**：旧指令、历史轨迹里的错误假设与新指令打架，模型可能遵循过期指令（位置越靠前越容易被"遗忘"）
- **Lost in the Middle**（Liu et al. 2023）：信息位于上下文中部时召回率显著低于两端——放资料有位置策略
- **Error compounding 的语境版**：早期 tool 调用失败留下的负面痕迹会持续污染后续决策（与 Day 123 ReAct 的 error compounding 合流）
- **干扰信号密度**：10 万 token 里掺 5% 的无关历史，相对无害；但 50% 的无关历史会把相关信息的"信噪比"压到检索失败区

**② 成本结构**
Context 直接烧钱+烧延迟：
- 每轮请求全量重传历史（除 prompt caching 外），**输入 token 是乘数不是加数**——上下文涨 2 倍，每轮调用的成本和 TTFT 都涨 ~2 倍
- Tool schema 是隐形大户：一个 agent 挂 20 个工具定义，可能稳定吃掉数千 token——比很多"正文"还贵
- 多智能体场景（Day 127 预告）：每个子 agent 的全量复制 = 上下文成本的乘法爆炸

**③ 并发与一致性**
单轮对话的上下文是串行 append；agent 带并行工具调用、子 agent 汇报时，上下文成为**共享可变状态**——写入冲突、顺序敏感、部分失败，是分布式系统问题的微缩版（Week 16 callback：context 也需要"一致性模型"）。

### 2. 四种主流策略总表（Anthropic 官方框架 + 工程实践）

| 策略 | 机制 | 保真度 | 成本 | 适用场景 | 致命伤 |
|---|---|---|---|---|---|
| **截断 Truncation** | 从头/从尾砍掉旧消息 | 低 | 极低 | 短期会话、状态可外部重建 | 丢掉的可能是关键事实（"刚开始定的目标"恰恰在最老的消息里） |
| **滑动窗口** | 只保留最近 K 轮/K token | 中 | 低 | 对话型助手、近期相关性强的任务 | 与最小覆盖子串同构——盲目截断可能丢掉 need 集合 |
| **摘要压缩 Summarization** | LLM 把历史压成 summary | 中 | 中（要调用模型） | 长任务中途、不可重建的历史 | 有损+摘要本身幻觉+压缩不可逆地丢失细节 |
| **结构化压缩 Structured Compaction** | 按类型裁剪：丢 tool output 保留 tool call、丢图片留描述 | 高 | 低 | Coding agent、computer use | 需要精确的"信息类型↔重要性"先验 |

生产系统的真相又是**叠加态**（Day 123 的规律再次出现）：
- Claude Code 的 `/compact` = 摘要压缩；自动触发的上下文告警 = 滑动窗口兜底
- Computer Use 类系统对旧截图做 **tool result elision**（只留最近 N 张，旧的直接丢弃）= 结构化压缩
- 会话开始时重读 CLAUDE.md / 项目文档 = **重启式截断 + 外部状态重建**

### 3. 结构性对策：外部化（Sub-agent 与文件）

Anthropic 博客的核心结论之一：**能不进上下文就不进上下文**。

- **Sub-agent 架构**：把子任务连同它需要的全部上下文一起"打包"交给子 agent，子 agent 在自己的上下文里干完，只把**最终结论**回传主上下文。主上下文的增量从"子任务全过程（10⁵ token）"降到"一份报告（10³ token）"——这就是最小覆盖子串的工程版：**coverage 用结果满足，过程不进窗口**。
- **文件系统外置**：大段产出写文件（代码、报告、数据），上下文里只留文件路径+一句话摘要。需要时再 `read` 回来——用随机访问换线性存储。
- **Write-based memory**：与其塞 100 条历史对话，不如维护一个滚动更新的 `state.md`（当前目标/已完成/下一步/关键决策），每轮重写而非追加。这是把 append-only 改成 read-modify-write，压缩从"事后补救"变成"持续保活"。

记忆系统三层设计（Day 126 预告）将展开：short-term buffer / working context / long-term store，本质就是缓存层级——和 CPU 缓存、KV cache 是同一个问题的三个化身。

### 4. Prompt Caching：上下文工程的"零存整取"

工程侧必须知道的账本项：
- 缓存命中要求**前缀严格一致**：system prompt 放最前且保持稳定，变动大的内容往后排
- 多轮对话的缓存断点在新消息处：历史整体命中，只有增量计费——所以"上下文长"不等于"每次都全价"
- 工具定义稳定化：工具 schema 是前缀的一部分，频繁改 schema = 亲手打碎缓存
- 账本意识：**KV cache 省的是推理显存（Day 119），prompt caching 省的是 API 账单**——一个是系统的、一个是钱包的，面试别混

### 5. 长上下文使用的位置策略（可操作的细节分）

- **大文档/大 diff 放前，指令放后**：指令在末尾，模型对末尾注意力最强（与"lost in the middle"互补）
- 用清晰的**结构标记**（XML tag / markdown 标题）切分不同信息源，降低检索混淆
- 关键约束**重复强调**或置于首尾两端，不要只埋在中段
- 示例放在指令之后、任务之前——起"聚焦透镜"作用
- 给模型"不知道就说不知道"的逃生通道，比硬塞更多资料更能对抗腐烂

### 6. 高频面试 Q&A 速答

**Q：RAG vs 长上下文，选哪个？**
不是二选一。判据三问：①数据量是否超出窗口（超了必须 RAG/外部化）②是否需要精确召回（长上下文"读过但未必想起"，RAG 是显式检索）③成本敏感度（全量塞上下文 = 每轮全价，RAG = 检索一次+注入少量）。工程常态：**窗口装当前任务状态，RAG 装世界知识**。

**Q：怎么判断该摘要压缩还是结构化裁剪？**
看信息类型。不可重建的决策/事实 → 保留原文或高精度摘要；可重建的过程产物（tool 输出、中间代码）→ 结构化裁剪或外置文件。判断口诀：**"丢了能不能低成本重新拿到？能→裁，不能→压"**。

**Q：上下文窗口大小 vs KV cache 是一回事吗？**
不是。窗口是**逻辑容量**（最多能看多少），KV cache 是**物理机制**（看过的怎么加速复用）。窗口 200K 不代表免费——KV 显存随长度线性涨（Day 119 的口算公式），所以长上下文同时是模型能力问题和 serving 成本问题。

**Q：多智能体系统怎么管上下文？**
三条军规：①子 agent 上下文隔离，只回传结论（最小覆盖原则）②主上下文维护全局状态摘要而非流水账 ③并发写入要串行化或分区（共享可变状态问题，Week 16 回声）。Day 127 展开。

**Q：上下文工程 vs Prompt Engineering？**
Prompt Engineering 是**单次填充的艺术**；Context Engineering 是**多轮生命周期管理**：写入、增长、退化、压缩、重建。前者是静态优化，后者是动态系统——面试里能给出这个升维定义就是加分项。

**Q：怎么估算一个 agent 单轮的上下文开销？**
四账相加：system prompt + tool schemas + 历史消息（含 tool 输出，往往是最大头）+ response 预留。经验法则：coding agent 跑半小时，历史里 70%+ 是 tool output——**管控 tool 输出长度（截断、摘要、外置）是最有效的杠杆**，而不是删用户消息。

### 7. 金句收尾

> **"Truncation 是失忆，summarization 是听说，structured compaction 是整理，sub-agent 是让别人去记。工程上高级的上下文管理，是让每一条信息在正确的时间处于正确的位置——而不是试图让模型什么都记得。"**

### 8. 今日自检清单

- [ ] 最小覆盖子串双指针模板能 10 分钟默写（need/cnt/missing 三件套）
- [ ] 能解释 `missing` 为什么要数种类而非次数
- [ ] 能给出贪心正确性的"右端点固定时短窗不劣"论证
- [ ] 能说出 context rot 的四大机制（指令冲突/lost in the middle/error compounding/信噪比）
- [ ] 四种策略总表（截断/滑窗/摘要/结构化压缩）能默写并各举一生产实例
- [ ] 能讲清 sub-agent 外置上下文 = 最小覆盖子串的工程版
- [ ] prompt caching 的前缀一致性要求与 schema 稳定化能脱口而出
- [ ] RAG vs 长上下文三问判据能现场作答

---

## 明日预告

**Day 125**：Tool Use 工程、MCP 协议与 Computer Use —— 从"会调用"到"调得稳"：function calling 的实现细节、MCP 的 client-server 架构与安全问题、computer use 的观测-动作循环。算法题将选一个**多源最短路径/依赖解析**类题目作为 tool orchestration 的微缩模型。

---

*Day 124 · 2026-10-06 · 连续第 124 天 🎭*
