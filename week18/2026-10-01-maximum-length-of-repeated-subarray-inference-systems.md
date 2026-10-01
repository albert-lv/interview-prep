# Day 119 — 最长重复子数组 + 长上下文与推理系统工程（KV Cache · Prefix Caching · 延迟-质量权衡）🔬

> 📅 2026-10-01 · Week 18 Day 5 · 连续更新第 119 天
>
> 主题：推理模型与 Test-Time Compute 🧩 · 算法题 = 最长公共"后缀"DP（识别重复 → 复用），面试技巧 = 推理系统工程：KV cache 账本 / PagedAttention / prefix caching / PD 分离 / 延迟-质量交换所
>
> 🧵 本周路线：Day 115 Test-Time Compute 总览 → Day 116 PRM（过程验证器）→ Day 117 树搜索（ToT/MCTS/rStar）→ Day 118 内生验证（Self-Consistency/Self-Refine/验证者悖论）。今天落地到**系统工程**：算力省在状态里还是省在缓存里？算法题是完美镜像——最长重复子数组要求的就是"两个数组共享的最长连续片段"，而 LLM 推理系统的**前缀缓存（prefix caching）**做的正是"识别重复前缀 → 复用 KV cache"。Day 1 的 DP 讲「重叠子结构折叠计算」，今天的 serving 讲「共享前缀折叠计算」——**同一个对偶，隔了 118 天，闭环了**。

---

## 1) 今日算法题

### 最长重复子数组（Maximum Length of Repeated Subarray）

**题意**：给两个整数数组 `nums1` 和 `nums2`，求**同时**出现在两个数组中的**最长公共连续子数组**的长度。子数组要求**连续**（区别于子序列），且可以同时出现在任意位置。

```
输入: nums1 = [1,2,3,2,1], nums2 = [3,2,1,7,5]
输出: 3
解释: 最长公共子数组是 [3,2,1]，长度 3。

输入: nums1 = [0,0,0,0,0], nums2 = [0,0,0,0,0]
输出: 5
解释: 整个数组都是公共子数组。注意答案可能是全数组——corner case。
```

**关键约束**：`1 <= nums1.length, nums2.length <= 1000`，`0 <= nums1[i], nums2[i] <= 100`。n·m = 10⁶，O(n·m) DP 稳过，O(n·m·min) 暴力会 T。

### 思路：和「最长公共子序列」一字之差，状态定义完全相反

LCS（不连续）的 `dp[i][j]` 表示"前 i 个和前 j 个的最优"——**前缀视角**，不匹配时继承历史最优；本题要**连续**，`dp[i][j]` 必须改成"**以 nums1[i-1]、nums2[j-1] 结尾的最长公共后缀长度**"——**后缀视角**，一旦 `nums1[i-1] != nums2[j-1]` 立刻清零，一段公共子数组必须**从某个对齐点开始连续匹配到当前**。

**转移方程**：

```
dp[i][j] = dp[i-1][j-1] + 1   若 nums1[i-1] == nums2[j-1]
dp[i][j] = 0                  否则            // ← 连续性的命门：不匹配就断，不是继承
答案 = max 所有 dp[i][j]
```

**为什么清零而不是"继承历史最优"**：连续子数组不允许跳过任何元素。若 `dp[i][j]` 继承 `max(dp[i-1][j], dp[i][j-1])`，求出来的就是 LCS 而不是公共子数组——这是本题最大的坑，也是面试官最爱钓的点："改成子序列怎么改？"（去掉清零行即可）。

**空间优化**：`dp[i][*]` 只依赖 `dp[i-1][*]` → 滚动数组。压缩成一维时**必须倒序遍历 j**，否则 `dp[j-1]` 在本轮已被覆盖（变成了 dp[i][j-1]），递推关系被破坏。这个"压缩要倒序"的细节和 0-1 背包一模一样——背过模板，但要在面试现场说出**为什么**。

**复杂度**：时间 O(n·m)，空间 O(m)（滚动后）。

### 代码

**Python（二维版，先写对）：**

