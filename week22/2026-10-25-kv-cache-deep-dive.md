# Day 143 · 2026-10-25 · Week 22 Day 2

## 今日主题：KV Cache 管理深潜——PagedAttention block 调度 / RadixAttention / 抢占换出 / MLA 低秩压缩

> 昨天（Day 142）我们把推理基础设施的总览地图画完了，今天扎进整个系统里「最贵的一块显存」——KV cache。
> Gartner 说 2026 年推理支出 $233 亿首超训练；Artificial Analysis 说缓存读写占 Agent 任务成本 58.8%。
> **这两笔钱，大头都躺在 KV cache 的管理方式里。**

---

## 🔥 本周时事锚点（10/18–10/25 · KV cache 专题）

1. **ACL 2026《When Efficiency Meets Safety》（10/24，昨天见刊）**——首个系统研究 **KV cache 压缩 × jailbreak 攻击**的相互作用，结论是教科书级双刃剑：
   - **「意外鲁棒性」**：SnapKV 等驱逐式压缩让 GCG / ReNeLLM 等优化型越狱攻击显著失效——攻击构造的对抗后缀吸走注意力，反而让真正的恶意 query 被压缩策略判定为「低重要性」直接驱逐（**Malicious Semantic Eviction**）；离散的压缩操作打断梯度连续性（**Gradient Mismatch**），攻击者对着全量模型优化的梯度在压缩世界里是错的。
   - **「脆弱性悖论」**：CAM 等合并式压缩下，In-Context Attack 被放大——**LLaMA-2 从 0% ASR 飙到 34%**（KV budget 512）。机理：浅层激进合并 → value 叠加 → **功能头坍缩** → refusal 向量偏移，「安全锚点」丢了。
   - **Safe-CAM**：逐头、历史感知的反馈合并 → ASR 回到 0%，良性任务还涨点，零额外显存。**KV cache 压缩率从此是安全变量，不只是成本变量。**
2. **KV Cache Hijacking（arXiv 2607.19957，本周社区热议）**——「同 token 同 KV」的位置无关复用假设被击穿：同一个 token 在不同上下文语义下 KV 完全不同；**被污染的前缀若把 KV 写进共享缓存，后续请求复用时行为被静默劫持**。多租户前缀缓存的复用策略必须考虑语义完整性。
3. **KV-Cloak（NDSS 2026）**——KV 缓存隐私三宗罪：**inversion**（从 KV 张量反推用户原始输入）、**collision**（跨用户内容碰撞推断）、**injection**（恶意 KV 注入）。KV cache 在 GPU 显存、CPU 内存、网络传输、SSD 落盘全链路基本是**明文**。防御 = 可逆矩阵混淆 + 算子融合，开销 <10% 延迟。LeftoverLocals（AMD GPU 显存残留）已经示范过硬件层 KV 泄露的玩法。
4. **vLLM cache_salt / CVE-2025-46570 前缀缓存侧信道**——共享 prefix cache 存在时序侧信道 + 投毒面，官方对策是给缓存键加盐做租户隔离。**多租户场景下「缓存命中」本身就是信息泄露。**
5. **框架双发版（10/18–10/19）**——vLLM **v0.32.0**（94.4K stars）与 SGLang **v0.5.22**（37.5K stars）同期发布；SGLang v0.5.22 **默认启用 Rust 前缀缓存核心**，HiCache 分层 KV（热数据留 GPU / 冷数据卸 CPU / NVMe）进入默认配置。
6. **DeepSeek DeepInfer 开源（10/16–10/20）明确「不含 KV 独立系统」**——README 写明未包含 KV cache 独立系统与 P/D 分离，部署方需自行补齐**分布式 KV、分阶段服务、副本负载均衡**。「开源引擎、保留服务层」= KV 管理层正是各家真正的护城河。
7. **ModelSphere 开源（10/16）**——三级 KV 缓存 + 统一 KV 缓存池，把 KV 从单引擎内部件升格为可共享的分布式资源池。
8. **FP8 KV cache 量化实测（2026-04 vLLM 团队数据，本月被多处引用）**——KV 体积砍半，精度在噪声范围内；配合 GQA/MLA，长上下文推理的显存天花板被连续击穿。
9. **（呼应 Day 142）LMCache CVE-2026-105192**——缓存层的 RCE 是边界失守；今天的三篇论文是缓存**内容**的机密性 / 完整性 / 安全语义失守。**KV cache 管理从性能问题正式升格为安全工程。**

