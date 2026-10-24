# Day 142 · 2026-10-24 · Week 22 Day 1

## Week 22 主题：Inference 系统工程——Agent 时代的推理基础设施

> 本周地图：serving 架构 / KV cache 与 prefix caching / 模型路由与语义缓存 / 网关预算护栏 / 推理成本工程。
> 上周（Week 21）我们把 agentic RL 的「训练侧」打了个通关，这周换到「推理侧」——Gartner 数据已经替我们把「为什么是现在」讲完了：**2026 年全球推理支出 233 亿美元，历史上第一次超过训练（190 亿），占 AI IaaS 总支出 55%**。

---

## 🔥 本周时事锚点（10/18–10/24）

1. **DeepSeek 开源 DeepInfer 推理框架（10/20，本周头条）**——内部大规模云服务推理框架首次开源，兼容 NVIDIA GPU 与华为昇腾 950，已承载 DeepSeek V4.1 Flash 线上服务：**单集群日均处理 155 万亿 token**，按谷时电价计算**每 GPU 小时收入约 38 美元**。开源推理框架从 vLLM/SGLang「双寡头」进入「三足鼎立」。
2. **推理经济学三连数据**——Gartner：2026 推理支出 $233 亿首超训练 $190 亿（55%），2027 升至 59%；中国信通院：2026 推理算力占总负载 **70.5%**；IDC：2025 中国公有云大模型调用 **1944 万亿 token、同比 16 倍**，火山引擎占 49.5%。
3. **Artificial Analysis 成本结构拆解（10/14）**——标准评测任务平均输出 56,445 token，**缓存读取+写入占总成本 58.8%**，纯推理仅 22.1%，最终回答只占 15.2%；闭源模型每任务 $3.51 vs 开源 $0.80（4.4×）。「缓存比推理贵」是 Agent 时代最反直觉的账单。
4. **MiniMax M3.1 Flash「太空兔」（10/19）**——匿名灰度版已真实处理 **200T+ token** 才亮明身份：Agentic 任务代际提升、线上推理速度提升近 3 倍、**使用成本降低 70%**。模型灰度发布 = 路由层的真实流量实验。
5. **CoreWeave × NVIDIA Vera Rubin NVL72 本周投产**——总 token 吞吐较 GB200 最高 **4.8×**，RL 场景每 GPU 输出吞吐 3.8×，Cognition 为首个生产客户；NVIDIA 10/12 已向 OCP 贡献 Vera Rubin 机架设计。
6. **LMCache 0-day RCE（CVE-2026-105192，10/8 披露，CVSS 9.8）**——vLLM 生态最常用的 KV cache 缓存层，ZeroMQ 未认证接口 + Python `pickle` 反序列化外部输入 = 远程代码执行，官方镜像默认 root 运行。**推理基础设施的缓存层第一次成为攻击面**——KV cache 不只是性能件，是信任边界。
7. **「AI 短剧吃掉中国 55% token」之争（10/19）**——中文在线/信通院报告口径 vs 豆包公开反驳「字节总算力只有约 10% 用于视频生成」。产出 token 占比 ≠ 总算力占比，**口径之争本身就是成本工程必修课**。
8. **TGI 进入维护模式（9/5 官宣）**——Hugging Face 官方推荐迁移 vLLM / SGLang，开源 serving 引擎两强格局正式定型；NVIDIA Dynamo 1.0 production-ready，K8s 侧 llm-d / Gateway API Inference Extension 成新战场。

---

## 一、今日算法题：拼车（LC 1094）—— 差分数组：token 预算时间线容量校核

### 题目

有一辆容量为 `capacity` 的班车，要接 `trips[i] = [num_passengers, start_i, end_i]` 表示第 i 趟行程：从 `start_i` 上车 `num_passengers` 人，在 `end_i` 下车（下车发生在容量校验之前——到达 end 的乘客先下车）。问：能否在不超载的前提下完成所有行程？