```python
def findLength(nums1: list[int], nums2: list[int]) -> int:
    n, m = len(nums1), len(nums2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    ans = 0
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if nums1[i-1] == nums2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1   # 连续匹配：接上一段
                ans = max(ans, dp[i][j])
            # else: dp[i][j] = 0 —— 连续子数组断裂，不继承！
    return ans
```

**Go（滚动数组，压缩要倒序）：**

```go
func findLength(nums1, nums2 []int) int {
    n, m := len(nums1), len(nums2)
    dp := make([]int, m+1)
    ans := 0
    for i := 1; i <= n; i++ {
        for j := m; j >= 1; j-- { // 倒序！正序会读到本轮已更新的 dp[j-1]
            if nums1[i-1] == nums2[j-1] {
                dp[j] = dp[j-1] + 1
                if dp[j] > ans {
                    ans = dp[j]
                }
            } else {
                dp[j] = 0 // 断裂清零——本题与 LCS 的唯一差别
            }
        }
    }
    return ans
}
```

**Follow-up 1：O((n+m)·log min(n,m)) 怎么做？二分 + 滚动哈希。**

判定"是否存在长度 L 的公共子数组"：把 nums1 的所有长度 L 子数组哈希塞进集合，再扫 nums2 的每个长度 L 窗口，任一命中即存在。判定单调（有长 L 必有短 L）→ 二分 L。总复杂度 O((n+m)·log min(n,m))，代价是哈希碰撞（双哈希或 64 位取模压到可忽略）。更理论的做法是后缀自动机 O(n+m)，但 hash 二分是现场十分钟能推出来的版本。

```python
def findLengthHash(nums1: list[int], nums2: list[int]) -> int:
    MOD, BASE = (1 << 61) - 1, 1000003
    def contains(L: int) -> bool:
        seen = set()
        h, pw = 0, pow(BASE, L, MOD)
        for i, x in enumerate(nums1):
            h = (h * BASE + x + 1) % MOD
            if i >= L: h = (h - (nums1[i-L] + 1) * pw) % MOD
            if i >= L - 1: seen.add(h)
        h = 0
        for i, x in enumerate(nums2):
            h = (h * BASE + x + 1) % MOD
            if i >= L: h = (h - (nums2[i-L] + 1) * pw) % MOD
            if i >= L - 1 and h in seen:
                return True
        return False
    lo, hi = 0, min(len(nums1), len(nums2))
    while lo < hi:                    # 二分最大可行长度
        mid = (lo + hi + 1) // 2
        if contains(mid): lo = mid
        else: hi = mid - 1
    return lo
```

**Follow-up 2：把答案改回"子序列"**（LeetCode 1143 LCS）——只需把"不匹配清零"改成 `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`。面试时主动对比写出两行，一句话点破："子序列继承历史最优，子数组必须清零续命"。

### 面试官连环问

> **Q1：这题和最长公共子序列（LCS）有什么区别？状态定义为什么不一样？**
>
> 子序列可以不连续 → 状态是"前 i 前 j 的最优值"，不匹配时继承 `max(左, 上)`；子数组必须连续 → 状态只能是"以 i-1、j-1 **结尾**的公共后缀长度"，不匹配必须清零。一句话：**最优值类状态可以继承，长度/位置类状态必须局部**。这决定了转移方程的形状。

> **Q2：滚动数组为什么 j 要倒序？**
>
> 压缩后 `dp[j]` 的语义是"上一行"。正序遍历时，`dp[j-1]` 在本轮已经被更新成 `dp[i][j-1]`，递推就拿错了对象。倒序保证读到的 `dp[j-1]` 仍是上一行的值。和 0-1 背包的倒序是同一个原因——滚动压缩的本质是"用空间换时间后，再用遍历顺序换正确性"。

> **Q3：还能更快吗？**
>
> 二分长度 + 滚动哈希 O((n+m)·log min(n,m))，存在性判定单调所以有得二分；理论上界是后缀自动机/后缀数组 O(n+m)。但要补一句：n,m ≤ 1000 时 DP 常数极小，工程上反而是最优解——**会算复杂度，也要会说什么时候不用优化**，这是 senior 和 junior 的分水岭。