---

## 一、今日算法题：我的日程安排表 III（LC 732）—— 懒标记线段树：在线区间加与峰值并发

### 题目

实现一个 `MyCalendarThree` 类：每次 `book(start, end)` 预订半开区间 `[start, end)` 的会议，**返回预订后当前所有会议中最大重叠次数 k**（即存在一个时刻被 k 个会议同时覆盖）。

```
MyCalendarThree cal = new MyCalendarThree();
cal.book(10, 20); // 返回 1  —— 只有 [10,20)
cal.book(50, 60); // 返回 1  —— 两个会议不重叠
cal.book(10, 40); // 返回 2  —— [10,20) 被两个会议覆盖
cal.book(5,  15); // 返回 3  —— 5~15 之间有 [5,15),[10,20),[10,40) 三个
cal.book(5,  10); // 返回 3  —— 最大重叠仍是 3
cal.book(25, 55); // 返回 3  —— [25,40) 三个，[50,55) 三个，不突破
```

约束：`0 <= start < end <= 10^9`，调用次数最多 `400` 次。

### 工程重述：这就是 KV 块池的「在线版拼车」

昨天拼车（LC 1094）是**离线**判定：所有行程提前给你，差分数组扫一遍完事。
今天这道题是**在线**流：预订一个接一个来，每接一个都要立刻回答「当前峰值并发是多少」。

映射到 KV cache 管理器（今天的主角）：

- 每个 `book(s, e)` = 一个推理请求持有 KV 块的生命周期 `[到达, 释放)`；
- 返回值 k = **当前显存池被同时占用的峰值块数**；
- `k > 物理块总数` 的那一刻 = **触发抢占 / 换出（preemption / swapping）的瞬间**；
- 请求区间**事先不可知**（用户随时来）、**每次都要即时回答**（调度器每个 step 都要决策）——离线差分数组救不了你，必须上**在线数据结构**。

### 为什么不是差分 / BIT？——先建「数据结构选型决策表」

| 需求形态 | 数据结构 | 本题是否满足 |
|---|---|---|
| 区间加 + **离线**全序列扫一遍 | 差分数组 | ❌ 查询是在线的 |
| 区间加 + 单点查值 | 差分 / BIT（两次点更新） | ❌ 要的是全局/区间最值 |
| **区间加 + 区间最值** | **懒标记线段树** | ✅ **正主** |
| 区间加 + 区间和 | 懒标记线段树（或 BIT 技巧） | — |

**面试必背推论**：区间加 + **max/min** 查询 ⇒ BIT 做不了（BIT 的累加结构保和不保最值），必须用线段树懒标记。这就是为什么面试官在本题的 follow-up 里等你报 BIT 然后温柔地摇头。

### 思路：懒标记线段树（Lazy Propagation Segment Tree）

朴素线段树区间加要 O(log T)：把 [ql, qr] 覆盖的 O(log T) 个节点逐个 +1，但这样**部分覆盖的节点其子树所有叶子都没更新**——下次查询下探到叶子时发现值是错的。

懒标记的精髓就一句话：**「我现在没空下推，先记账，谁需要谁结账。」**

- 每个节点多存一个 `add`（懒标记）= 「本区间整体被加了 add，子树还没收到」；
- 区间更新完全覆盖某节点时：只改 `mx += val`、`add += val`，**直接返回，不递归**；
- 一旦未来某次操作需要**部分覆盖**这个节点（必须下探子区间）：先把 `add` 下推给孩子（孩子 `mx += add`、`add += add`，自己 `add` 清零）——这一步叫 push_down；
- 更新完回溯：`mx = max(左.mx, 右.mx)`。