```
输入：trips = [[2,1,5],[3,3,7]], capacity = 5
输出：true      // [1,3)：2 人；[3,5)：2+3=5 人；[5,7)：3 人 —— 全程 ≤ 5

输入：trips = [[2,1,5],[3,5,7]], capacity = 4
输出：false     // 第 5 站先下 2 人再上 3 人 = 3 人？不对——end==start 时先下车后上车，[5,7) 是 3 人 ≤ 4 → true
                // 反例应是 [[3,2,7],[3,7,9],[8,3,9]] capacity=5：[3,7) 区间 3+3=6 > 5 → false
```

约束：`1 <= trips.length <= 1000`，`0 <= start_i < end_i <= 1000`。

### 工程重述：推理网关的「预算预约」问题

把班车换成 **agent 推理网关的 token 预算池**：

- `capacity` = 网关每分钟的 token 预算（比如 $100/分钟 折算成 token 配额，或显存池容量）；
- 每个 `trip` = 一个 agent 任务的执行窗口 `[start, end)`，`num_passengers` = 它预约的 token 量；
- 问「能否接单全部任务」= **预算时间线上的容量可行性校验**——这是网关预算护栏（本周主题之一）最底层的算法原语：不是等账单爆了再熔断，而是**接单前就把时间线扫一遍**。

### 思路：差分数组 + 扫掠（Difference Array + Sweep Line）

直觉解法：对每个行程，把 `[start, end)` 区间内每个站点都加上人数，最后检查有没有站点超载——O(n·T)，T=1000 时 10⁶ 勉强能过，但面试官要的不是这个。

关键观察：**我们从不关心每个站点的绝对人数，只关心「变化量」**。

- `diff[start] += p`：在 start 站，车上多了 p 人（事件发生）；
- `diff[end] -= p`：在 end 站，车上少了 p 人（事件撤销）；
- 从左到右累加 `load += diff[i]`，就是在还原「第 i 站车上实载人数」；
- 全程维护 `max(load)`，`max(load) <= capacity` 即合法。

这就是**差分数组**：区间加 O(1)、一次扫掠 O(T) 还原全序列。它是「扫描线」家族里最朴素的一员——会议室 II（Day 85）是堆版本，今天是纯数组版本。

```
trips = [[2,1,5],[3,3,7]], capacity = 5

diff:  index  0  1  2  3  4  5  6  7
             0 +2  0 +3  0  0 -2 -3  0      // 注意 end 处是「先下车后上车」
load:        0  2  2  5  5  3  3  0          // 累加前缀和
max(load) = 5 ≤ 5 → true
```

**为什么 `end` 处的 -p 正好处理了「先下车后上车」**：load 是「站 i 出发后车上的人数」，在 i=end 这一站先加上 `-p`（下车完成），再看 load——等价于 end 站先下车再上车，题目语义天然吻合。

### 代码

Go：

```go
func carPooling(trips [][]int, capacity int) bool {
    diff := make([]int, 1002) // 题目 end ≤ 1000，开 1002 防越界
    for _, t := range trips {
        p, s, e := t[0], t[1], t[2]
        diff[s] += p
        diff[e] -= p // end 先下车，天然满足题意
    }
    load := 0
    for i := 0; i <= 1000; i++ {
        load += diff[i]
        if load > capacity {
            return false // 超载站点定位：第一个 load > capacity 的 i
        }
    }
    return true
}
```

Python：

```python
from collections import defaultdict

def car_pooling(trips, capacity):
    # 通用版：站点不连续 / 站点范围巨大（10^9）时用 map 差分 + 排序扫掠
    diff = defaultdict(int)
    for p, s, e in trips:
        diff[s] += p
        diff[e] -= p
    load = 0
    for site in sorted(diff):          # 只扫「有事件的站点」，O(k log k)
        load += diff[site]
        if load > capacity:
            return False
    return True
```

### 复杂度