> **Q4：要求返回具体子数组内容而不只是长度？**
>
> 记录最大值出现的位置 (i, j)，回溯 dp[i][j] → dp[i-1][j-1] → … 连续走 ans 步即可。或者滚动数组时顺手维护 `bestEndI, bestEndJ` 两个坐标。考察的是"dp 值 ↔ 路径还原"的通用技能（和 Day 1 爬楼梯数路径一脉相承）。

> **Q5：数据是字符串（如 DNA 序列比对）有什么变化？**
>
> 本质不变，哈希基数换字符集大小；若元素范围大，先离散化。DNA 比对还会加**带权匹配/失配罚分**（如 BLAST 打分），那就从"最长公共"变成"最大相似子串"（DP 转移带罚分项），但"匹配续上、失配清零/罚分"的骨架不变。

> **Q6：从 10⁶ 数组 × 10⁶ 数组（如两版代码 diff、两版模型权重对拍）怎么办？**
>
> 三层升级：① 哈希二分 + 布隆过滤器判定存在性，省内存；② 分块外存 DP（核外计算，按行块加载）；③ 后缀自动机/后缀数组线性扫。工程语境下真正的考点是**内存带宽与缓存局部性**——dp 数组按行遍历对 CPU cache 友好，分块后更友好。算法题聊到 cache line，面试分直接上一档。

### 🧬 核心隐喻：DP 折叠计算 ↔ Prefix Caching 折叠计算

本题的暴力做法是枚举 nums1 的每个起点 × nums2 的每个起点 × 逐位匹配 O(n·m·min)；DP 的 `dp[i][j] = dp[i-1][j-1]+1` 干的事是——**"上一处对齐点的计算成果，被下一处直接续用"**。把"重复出现的公共片段"识别出来，一次计算、处处复用。

LLM 推理系统的 **prefix caching** 是同构操作：系统提示词、少样本示例、长文档、多轮对话历史，本质都是"跨请求反复出现的公共前缀"。vLLM 把 KV cache 按 block（默认 16 token）切块并做**内容哈希**，新请求到来时逐块查表，命中的前缀直接复用物理块、跳过 prefill 计算。识别重复 → 缓存复用 → 不再重算。

**Day 1 的 DP 教我们「重叠子结构 → 折叠计算」；今天的 serving 教我们「共享前缀 → 折叠计算」。同一个对偶，隔了 118 天，闭环。**

GRPO 是教科书场景：一组 G=8~64 条 rollout 共享「系统提示 + 题目」长前缀，prefix caching 把 prefill 从 O(G×L) 降到 O(L)，省 30~60%——Day 112 的"隐性红利"，今天讲清原理。三处共享的 KV 块（GRPO 组、多轮对话、系统提示）→ 三处白嫖的计算。

---

## 2) 面试技巧：长上下文与推理系统工程

### 🗺️ 总览：一个请求在推理系统里的旅程

```
请求 → 分词 → 【prefix cache 查表】→ prefill 未命中部分（算 KV）
     → decode 逐 token 生成（读 KV + 写 KV）→ detokenize → 释放 KV 块
```

两个必须脱口而出的指标：

| 指标 | 全称 | 量的是什么 | 主战场 |
|---|---|---|---|
| **TTFT** | Time To First Token | 首 token 延迟（≈ prefill 时间） | 长提示、首响体验 |
| **TPOT / ITL** | Time Per Output Token | 后续每个 token 的间隔（≈ decode 步延迟） | 生成流畅度、流式体验 |

**Prefill vs Decode——两种完全不同的负载，这是全部门道的总开关**：

| | Prefill | Decode |
|---|---|---|
| 计算 | 一次并行算完整个提示词的 QKV+Attention | 自回归，一次只算 1 个 token |
| 瓶颈 | **算术强度低 → 计算受限（compute-bound）** | **每步读全部权重+全部 KV → 访存受限（memory-bound）** |
| batch 策略 | 越大越赚（摊薄并行开销） | 大 batch 显著提升吞吐，但单请求延迟变差 |
| 对应指标 | TTFT | TPOT |