**本题的两个工程化细节：**

1. **值域 10⁹，节点不可能提前建满** → 动态开点：节点在被访问到时才 `new`。本题 400 次调用 × 每层 2 个新节点 × 约 30 层 ≈ 2 万节点，游刃有余。若调用量到 10⁵ 也只需 O(n log T) 个节点。
2. **半开区间 → 闭区间**：`[start, end)` 加一 = 闭区间 `[start, end-1]` 加一，和差分题的「end 先下车」是同一个约定。

### 代码

```python
class Node:
    __slots__ = ("l", "r", "mid", "add", "mx", "left", "right")
    def __init__(self, l, r):
        self.l, self.r = l, r
        self.mid = (l + r) >> 1
        self.add = 0        # 懒标记：本区间整体 +add，尚未下推给孩子
        self.mx  = 0        # 本区间（含懒标记贡献）的最大重叠数
        self.left = self.right = None   # 动态开点：未访问过则为 None

class MyCalendarThree:

    def __init__(self):
        self.root = Node(0, 10**9)      # 值域就是时间轴

    def _push_down(self, node):
        """下探前调用：孩子不存在则动态创建；把账结给孩子。"""
        if node.left is None:
            node.left  = Node(node.l, node.mid)
            node.right = Node(node.mid + 1, node.r)
        if node.add:
            for ch in (node.left, node.right):
                ch.add += node.add
                ch.mx  += node.add
            node.add = 0

    def _update(self, node, ql, qr, val):
        if ql <= node.l and node.r <= qr:   # 完全覆盖：记账即返回
            node.add += val
            node.mx  += val
            return
        self._push_down(node)               # 部分覆盖：先结账再递归
        if ql <= node.mid:
            self._update(node.left,  ql, qr, val)
        if qr >  node.mid:
            self._update(node.right, ql, qr, val)
        node.mx = max(node.left.mx, node.right.mx)

    def book(self, start: int, end: int) -> int:
        self._update(self.root, start, end - 1, 1)   # 半开 → 闭区间
        return self.root.mx                        # 全局最大重叠 = 当前 k
```

### 复杂度

- 每次 `book`：O(log T)，T = 10⁹ → 实际约 30 层 ≈ **O(log 10⁹) ≈ 30 次操作**；
- 空间：O(n log T) 动态节点（本题 n=400，约 2 万节点；每条更新路径每层开 2 个点）。

### 备选写法：边界计数法（SortedDict / 离散化 + 差分思想）

如果面试官说「调用次数只有 400，值域 10⁹，有没有更简单的？」——可以退到**边界事件计数**：

```python
from sortedcontainers import SortedDict

class MyCalendarThree:
    def __init__(self):
        self.delta = SortedDict()          # 端点 → 净变化量

    def book(self, start, end):
        self.delta[start] = self.delta.get(start, 0) + 1
        self.delta[end]   = self.delta.get(end,   0) - 1
        cur = mx = 0
        for d in self.delta.values():      # 扫一遍当前所有端点
            cur += d
            mx = max(mx, cur)
        return mx
```

- 每次 O(n) 重扫（n = 已有端点数），n=400 时毫无压力；
- **注意这不满足「均摊 O(log n)」的承诺**：如果面试官追问 10⁶ 次调用怎么办，必须回到线段树；
- 但它揭示了线段树的本体：**线段树就是把「每次重扫前缀和」加速成「区间聚合 + 懒记账」的结构**。讲出这层等价关系是加分点。

### 高频连环问

1. **「能不能不用线段树？」**——能：调用少时用边界计数 O(n²)；端点已知且少时先离散化 + 差分数组。但要背熟决策表：在线 + 区间最值 ⇒ 懒标记线段树是标准答案。
2. **「值域 10⁹ 内存不够怎么办？」**——动态开点（本题正解）；或离线收集所有端点离散化到 ≤ 2n 的坐标再建树。
3. **「如果返回的是区间和而不是最大重叠呢？」**——懒标记线段树节点把 `mx` 换成 `sum` 即可；或者 BIT 区间加（两个点更新）+ 前缀和查询。
4. **「book 带删除（取消会议）呢？」**——`update(s, e-1, -1)` 就行，线段树天然支持撤销；这也是真实系统里请求取消 / 抢占释放的建模方式。
5. **「k 超过容量 C 怎么办？」**——本题没有容量概念，但工程版（真实调度器）要回答「**踢谁**」：踢最晚进来的（FIFO 抢占）/ 持有块最多的 / 优先级最低的——这就是下面面试技巧的第 4 节。