- 数组版：时间 **O(n + T)**，空间 **O(T)**（T = 站点数上限 1000）。n 和 T 同阶时就是线性。
- 通用 map 版：**O(n log n)**（排序事件点）/ **O(n)** 空间。站点域巨大（如时间戳到毫秒级）时这是正解。

### 高频连环问

**Q1：和会议室 II（LC 253，Day 85）什么关系？为什么那边用堆这边不用？**
会议室 II 求「最少需要几辆车」= 最优解问题，要按结束时间复用资源 → 最小堆维护最早结束；拼车只问「给定的 capacity 够不够」= 判定问题，差分数组一次扫掠就够，杀鸡不用牛刀。**判合法用差分，求最优用堆**——把这个对偶说出口，两道题就都白了。

**Q2：找出超载的具体时刻 / 超载了要踢掉哪些行程？**
第一个 `load > capacity` 的站点就是超载时刻（代码里 return false 的位置）。要定位「肇事行程」：扫到超载点后，回溯所有覆盖该点的行程（`s <= i < e`），按 per-trip 优先级（收益/重要性）排序踢除直到合法——这就是网关做预算拒单/降级的工程版。

**Q3：行程在线到达（流式），每来一个 trip 问一次「现在还合法吗」？**
静态差分不支持快速增量。升级数据结构：
- 站点域小：**树状数组 / 线段树**做区间加 + 全局 max 查询，单操作 O(log T)；
- 只问合法性：维护当前 `max(load)` 的**有序多重集合**（map 版），新增 `[s,e)+p` 只影响 diff 中 s 和 e 两个点的后续前缀和……严格说增量要重算后缀，所以工程上选线段树最稳。
- 面试答法：先讲「如果允许离线批处理就攒批做差分」，再讲「强在线就线段树」，最后补一句「真实网关是滑动窗口 + 令牌桶（Day 50），本题是它的事前可行性校验层」。

**Q4：双资源约束呢？比如每趟行程除了人数还有行李数，车有人数上限和行李上限。**
两维完全独立：**做两遍差分，两个预算分别判**，`load_p <= cap_p && load_w <= cap_w`。工程映射：agent 网关的**双预算 = token 配额 × 并发槽位**，二者都满足才放行——今天代码加一层循环的事，但面试里说出「双预算独立校验」是加分点。

**Q5：求「完成全部行程所需的最小容量」？**
`min capacity = max(load)`，扫一遍取峰值即可 O(n+T)。这就是容量规划：面试官问「这个网关该配多少预算」，答案就是历史流量时间线的峰值（再加 headroom，通常 20-30%）。

**Q6：站点是浮点数 / 时间戳怎么办？**
事件压缩：只对事件点（start/end）排序扫掠，这就是 map 版。若区间还有权重重叠计算（比如求每个点被多少区间覆盖的最大值），抽象为「区间 stabbing max」，通用解是**离散化 + 差分 + 排序事件**三板斧。

**Q7：能不能用前缀和代替差分？**
差分数组和前缀和互为逆运算：对差分数组求前缀和 = 还原原数组；对原数组求差分 = 构造差分数组。**区间加用差分、区间查询用前缀和**，树状数组则把两者统一成 O(log n) 的「区间加 + 区间查询」。能说清这层关系，说明数据结构不是背的。

**Q8：复杂度能不能再压？**
数组版已是最优 O(n+T)——必须读入所有行程 Ω(n)，必须扫过站点域 Ω(T)，下界就在那里，差分正好贴着下界。被追问「有没有 O(n)」时诚实答：T 与 n 同阶时本算法就是 O(n)。

### Fermi 估算加餐：DeepSeek 的 155 万亿 token/天是什么概念？