> 🗣️ 一句话记住：**prefill 是"一口气读完题"，decode 是"逐字往外蹦"；前者吃算力，后者吃带宽。**

### 账本一：KV Cache 到底占多少显存（必背公式）

```
KV bytes/token = 2 (K和V) × L层数 × H_kv头数 × d头维 × 精度字节

例：Llama-2-7B（L=32, GQA前 H=32, d=128, fp16）
  = 2 × 32 × 32 × 128 × 2B = 512 KB / token
  → 4K 序列 ≈ 2GB（fp16 权重才 ~13.5GB，KV 占比已 15%）
  → 32K 序列 ≈ 16GB（超过权重的 100%！）

Llama-3-70B（GQA：H_kv=8, d=128, L=80, fp16）
  = 2 × 80 × 8 × 128 × 2B = 320 KB / token
  → 32K 序列 ≈ 10GB/token-batch，权重 ~140GB
```

- **KV cache 随序列长度线性增长**，而 Attention 计算随长度**平方**增长——长上下文同时吃掉显存和算力。
- **GQA/MQA 就是砍 KV 头数**：70B 用 8 个 KV 头替代 32 个（4 倍压缩），精度损失微小——这是近两年的标准操作，面试提到 = 懂行。
- 现场估算口诀：「**层数 × 头维 × KV头数 × 2 × 字节数**」报出每 token 大小，再乘序列长度和并发——显存账本会算了，所有"32K 上下文能不能上 A100 80G"的问题都能口算。

```python
def kv_bytes(layers, kv_heads, head_dim, seqlen, dtype_bytes=2, batch=1):
    return 2 * layers * kv_heads * head_dim * seqlen * dtype_bytes * batch

kv_bytes(32, 32, 128, 4096)        # 2147483648 ≈ 2GB（Llama-2-7B, fp16）
kv_bytes(80, 8, 128, 32768)        # 10485760000 ≈ 10GB（Llama-3-70B GQA, 32K）
```

### 账本二：PagedAttention——KV cache 的"虚拟内存"

vLLM (OSDI 2023, arXiv:2309.06180) 的核心观察：**现有系统（FasterTransformer/Orca）为每个请求预留 max_len 的连续显存 → 内部碎片 + 外部碎片 + 无法共享**。PagedAttention 抄操作系统作业：

| 概念 | OS 虚拟内存 | PagedAttention |
|---|---|---|
| 页 / Page | 4KB | KV Block（默认 **16 token**） |
| 页表 | 虚拟页 → 物理页帧 | **Block Table**：逻辑块 → 物理块 |
| 按需分配 | malloc 不预占 | 生成到第几个块才分配第几个块 |
| 写时复制 | fork() COW | **Copy-on-Write 共享块**：共享前缀的多个请求读同一块，有人要写才复制 |
| Swap | 内存 ↔ 磁盘 | CPU offload（Swap Space，显存不够时） |

结果：**显存浪费从 60~80%（Orca）降到 <4%，吞吐量 2~4×**。面试讲法三步走：① 问题（预分配碎片 + 无法共享）；② 机制（块 + 块表 + 按需 + COW）；③ 收益（接近零浪费 + 前缀共享 → prefix caching）。

### 账本三：Prefix Caching——算法题的工程化身

- **做法**：对每个 KV 块算**内容哈希**（父块哈希 + 本块 token + 块号），全局哈希表记录"活跃块"。新请求逐块查表，命中则直接挂物理块、跳过这部分 prefill；第一个未命中块之后全部重算。LRU 管理容量，请求结束释放引用计数。
- **块大小的权衡**（经典追问）：块小 → 命中率高、元数据开销大；块大 → 元数据省、尾部内部碎片多（平均浪费 block/2 的尾部）。vLLM 默认 16 token 是经验平衡点。
- **三处白嫖场景**：① **GRPO 组共享**（G 条 rollout 同前缀：prefill O(G·L) → O(L)，省 30~60%，前缀越长越赚）；② **多轮对话**（历史轮次整体命中，只需 prefill 新消息）；③ **系统提示/少样本模板**（跨请求全局命中，命中率最高的资产）。
- **路由追问**：① 为什么块要对齐？——对齐才有稳定的哈希键；② 命中块中间的 token 变了怎么办？——内容哈希天然免疫，变了就是新键；③ 淘汰策略？——LRU + 引用计数，GRPO 组内同前缀块引用计数 > 1，组内全部结束才回收。