### 与 KV Cache 的精确对应（面试时主动讲这层，直接封神）

| 算法题概念 | KV cache 对应物 |
|---|---|
| 时间轴 [0, 10⁹] | 推理集群的连续时间线 |
| book(s, e) 区间 | 请求持有 KV 块的周期 |
| 懒标记 `add` | 「这个请求 +N 个块」的记账，延迟到调度时结算 |
| root.mx = k | 当前峰值块占用 = 池子设计容量依据 |
| k > 物理块数 | 触发 preemption / swap 的时刻 |
| update(-1) 撤销 | 请求完成 / 被抢占后释放块 |
| 动态开点 | 块按需分配、用完即还，从不预占全量 |

---

## 二、面试技巧：KV Cache 管理深潜——从 PagedAttention 到信任边界

### 0. 一句话总纲（面试开题用）

**「KV cache 是推理系统的主存：PagedAttention 是分页机制，RadixAttention 是页缓存，抢占换出是 OOM killer，MLA 是压缩算法，而 cache_salt 和 KV-Cloak 是mmu——这套操作系统类比讲完，一半追问都能接住。」**

### 1. PagedAttention：OS 分页思想移植

**要解决的问题**：传统推理框架为每个请求预分配 `max_model_len` 的连续显存，但生成长度方差极大（短答几百 token、长文几千），实测浪费 60%–80%，外加外部碎片。

**vLLM 的解法**（OS 类比逐条对上）：

| OS 概念 | PagedAttention | 面试要点 |
|---|---|---|
| 物理页 / 虚拟页 | KV block（默认 16 token）/ 逻辑块 | 块大小 16：太大碎片多、太小元数据膨胀、attention kernel 效率折损 |
| 页表 | **block table**（每序列一张：逻辑块号 → 物理块号） | 物理块可以不连续 |
| 内部碎片 | 只有最后一个不满块（平均 ~8 token） | 对比预分配浪费 60%+，约 4% 量级 |
| CoW / 引用计数 | parallel sampling、fork 共享前缀块，计数归零才回收 | 共享 ≠ 复制，显存二次利用 |
| 缺页分配 | decode 每步按块惰性分配新块 | 分配动作在调度器里，不在 kernel 里 |

**追问「block 里到底存什么」**：形状 `[num_layers, 2, num_kv_heads, block_size, head_dim]`，2 = K/V 两份；kernel 按 block table 间接寻址——所以 attention kernel 读的是**离散块**，这是「paged」对 kernel 的真正含义（vLLM 早期用 gather，新硬件用 TMA/索引直读优化）。

**为什么不用 CUDA 自带的 malloc / caching allocator 直接管？**——caching allocator 管张量粒度、不管「按 token 生长」的语义；block table 层让调度器以 O(1) 完成「这个序列又攒满 16 个 token 了，给它换一个物理块挂上」，且换块不动已生成的 KV。

### 2. Block 调度器：谁配得到显存

调度循环每个 step 干三件事：

1. **准入（admission）**：waiting 队列取新请求，估算需求 = 已占用块 + 本轮预计生成块的 lookahead；不够就排队——这是 admission control，网关侧的 token 预算护栏（Day 142）在这里落地成块粒度。
2. **运行（running）**：给 running 序列按需挂新物理块；**显存水位线** `gpu_memory_utilization`（默认 0.9）之上不分配，余量留给 activation 和 kernel workspace。
3. **回收**：完成的序列立即归还全部块（引用计数归零）。