把今天的算法塞进真实数字：155 万亿 token/天 ÷ 86400 秒 ≈ **18 亿 token/秒**（单集群）。再除以 $38/GPU·h 的账：一天每 GPU 产出 $912 收入。如果单 GPU 稳态输出 ~3000 token/s（Flash 级稠密小模型合理量级），一个集群 ≈ 60 万张 GPU 量级才够这个吞吐——**「单集群」的定义已经是超集群时代的事**。面试里做这种换算，比背任何 benchmark 数字都更能证明你对规模有体感。

---

## 二、面试技巧：Inference 系统工程总览——Agent 时代的推理基础设施

### 0. 为什么这一周值得单独开 Week 22

三组数字，三个结论：

| 数据（全部本周/近两周内） | 结论 |
|---|---|
| Gartner：推理支出 $233 亿 > 训练 $190 亿（2026，史上首次） | 钱在往推理流，**面试题的重心也在往推理流** |
| Artificial Analysis：缓存读写占 Agent 任务成本 **58.8%**，推理 22.1%，回答 15.2% | Agent 的成本结构里，**上下文搬运 > 生成本身** |
| 信通院：2026 推理算力占总负载 70.5%；IDC：中国公有云调用量同比 16 倍 | 这是持续性 7×24 运营支出，不是一次性训练账单 |

上周 MiMo 账里 rollout 占 43.8%（Week 21 视角），这周把镜头再推近一格：**同一张 GPU，在 7×24 的推理流水线上，每一分钱是怎么花出去的、每一段延迟是怎么产生的**。面试官问「设计一个 LLM 服务平台」，考的就是这张图。

### 1. 一张地图：请求的一生（先把骨架画对）

```
客户端 → [API 网关] → [路由层] → [推理引擎集群]
                                        │
           ┌────────────────────────────┼────────────────────────────┐
           ▼                            ▼                            ▼
       Prefill 池                 KV Cache 层                 Decode 池
   (compute-bound,           GPU HBM → CPU DRAM           (memory-bandwidth-bound,
    吃满 tensor core)        → SSD → 远端池(Mooncake/      每步重读全量 KV)
                             FlexKV/LMCache)
           │                            │                            │
           └──────────── KV 传递(NIXL/RDMA) ────────────────────────┘
                                        │
                                     响应流（TTFT → TPOT 流式吐出）
```

两个必须脱口而出的延迟指标：
- **TTFT**（Time To First Token）= 首 token 延迟，主要被 prefill 决定 → 长 prompt 场景的命门；
- **TPOT / ITL**（Time Per Output Token / Inter-Token Latency）= 流式体感，主要被 decode 步的显存带宽决定 → KV cache 越大每步越慢。

面试起手式：「延迟分两段看，TTFT 看 prefill 算力，TPOT 看 decode 带宽，优化手段完全不同，所以第一步永远是**拆账**而不是调参。」——这句话本身就是 DeepSeek/SGLang 团队面试时的标准思维。

### 2. Serving 架构四代演进（每代一句话 + 一个锚点）

| 代 | 机制 | 解决的问题 | 锚点 |
|---|---|---|---|
| 1. Static Batching | 等请求凑齐再跑 | —（基线） | 短请求等长请求，GPU 空转 |
| 2. Continuous Batching | 每个 decode step 重组 batch，完成的踢出、新进的插入 | 队头阻塞 | **Orca**（2022，iteration-level scheduling），如今是所有引擎的默认 |
| 3. PagedAttention / RadixAttention | KV cache 按 block 分页管理，跨请求前缀复用 | 显存碎片 + 重复 prefill | **vLLM**（2023）/ **SGLang**（2024） |
| 4. Disaggregated P/D 分离 | prefill 与 decode 拆到异构资源池，KV 经 RDMA 传递 | prefill 拖死 decode（干扰）、资源画像错配 | **DistServe**（OSDI'24）/ **Splitwise**（ISCA'24）/ **Mooncake**（FAST'25 best paper）/ **NVIDIA Dynamo**（2026 GA） |