### 账本四：PD 分离（Prefill-Decode Disaggregation）

**为什么拆**：prefill 要算力（大 batch、大 kernel），decode 要带宽（小 batch、快响应），资源画像相反；混部时 prefill 的插入会让 decode 的 TPOT 尖刺（抖动），SLA 互相伤害。

**怎么拆**：prefill 节点算完 KV → 按层传输到 decode 节点（RDMA/NVLink，传输量 = KV cache 大小，可用上面公式预算传输时间）→ decode 节点接管。两侧独立扩缩容、独立 batch、独立排队——TTFT 和 TPOT 分开优化、各有 SLO。

**一句话定取舍**：「短平快场景混部够用；高并发、长提示、SLA 严格的在线服务，PD 分离是正解。」

### 长上下文的三座大山与登山杖

| 大山 | 根因 | 登山杖 |
|---|---|---|
| Prefill 变慢 | Attention O(L²)，TTFT 爆炸 | **Chunked Prefill**（切片与其他请求混排）/ 路由到 prefill 专机 |
| 显存爆炸 | KV O(L) 线性涨 | **KV 量化**（INT8/FP8，~2×，轻微掉点）/ PagedAttention 去碎片 / Sliding-Window Attention |
| Decode 变慢 | 每步读全量 KV，带宽瓶颈 | KV 压缩 / 稀疏化（H2O 等逐出低价值 token）/ **RAG——别塞满上下文，检索该看的** |

**别漏了 RAG 这条工程路线**：Day 91 的 RAG 就是"长上下文杀手"——把 100K 文档塞进上下文是最贵的解法，检索出 5K 相关片段是便宜的解法。延迟-成本-质量三角里，RAG 经常是先手。

### 延迟-质量交换所：两边都能换，但汇率不同

| 方向 | 技术 | 汇率 |
|---|---|---|
| 用算力/显存**换延迟** | Speculative Decoding（Day 93：小模型草拟、大模型验证，无质量损失但耗 2× 算力） | 好汇率 |
| | KV Cache 量化（~2× 显存 ↓，轻微质量损失） | 好汇率 |
| | 层早退 / 层跳过（末几层跳过，~10~20% 提速，小掉点） | 看任务 |
| | PagedAttention / Prefix Caching / PD 分离 | **纯赚**：不动模型质量，纯系统收益 |
| 用延迟**换质量** | 长 CoT / test-time compute（Day 115 起全周主线） | 核心汇率 |
| | 自适应思考预算（答对就早停；难题多跑——compute-optimal） | 好汇率 |
| | Budget Forcing / Cascade（短模型先答，低置信升级长模型） | 好汇率 |

> 🎯 本周合流时刻：**前四天讲的 test-time compute 是"质量-延迟"交换的右侧（买质量），今天的 serving 优化是左侧（买延迟）。一个推理系统的设计感，就体现在你知道每笔交换的汇率。**
>
> 收尾金句：**「serving 的艺术是识别重复——能缓存的重复（prefix）别重算，能复用的状态（KV）别重算；test-time compute 的艺术是识别困难——能便宜的简单别买贵，值得花钱的困难别吝啬。」**

### 高频面试速查（一句话版）