**长 prefill 阻塞 decode**：一个 32K 前缀的 prefill 会把同批 decode 的 TPOT（每 token 时延）拉高数十倍 → **chunked prefill** 把 prefill 切成 8K 块，和 decode 混排调度，在 TTFT 和 TPOT 之间取平衡。vLLM v1 默认开启；SGLang 有类似机制。

### 3. RadixAttention：跨请求的页缓存

PagedAttention 管「一个请求生命周期内」的块；**prefix caching** 管「跨请求」的复用——SGLang 的实现叫 **RadixAttention**：

- 把所有请求的前缀组织成**基数树（Radix Tree）**：根到节点的路径 = 一段 token 序列，节点挂这段的 KV 块；
- 新请求进来沿树做**最长前缀匹配**，命中的节点直接复用 KV 块（引用计数 +1），未命中部分照常 prefill；
- 池子满时按 **LRU** 淘汰树节点、归还块；
- 效果（官方与第三方数据）：多轮对话 / RAG 固定文档块 / agent 相同工具定义前缀，命中率提升 **3–5×**，吞吐相应翻倍。

**为什么用基数树而不是 hash？**——前缀复用的本质是「最长公共前缀匹配」，树形结构天然支持逐 token 下探匹配；hash 只能整块等值匹配，无法做前缀层级复用。这是送分追问。

**本周对应**：SGLang v0.5.22（10/19）**默认启用 Rust 前缀缓存核心**；vLLM v0.32.0（10/18）继续强化 KV connector 生态；而 DeepSeek DeepInfer 明确「不含 KV 独立系统 / P-D 分离」——**KV 管理层是各家留给自己的护城河**，开源的是引擎，沉淀的是调度。

### 4. 抢占与换出（Preemption & Swapping）——今日算法题的物理实现

物理块耗尽时的两条路：

- **Preempt（抢占，当前默认）**：踢掉部分 running 序列（通常是最新进来的，FIFO），释放块；被踢序列回到 waiting，下次重新 prefill——**代价是重算 KV**。短 prefill 场景重算比搬运快，所以现代 vLLM 默认抢占。
- **Swap（换出）**：把块的 KV 数据拷到 CPU DRAM 腾出显存，恢复时再拷回。**代价是 PCIe 带宽**：70B 级模型单序列 KV 数 GB，PCIe 5.0 x16 也就 64 GB/s 双向，一次换出够 decode 几百步。所以交换只在「序列快结束 / prefill 极长」时划算。

**今日算法题闭环**：`book(s,e)` 是请求持有块，`root.mx` 是峰值并发需求——`root.mx` 超过物理池容量时，真实调度器必须在「踢谁」上做决策：FIFO（实现简单、公平）、按已持有块数（释放收益最大）、按优先级（付费等级）。**生产观测指标**：vLLM 的 `num_preemptions`、Prometheus 里 preemption 计数飙升 = 池子配小了或准入太激进——这是 SRE 面试里「推理集群容量告警」的第一嫌疑犯。

### 5. MLA 低秩压缩：KV 体积的量级解法

KV cache 体积公式（面试必写）：

```
KV bytes = 2(K,V) × num_layers × num_kv_heads × head_dim × seq_len × dtype_bytes
```

以 70B 级、GQA 8 KV heads、32 层、128K 上下文、BF16 估算：单请求 ≈ **40 GB**——一张 H100（80GB）只够 2 个并发长请求。压缩路线三代：

1. **GQA / MQA**（架构层）：kv_heads 从 64 → 8 → 1，直接砍头数；面试追问「MQA 为什么不 universally 更好」——质量折损 + 长上下文检索能力下降。
2. **MLA（DeepSeek-V2/V3/R4 系）**：不缓存展开后的每头 K/V，而是缓存**低秩 latent 向量**（512 维），用的时候再投影展开——KV 体积再降一个量级，这是 DeepSeek 长上下文单位成本极低的架构根因。代价：attention kernel 要配合 latent 投影做融合（FlashMLA / FlashInfer 的支持度是落地关键）。
3. **FP8 / INT8 KV 量化**（表示层）：KV 张量转 FP8，体积砍半，精度在噪声内（vLLM 团队 2026-04 评测）；GQA 之后每 token KV 本就小，量化是性价比最高的下一步。