**2026 年的现状（面试报版本号用）**：P/D 分离已从论文进生产——vLLM 0.28（8/26）把多模态的 encoder/prefill/decode 拆成**三相**；SGLang 有 EPD disaggregation + HiCache 多级缓存；K8s 侧 **llm-d** 和 Gateway API Inference Extension 在做集群级路由；**DeepSeek 10/20 开源 DeepInfer** 直接把它在昇腾 950 和 NVIDIA 双栈上的线上框架扔了出来，155 万亿 token/天/集群是自带的成绩单。务实提醒：单节点 8 卡以下，P/D 分离是负优化（运维复杂度 > 收益），chunked prefill 拿掉大部分红利——**低于 8 GPU 诚实说「不拆」比硬背架构图加十倍分**。

### 3. KV Cache 与 Prefix Caching：显存才是并发上限

**(a) 显存账（必须会算）**：服务一个请求的 KV 显存 ≈ `2（K+V）× 层数 × kv_heads × head_dim × 序列长度 × 每元素字节数`。70B 级模型 32 层、GQA 8 KV heads、128 维、FP16：每 token 约 0.5MB——**8 万 token 的长上下文单请求就要 40GB**，一张 H100 裸卡只够撑 2 个并发。所以 KV cache 管理直接决定并发数，这就是 PagedAttention 存在的理由。

**(b) Prefix caching 的三类天然红利**（谁命中谁知道）：
1. **System prompt / 指令模板共享**：所有请求共享同一前缀 → 一次 prefill 永久复用；
2. **多轮对话**：第 N 轮只需 prefill 新增部分（vLLM/SGLang 自动按 block 命中）；
3. **Agent 批量任务**（Week 21 回响）：同一 tool schema、同一环境描述发给 2.5 万条 rollout——组内共享前缀命中率极高，异步攒批让同时到达的请求更多，命中更稳（Day 136 伏笔回收）。

**(c) 多级 KV 存储**：纯 HBM 放不下 → **分层**：GPU HBM → CPU DRAM（容量 10×、带宽 100× 差距）→ SSD → 远端池。代表系统：**Mooncake**（Moonshot，KV-centric 架构，RDMA transfer engine 报告 87–190 GB/s，2 月进 PyTorch 生态）、**FlexKV**（腾讯×NVIDIA）、**LMCache**（学术圈最常用的 vLLM 缓存层）。加上 DeepSeek 的 **MLA**（低秩压缩 KV，把 KV 体积砍掉一个量级）——**KV cache 是 2026 推理系统的主战场**，没有之一。

**(d) 安全插播——LMCache CVE-2026-105192（9.8 分，10/8 披露）**：KV 缓存层 ZeroMQ 未认证 + `pickle` 反序列化 = RCE，官方镜像默认 root。**教训三层**：①缓存层已经是信任边界——它和模型权重一样在数据路径上，安全评审不能跳过；②pickle 反序列化是 Python 生态的永恒命门，序列化协议选型（protobuf/JSON/msgpack）是设计题考点；③「性能组件」和「安全组件」的边界是假命题，LMCache 的例子说明每一个新缓存层都是新的攻击面。Week 20 的防御纵深在这里复用。

### 4. 模型路由与语义缓存：省钱的第一梯队

**(a) 模型路由三档**：
- **规则路由**：按租户/任务类型/输入长度硬编码（便宜可控，Coverage 优先用）；
- **分类器路由**（RouteLLM 式）：小模型预测「这道题大模型能答好的概率」，概率高才放行贵模型——经典数字：**保 95% 质量砍 2× 成本**；
- **Fallback 路由**：快模型超时/低置信 → 升级到强模型。工程细节：fallback 要带「已花 token」上下文，避免重复 prefill（语义缓存接住，见下）。