1. **KV cache 为什么能加速？** decode 时历史 token 的 K/V 不再变化，缓存下来每步只需算新 token 的 Q 与历史 K 点积——避免每步重算全部历史，O(L²) → O(L)（每步）。
2. **KV cache 显存怎么算？** `2 × 层数 × KV头数 × 头维 × 字节 × 长度`；Llama-7B fp16 ≈ 512KB/token，4K ≈ 2GB；GQA/MQA 砍头数就是砍这项。
3. **Prefill 和 decode 的区别？** prefill 并行算全提示（compute-bound），decode 逐 token 生成（memory-bound）——两个阶段的 batch 策略和优化方向完全不同。
4. **PagedAttention？** 把 KV 分块（16 token）+ block table 按需映射——OS 虚拟内存思路：碎片 <4%、COW 支持跨请求共享（抄了 fork 的作业）。
5. **Prefix caching 原理与收益？** 块级内容哈希（父哈希+token），命中跳过 prefill；GRPO 组共享前缀省 30~60%（O(G·L)→O(L)），多轮对话和系统提示是天然高命中资产。
6. **为什么 PD 分离？** prefill 算力型、decode 带宽型，资源画像相反；分离后独立扩缩容与调度，KV 按层 RDMA 传输，TTFT/TPOT 各有 SLO。
7. **长上下文瓶颈？** 显存 O(L)（KV 量化/PagedAttention）、prefill 计算 O(L²)（chunked prefill）、decode 带宽 O(L)（KV 压缩/RAG 减输入）。
8. **"大模型推理优化"总-分答题模板**：模型层（量化/蒸馏/剪枝/投机解码）→ **服务层（PagedAttention·prefix caching·PD 分离·continuous batching，主战场）** → 系统层（RDMA/并行策略）。考官想听的是服务层，别停在模型层。
9. **和 veRL 的关系？** Day 113 rollout 是"半个推理服务"：vLLM/SGLang 引擎 + prefix caching（组内共享前缀）+ partial rollout + continuous batching——训练侧的推理加速和在线 serving 是同一套账本。

### Day 119 自检清单

- [ ] 默写 KV cache 显存公式，并用 Llama-7B/70B 各算一个数
- [ ] 说出 prefill vs decode 的资源画像差异，以及为什么 batch 策略不同
- [ ] 画出 PagedAttention 的 block table 结构，说出 COW 的两个场景
- [ ] 讲清 prefix caching 的哈希链怎么构造，块大小的权衡
- [ ] 口算 GRPO prefix caching 收益：L=2K prompt、G=16、答案 500 token，省多少 prefill？
- [ ] 说出 PD 分离的收益和 KV 传输量的计算方法
- [ ] 用"延迟-质量交换所"回答：为什么 PagedAttention 是纯赚，speculative decoding 是花算力买延迟？

### 明日预告

**Day 120：Verifier 生态与 LLM-as-Judge**——最后一块拼图：当没有规则验证器时怎么办？LLM-as-Judge 的设计、偏差（位置偏差/冗长偏差/自我偏好）、评估集的构建，以及 judge 被 Goodhart 的全过程。验证器家族收官，后天 Day 121 综合复习，Week 18 完结 🎉

---

## 📎 附录：今日一页纸

```
┌──────────────────────────────────────────────────────────┐
│  最长重复子数组: dp[i][j] = 以 i-1/j-1 结尾的公共后缀长度      │
│  匹配 → dp[i-1][j-1]+1; 不匹配 → 0(清零是本题命门)            │
│  滚动数组 j 倒序; 优化 = 二分长度+滚动哈希                     │
├──────────────────────────────────────────────────────────┤
│  KV/token = 2×L层×H_kv头×d维×字节  (7B fp16≈512KB)         │
│  PagedAttention: 16-token 块 + block table + COW          │
│  Prefix cache: 块内容哈希(父哈希+token),命中跳过 prefill      │
│  GRPO: prefill O(G·L)→O(L), 省 30~60%                      │
│  PD 分离: prefill 算力型 ∥ decode 带宽型, KV 按层 RDMA      │
│  长上下文: O(L²)prefill · O(L)显存 · O(L)带宽 → 三把登山杖  │
│  交换所: serving 买延迟(纯赚) ↔ test-time 买质量(核心汇率)   │
└──────────────────────────────────────────────────────────┘
```