**但！本周 ACL 2026 论文（10/24）给这条路线泼了盆冷水**：压缩 × 安全存在非平凡相互作用——驱逐式压缩有「意外鲁棒性」，合并式压缩有「脆弱性悖论」（LLaMA-2 ASR 0%→34%）。**面试话术升级**：「KV 压缩率不只是精度-成本 tradeoff，还是安全变量——压缩策略上线前要在安全基准上回归，Safe-CAM 这类逐头反馈机制是新的默认项。」

### 6. KV 分层：GPU → CPU → SSD → 远端

长上下文 + 高并发下，活跃 KV 总量远超单卡显存 → 分层卸载成标配路线：

- **Mooncake**（Kimi 的分离式架构，NSDI'25 论文）：KV cache 池独立部署 + 高性能传输引擎，P/D 分离的存储底座；多级缓存命中时延实测可查 87–190 GB/s 级带宽利用。
- **LMCache**：vLLM 生态的 KV 缓存层（Day 142 讲过它的 CVE-2026-105192——缓存层第一次成为攻击面）。
- **SGLang HiCache**（v0.5.22 默认）：热数据 GPU / 温数据 CPU / 冷数据 NVMe。
- **ModelSphere**（10/16 开源）：三级 KV + 统一缓存池，把 KV 从「引擎内部件」升格为「集群级可共享资源」。
- **InfiniStore、FlexKV** 等同路线选手。

**代价账**：每层换层都买「显存弹性」，付「传输延迟 + 一致性复杂度 + 安全边界扩大」。KV-Cloak 那篇 NDSS'26 论文就是在说：KV 一旦离开 GPU 显存，全链路明文 = 用户输入可反推（inversion）、可碰撞（collision）、可注入（injection）。

### 7. 本周安全风暴：KV cache 的四道信任边界（3–7 天热点专节）

把今天能引用的素材串成面试里的「安全纵深」叙述：

| 层 | 事件 | 面试一句话 |
|---|---|---|
| **缓存即代码执行** | LMCache CVE-2026-105192（Day 142） | 缓存层反序列化 = RCE；缓存进程降权 + 出向默认拒 |
| **缓存即侧信道** | vLLM cache_salt / CVE-2025-46570 | 共享前缀缓存的命中时序泄露跨租户信息；多租户必开 salt |
| **缓存即明文数据** | KV-Cloak（NDSS'26） | KV 离开显存即明文 → inversion 反推输入；混淆 + 算子融合 <10% 开销 |
| **缓存即安全语义** | ACL 2026 Safe-CAM（10/24） | 压缩策略改变越狱抗性；压缩率 = 安全变量，需安全回归 |
| **复用即劫持** | KV Cache Hijacking（arXiv 2607.19957） | 位置无关复用假设被击穿；污染前缀的 KV 可借复用静默劫持 |

**面试官问「推理系统有哪些攻击面」时，这套五连是本周限定版满分答案**——而且每一条都有 10 月内的真实出处，不是背诵。

### 8. 模拟面试五连

**Q1：PagedAttention 块大小为什么默认 16？改大改小会怎样？**
🎯 三块权衡：①元数据/页表项数 ∝ 1/block_size，太大页表瘦、太小爆炸；②内部碎片 = 半块均值，太大浪费线性涨；③kernel 效率——attention 对 16/32 的向量化最友好，太大突破 SMEM / 寄存器预算，kernel 被迫拆块。**16 是三者的经验甜点，不是定律**（TRT-LLM 等有用不同值的）。

**Q2：prefix caching 命中了前缀，还需要 prefill 吗？哪些状态不能复用？**
🎯 KV 块能复用，但**不是所有状态都能**：
- RoPE 位置编码依赖绝对位置——复用块的 K 带着旧位置，续写时新 token 的位置必须和缓存时对得上（RadixAttention 靠「只复用从 0 开始的前缀」天然保证）；
- attention sink / softmax 的运行统计、某些 kernel 的在线归一化状态不一定可搬；
- 采样状态（rng、logits 处理管线）按请求隔离。
所以工程实现里「命中」严格定义为「从第 0 个 token 开始的连续公共前缀」。