**(b) 语义缓存（semantic caching）**：exact-match 缓存 key = 相同 prompt 哈希；semantic = embedding 相似度 > 阈值即命中。GPT-6 系列的 **Ultrafast 模式（10/9，Blackwell 上 8× 速度）** 本质是「路由 + 缓存 + 投机解码」打包的产品化。MiniMax「太空兔」（10/19）则是另一个极端：**匿名模型灰度 200T token 验证质量和成本后才亮身份**——模型路由层的 A/B 实验已经是发布流程的一部分，不是运维技巧。

**(c) 路由 + KV 的合流**：DigitalOcean × Inferact 的 prefix-aware routing、llm-d 的 cache-aware scheduler——**把请求路由到「已经持有它的 KV」的节点**，路由决策开始吃掉缓存红利。这是 2026 下半年 serving 论文最热的方向（PrefillShare、KVCOMM、DroidSpeak 全在卷「跨请求/跨模型的 KV 复用」）。

### 5. 网关预算护栏：今天的算法题就是它的内核

Week 21 收官金句说「成本异常 = 行为异常」。本周把它工程化：

- **per-agent token 硬预算**：每个 agent 实例发 token 配额，网关按今天的「拼车校验」在时间线上预检；
- **成本异常熔断**：单 agent 花费斜率突增（如 10×）→ 熔断转人工——失控循环的第一个信号往往就是烧钱斜率（Day 141 混合题④闭环）；
- **配额三件套**：限流（rate，令牌桶，Day 50）、配额（quota，周期总量）、并发（slot，同时跑几个）——三轴独立，缺一个都有逃逸路径；
- **「Token 原生」控制平面**（信通院白皮书 8/29）：调度器看的是 **Token 吞吐 / TTFT / 单 token 成本**，不是 CPU 利用率——监控指标换了物种，运维心智模型整个要换。

### 6. 推理成本工程：把账单拆到不能再拆

**成本公式（背这个就够打大多数 follow-up）**：

```
单 token 成本 = (GPU 折旧 + 电力 + 机房 + 人力) / 有效吞吐 token/s
```

优化只有三条路：**分子变小**（便宜的卡/绿电/摊薄运维）、**分母变大**（批处理/缓存命中/量化/投机解码）、**不算账的部分砍掉**（浪费的 retry、loop、重复 prefill）。

本周真实数字当弹药：
- Artificial Analysis：**缓存读写占 58.8%** —— 上下文搬运是 Agent 账单大头 → 压缩 context（Day 124）、prefix 命中、语义缓存，每一个都在砍这 58.8%；
- MiMo 账（回收）：rollout 43.8% → Loop Pruning 是 ROI 第一刀；
- GPU 租金（10/13 周）：H100 $2.98 / B200 $5.40 / **B300 $7.27（单周 +6%，较 B200 溢价 28%→35%）**——新架构溢价在走阔，旧卡（A100）靠残余价值续命到 2029；
- CoreWeave 债券收益率一度 >15% vs B300 租金走强——**硬件在涨价，融资成本也在涨，只有系统效率是你可以掌控的变量**；
- DeepSeek DeepInfer：**$38/GPU·h**——把「每 GPU 小时产值」当成 serving 团队的北极星指标，这个口径本身就值得抄。

**「AI 短剧 55%」之争（10/19）当口径教材**：中文在线/信通院说的是**输出 token 占比**，豆包反驳的是**总算力占比**——预训练不产出用户可见 token 却吃满算力。被问「你们的 AI 成本多少」时，先反问「按哪个口径」：token 口径、算力口径、还是账单口径。会分口径的人，才有资格谈降本。

### 7. 模拟面试：组合题两连

**题 1：设计一个支撑 10 万并发 agent 的推理网关。**