**Q3：抢占一个快生成完的序列 vs 刚 prefill 完的序列，选哪个？**
🎯 释放收益视角：快生成完的序列持有块最多（序列最长）但**马上自然释放**，抢了等于白抢（沉没成本已花，收益即将到账）；刚 prefill 完的序列持有块中等、且重算成本低（prefill 一次能重来）。还要考虑**用户对 TPOT 抖动敏感度**：抢占 = 该请求 TTFT 翻倍（重算 prefill），优先抢低优先级 / 内部任务。答案没有绝对，重点是**把「释放收益、重算成本、SLO 等级」三个变量摆上桌**。

**Q4：怎么观测 KV cache 健康度？**
🎯 四个核心指标：**块利用率**（used/total blocks，持续 >85% 要扩容）、**preemption 速率**（vLLM `num_preemptions`，>0 就该告警）、**prefix cache 命中率**（<50% 说明负载模式不适合复用或树太小）、**每请求平均块持有数 × 序列数 = 池子占用曲线**（预测何时打满）。配合 gpu_memory_utilization 水位线做容量规划。

**Q5（本周限定）：KV cache 压缩上线前要补哪些评测？**
🎯 传统三件套（精度 MMLU / 长针 NIAH / 吞吐成本）+ **安全回归**（越狱 ASR：GCG / ReNeLLM / ICA 三族，按 ACL 2026 的划分分别测优化型、编码型、上下文型——它们的压缩响应方向相反！）+ **隐私评估**（若 KV 出卡，对照 KV-Cloak 的 inversion/collision/injection 三攻击做红队）。

### 9. 今日金句

**「KV cache 管理是推理系统的内存管理课：PagedAttention 教你分页，RadixAttention 教你页缓存，抢占换出教你 OOM killer，MLA 教你压缩——而 2026 年 10 月的这三篇论文告诉你，这门课现在开始计入安全学分。」**

---

## ✅ Day 143 自检清单

- [ ] 能默写懒标记线段树的 `update` + `push_down`，并说清「完全覆盖改自己、部分覆盖先结账」
- [ ] 背熟选型决策表：区间加 + 区间最值 ⇒ 线段树；区间加 + 单点查 ⇒ 差分/BIT；并解释 BIT 为什么不保最值
- [ ] PagedAttention 七连：块大小权衡 / block table / CoW / 内部碎片 / kernel 间接寻址 / watermark / chunked prefill
- [ ] RadixAttention：基数树 vs hash 的送分追问；LRU 淘汰；「只复用从 0 开始的前缀」的工程原因
- [ ] 抢占 vs 换出的决策三变量（释放收益 / 重算成本 / SLO）
- [ ] KV 体积公式能现场推（2×L×H_kv×D×T×bytes），并算出 70B/128K ≈ 40GB
- [ ] MLA 一句话：缓存低秩 latent 而非展开 K/V，512 维换量级体积
- [ ] 本周安全五连各配一个真实事件：LMCache RCE / cache_salt 侧信道 / KV-Cloak 明文 / Safe-CAM 压缩×越狱 / KV Hijacking 复用劫持
- [ ] 说出 Safe-CAM 论文的两个机制名词：Malicious Semantic Eviction（意外鲁棒性）与 Vulnerability Paradox（脆弱性悖论，LLaMA-2 ASR 0→34%）
- [ ] 模拟面试五连能各答 90 秒

---

## 📌 明日预告（Day 144 · Week 22 Day 3）

**测试时扩展（Test-Time Scaling）——majority voting / 自洽性 / 元推理反思**
候选算法题：蓄水池抽样 / 加权采样实战
（从「管理 KV」转向「花 KV」：同一个模型，怎么用采样策略买智能——以及为什么 n=8 投票的正确打开方式是流式早停。）