六步骨架（和 Week 21 同构，推理版）：
1. **量化画像**：QPS、输入/输出长度分布（幂律）、TTFT/TPOT SLO、缓存命中率假设——先拿数字，DeepSeek 155 万亿 token/天可以当 Fermi 锚；
2. **引擎选型**：vLLM / SGLang / DeepInfer / TensorRT-LLM 按硬件栈选（NVIDIA 全系 TRT-LLM 有极致优化；昇腾选 DeepInfer；快速迭代选 vLLM）；单节点 <8 卡不拆 P/D，集群级上 disaggregation + Mooncake KV 池；
3. **缓存策略**：prefix caching 开满（block 对齐 system prompt），热点前缀预热，多级 KV（HBM→DRAM→SSD）按成本分层；
4. **路由与护栏**：规则+分类器双层路由（保 95% 质量砍 2×），fallback 语义缓存防重复 prefill，per-agent 三轴配额 + 成本异常熔断（今天那道题做预检）；
5. **成本模型**：单 token 成本公式摊到每租户，缓存读写占比（对标 58.8%）进监控大盘；
6. **可观测**：TTFT/TPOT p99、KV 命中率、队列深度、每 GPU·h 产值（对标 $38）——四个指标报警线写死。
收尾点睛：「LMCache CVE 的教训我记着——缓存层进信任边界评审清单。」

**题 2：上线后 TPOT p99 突然恶化 3×，怎么排查？**

分层剥洋葱：
1. **是不是负载变了**：输入长度分布右移（长 KV → 每步带宽翻倍）？新上了长上下文租户？
2. **是不是缓存冷了**：prefix 命中率掉 → 相同流量下 prefill 翻倍、KV 池被打满，抢占/换出加剧 → decode 每步更慢；
3. **是不是邻居干扰**：P/D 没分离的集群里一个长 prefill 插进来（chunked prefill 上限失控？）；混布的其他任务抢带宽；
4. **硬件降级**：ECC 纠错、显存降频、RDMA 链路抖动（NVLink 错误计数）；
5. **引擎行为变更**：版本升级换了调度器默认值（gpu_memory_utilization、max_num_batched_tokens）——**先查变更，再查流量，最后查硬件**，这是 Week 21 混沌纪律在推理侧的同款。

### 8. 本周金句

> **「推理系统的第一课：prefill 是算力生意，decode 是带宽生意，而 Agent 时代这俩都让位给第三门生意——搬运生意。当缓存读写占掉 58.8% 的成本，优化推理不再是优化模型，是优化数据流动；而数据流动的每一道闸，都同时是一道安全边界。」**

### 9. Day 142 自检清单

- [ ] 能不画图说出请求生命周期：网关 → 路由 → prefill → KV 层 → decode → 流式响应
- [ ] 能背 TTFT / TPOT 分别由什么决定、对应什么优化手段
- [ ] 能讲 continuous batching 为什么解决队头阻塞，PagedAttention 为什么解决碎片
- [ ] 能算 KV cache 显存账（层数 × heads × dim × 长度 × 2 × 字节）
- [ ] 能说出 prefix caching 的三类红利场景，并把它和 Week 21 rollout 组内共享前缀连起来
- [ ] 能背 P/D 分离的收益阈值（8 GPU 以下别拆）与 KV 传输的工程 crux（RDMA/NIXL/Mooncake）
- [ ] 能讲模型路由三档 + 语义缓存，并各给一个本周真实案例（太空兔灰度 / Ultrafast / RouteLLM 95% 砍 2×）
- [ ] 能复述成本公式（分子三项 ÷ 有效吞吐）+ 三个本周真实数字（58.8% / $38·GPU·h / B300 $7.27）
- [ ] 能用拼车算法讲清网关预算预检，并说出三轴配额（限流/配额/并发）
- [ ] 能讲 LMCache CVE 的三层教训（缓存层=信任边界 / pickle / 性能件无豁免权）

---

### 明日预告（Day 143 · Week 22 Day 2）

**KV Cache 管理深潜**：PagedAttention block 调度、RadixAttention 树结构、抢占与换出（preemption/swapping）、MLA 低秩压缩——候选算法题：区间加/区间最值类（线段树/树状数组）作 KV 分层调度的微缩模型。
