# Day 161 · 2026-11-12 · Week 25 Day 3 🛟 容错日

> **本周主题**：Agentic RL 训练基础设施实战（rollout 基础设施与异步解耦 ✓ → 权重同步与训练-推理一致性 ✓ → **奖励服务与 rollout worker 容错** → 训练安全与成本工程 → 收官复盘）
>
> **今日算法题**：LFU 缓存（LC 460 · O(1) get/put = reward server 的热度分层 = APRIL buffer 的淘汰策略）
>
> **面试技巧**：奖励服务与 rollout worker 容错——reward server 延迟吞吐账本、worker 故障域三档划分（失联/卡死/半残）、APRIL buffer 设计、失败 episode 归因与重试、设计题「十万 QPS 奖励服务的负载均衡与降级」
>
> 🔥 **今日时事锚点 = Anthropic IPO 获数倍超额认购（彭博 11/12：订单达发行股数数倍，承销商考虑把发行价上调至 $70-78 区间之上，完全摊薄估值冲 1.5 万亿美元、总募资或破 1000 亿；暗盘已炒至 1.8 万亿；11/13 纽约中午 Amodei × Dimon 直播对谈；日本配售 30-35 亿美元）——洪峰来了，是无限放量还是上调价格？这是一道容量规划题** × **智元发布终身具身智能框架 LIFE（11/11：RoboBeats 轻量决策模型给机器人接"心跳"做条件跟踪与事件发现 + Plastic Memory 可塑性记忆 + EDM 能力复用——worker 健康检查、checkpoint、失败归因沉淀三件套的行业版）** × **vLLM 官方博客 11/11《Encoder Disaggregation: EPD》（metadata-only 多模态请求、专用 encoder 执行、跨 encoder 缓存——把 encoder 从 decode 进程拆出去 = 教科书级故障域划分）** × **Mercor D 轮 7.5 亿美元估值 200 亿（11/11：英伟达参投，模型评估与后训练数据="评估即产业"，reward 是 RL 的燃料这门生意值两个百亿）** × **背景板：腾讯混元 Hy4 调用激增十倍扩容（11/5-6）、MiniMax H3-Image 开源 10 秒 4K（11/11）**

---

## 一、今日算法题：LFU 缓存（LC 460）

### 题目

设计一个容量为 `capacity` 的缓存，支持 `get(key)` 和 `put(key, value)`，要求**两个操作都是 O(1)**：

- `get(key)`：key 存在返回值（**并使其访问次数 +1**），不存在返回 -1
- `put(key, value)`：key 已存在则更新 value（访问次数 +1）；不存在则插入；**容量满时淘汰「访问次数最少」的 key；访问次数相同则淘汰「最久未访问」的**

进阶：要求在 O(1) 时间完成淘汰决策。

### 为什么今天做这题

这是 Day 161「奖励服务与容错」主题的算法化身，两道工程映射：

**映射一：reward server 的热度分层。** reward 打分很贵——LLM-as-judge 一次打分就是一次推理调用。但 trajectories 高度重复：同一批 prompt 的多个 rollout 共享 prefix、被重试的 episode 重新提交、相同 (task, seed) 的复现请求……所以生产级 reward server 一定是分层的：L0 规则引擎（微秒级、无需缓存）→ L1 **确定性 reward 缓存**（trajectory 哈希 → score，完全可缓存，命中即免一次 judge 调用）→ L2 LLM-judge 池（批量推理）。L1 这层用什么淘汰策略？——**打分过的轨迹还会被再次打分，"还会被用"就是频率**。LFU 的 `minFreq` 就是缓存里"最不可能再省到钱"的那条轨迹。Mercor 估值 200 亿做的"模型评估"生意，内核就是这个缓存命中率。

**映射二：APRIL buffer 的淘汰策略。** Day 159 登场过的 APRIL（partial rollout 复用，+44% 吞吐、最高 +8% 精度）：worker 挂了，已完成的 episode 片段存进 buffer，新 rollout 续跑而不是重跑。buffer 容量有限——哪些 partial episode 值得留？**被续跑次数多的（频率）、最近被续跑过的（新近性）、信息密度高的（权重）**。这就是 LFU 的三要素，APRIL 论文里"buffer 偏向已完成 episode 反而提精度"的秘密，用 LFU 的话说就是：**高频近期片段的期望复用价值最高，淘汰决策应该让位给热度**。

### 思路：三个哈希表 + minFreq 指针

难点一句话：LRU 只要"最近性"一条轴（哈希表 + 双向链表），LFU 多了"频率"维度，所有操作还是 O(1) 的前提是——**频率本身就是桶的索引**。

```
kv      : key → [value, freq]              // 值和当前频率
freqMap : freq → 双向链表（该频率的所有 key，头部=最新访问）
minFreq : 当前最小频率（淘汰时直接用，不用扫）
```

**get(key)**：
1. `kv` 查不到 → -1
2. 拿到旧频率 f，把 key 从 `freqMap[f]` 链表摘除，挂到 `freqMap[f+1]` 头部
3. 若 `freqMap[f]` 空了 且 `f == minFreq` → `minFreq++`（**唯一需要动 minFreq 的地方：它只可能从 f 涨到 f+1**）
4. 更新 `kv[key].freq = f+1`

**put(key, value)**：
- 已存在：更新 value，走一遍上面的提频流程（等价于 get）
- 不存在且满：从 `freqMap[minFreq]` **尾部**（最旧）摘一个 key，三处同删（`kv`、链表、`minFreq` 桶若空不管，反正马上重置）
- 插入：`kv[key] = [value, 1]`，挂 `freqMap[1]` 头部，**`minFreq = 1`**（新元素频率必然最小——这是 `minFreq` 的复位点）

**正确性要害**：淘汰永远只看 `freqMap[minFreq]` 的尾部。因为 minFreq 是全局最小频率，该桶尾部是同频率里最久没动的——"最少访问 + 最久未访问"两条 tie-break 规则同时被满足。

### Go 实现（container/list 版）

```go
type LFUCache struct {
    capacity int
    size     int
    minFreq  int
    kv       map[int]*entry        // key → entry
    freqMap  map[int]*list.List    // freq → 链表（存 *entry，头部最新）
}

type entry struct {
    key, value, freq int
    elem             *list.Element // 在 freqMap[freq] 里的链表节点
}

func Constructor(capacity int) LFUCache {
    return LFUCache{
        capacity: capacity,
        kv:       make(map[int]*entry),
        freqMap:  make(map[int]*list.List),
    }
}

func (c *LFUCache) touch(e *entry) {
    // 摘除旧桶，挂进 f+1 桶头部
    old := e.freq
    fl := c.freqMap[old]
    fl.Remove(e.elem)
    if fl.Len() == 0 {
        delete(c.freqMap, old)
        if c.minFreq == old { // 唯一让 minFreq 上移的分支
            c.minFreq++
        }
    }
    e.freq++
    c.freqMap[e.freq].PushFront(e) // 注意：PushFront 后要把 elem 回写
    e.elem = c.freqMap[e.freq].Front()
}

func (c *LFUCache) getOrCreateList(f int) *list.List {
    if l, ok := c.freqMap[f]; ok {
        return l
    }
    l := list.New()
    c.freqMap[f] = l
    return l
}

func (c *LFUCache) Get(key int) int {
    e, ok := c.kv[key]
    if !ok {
        return -1
    }
    c.touch(e)
    return e.value
}

func (c *LFUCache) Put(key, value int) {
    if c.capacity == 0 {
        return
    }
    if e, ok := c.kv[key]; ok {
        e.value = value
        c.touch(e)
        return
    }
    if c.size == c.capacity {
        // 淘汰：minFreq 桶的尾部 = 最少访问里最旧
        l := c.freqMap[c.minFreq]
        victim := l.Back().Value.(*entry)
        l.Remove(victim.elem)
        if l.Len() == 0 {
            delete(c.freqMap, c.minFreq)
        }
        delete(c.kv, victim.key)
        c.size--
    }
    // 插入：频率 1，且 minFreq 复位为 1
    e := &entry{key: key, value: value, freq: 1}
    c.getOrCreateList(1).PushFront(e)
    e.elem = c.freqMap[1].Front()
    c.kv[key] = e
    c.minFreq = 1
    c.size++
}
```

**复杂度**：get / put 均 **O(1)**——所有操作都是哈希表定位 + 链表头尾操作，没有任何扫描。空间 O(capacity)。

**面试现场三处易错**（都藏在 `touch` 里）：
1. `elem` 摘除后要回写新桶的 `Front()`，否则下次摘除的是野指针
2. `minFreq++` 的条件是"旧桶空了 **且** 旧频率就是 minFreq"——写成只判空会漏掉"删的是非最小桶"的情形
3. put 已存在的 key 也走 `touch`（频率 +1），不能只改 value——这是 LFU 和 LRU 的行为差异，面试官最爱追问

### 进阶六连

1. **LRU（LC 146）对照**：哈希表 + 一条双向链表（头最新尾最旧），get/put 提到头部，满时砍尾部。LFU = 把"一条链表"升级成"按频率分桶的链表数组"。**面试话术：LRU 防的是"冷数据占位"，LFU 防的是"一次性热点刷榜"——笔试题刷流量的 burst 会把 LRU 打穿（大量新 key 进来把真正的热数据挤出去），LFU 需要 burst 每个 key 访问够次数才能上位。反过来 LFU 的弱点是新数据要爬坡，可能刚进来就被淘汰（minFreq=1 的桶最先挨刀）**——两个结构的缺陷互补，引出下面两条。
2. **抗突发：2Q / LRU-K / ARC**：2Q 用一个 FIFO 准入队列缓冲 burst，存活两次才进主 LRU（新数据先观察，不直接抢热数据位置）；ARC 再加自适应调节，是 Linux page cache 思想的商业化版。**工程结论：纯 LFU 几乎不直接上生产，生产是"准入窗口 + 频率"的混合体。**
3. **TinyLFU（Caffeine 的默认，工程首选）**：Window-TinyLFU = 1% 窗口（SLRU）+ Count-Min Sketch 频率估计 + 准入策略（新元素频率估计值 > 被踢者才准入）。用 4-bit 计数器的 sketch 把频率统计压到几 KB。**reward 缓存的答案就是这个：L1 缓存别写裸 LFU，写 W-TinyLFU，burst 来了热数据不被冲掉**。
4. **带 TTL 的 LFU**：过期和淘汰是两个正交维度——TTL 管"还能不能用"，LFU 管"先扔谁"。实现 = 桶内链表节点带过期时间，淘汰前先惰性清理桶头过期节点（和 Day 160 的 LC 480 延迟删除双堆同构）。**reward 语义缓存必备**：judge 结果带温度，TTL=10 分钟，过期后宁可重打分不返回陈旧奖励。
5. **分布式 LFU**：一致性哈希分片，每个分片本地跑 LFU；跨分片热度用 sketch 汇总（呼应 Day 160 的"分布式中位数 sketch 可合并性"——**频率 sketch 天然可合并，加法就行，这是它优于精确 LRU 的分布式性质**）。reward server 集群的 L1 缓存就是这么搭的。
6. **加权 LFU**：不同 episode 的打分成本不同、复用收益不同——按"节省的 judge 调用成本"加权：权重 = 轨迹长度 × judge 单价 × 期望复用次数。淘汰时踢"加权价值最低"而不是"频率最低"。**这才是 reward 缓存的真实目标函数：缓存不是命中率游戏，是省钱游戏。LFU 是它在"单位价值均匀"假设下的闭式解。**

---

## 二、面试技巧：奖励服务与 rollout worker 容错

### 0. 先把 reward server 在训练循环里的位置钉死

```
trainer ──下发任务──▶ rollout worker（GPU 上的 vLLM/SGLang，产 episode）
                          │  episode 完成
                          ▼
                      reward server（打分：规则 / LLM-judge / 混合）
                          │  (trajectory, reward) 对
                          ▼
                      训练侧（算 advantage → 梯度 → 下一步权重）
```

三个高压事实：
- **rollout worker 是生产者，reward server 是收银台**。收银台慢，整条流水线在等小票；收银台挂，今天的货（episode）全部无法入账（没有 reward 的 trajectory 对训练是废数据——它无法参与 advantage 计算）。
- **reward 延迟藏在尾部**。`T_iter = max(T_rollout, T_train)`（Day 159）里，episode 的"完成"是以拿到 reward 为准的——reward P99 慢 10 秒，rollout 队列的收尾就被拖 10 秒，又回到**尾部定价**。
- **reward 自身是个推理服务**。LLM-as-judge 打分 = 一次模型调用，agentic 场景一次打分要看几千 token 的轨迹 + rubric，judge 池就是第二个小 vLLM 集群。**所有推理服务的工程手段（批处理、缓存、限流、降级、故障域）在 reward server 上全部适用一次**——这也是为什么今天的算法题是缓存。

### 1. Reward server 的延迟吞吐账本（三个数字开口报）

面试官问"reward 服务怎么设计"，先报账本再谈架构：

| 轴 | 指标 | 红灯线 | 工程手段 |
|---|---|---|---|
| 吞吐 | QPS（episode/step × 并发） | judge 池饱和、排队水位持续上涨 | 批量推理、L1 缓存（今天算法题）、削峰队列 |
| 延迟 | 打分 P50/P99 | P99 超过 rollout 收尾预算 | 分层（规则 <1ms / 缓存命中 <5ms / judge 批推理）、降级 |
| 可用性 | 打分成功率 | <99.9% 直接拖停训练迭代 | 降级路径、超时熔断、重试预算 |

关键话术：**"reward 的超时预算不是拍脑袋，是从 T_iter 倒推的——训练 step 允许 rollout 收尾 30 秒，judge P99 就要压在 10 秒内，剩下 20 秒是留给出队和重试的余量。超时的 episode 先入 pending 队列打'延迟分'，trainer 容忍少量 reward 滞后到位（最终一致），而不是让 worker 干等。"**

### 2. Worker 故障域三档划分（今天的核心考点）

一个 agentic RL 集群里 rollout worker 的死法不止"挂了"一种，**分档决定处置**：

| 档位 | 现象 | 探测 | 处置 |
|---|---|---|---|
| **失联（crash）** | 进程没了，心跳消失 | 心跳超时（秒级） | 最简单：无状态重启、拉最新 checkpoint（Day 159 RLinf 模式），损失一个 update cycle |
| **卡死（hang）** | 进程活着但零推进：死锁、工具调用无限等待、GC 长停顿、等一个永远不会来的响应 | **liveness 正常 + progress 为零**（Day 155 的 L3 进展探针：进展事件时间戳 + 步数计数器，静默超阈值 = suspicion） | 换策略重试 → 回滚 checkpoint → 升级人工，三层递进；**不能只看心跳——只看心跳的卡死 worker 会永远占着 GPU 空转烧卡时** |
| **半残（degraded）** | 还能产 episode，但变慢/部分工具超时/返回质量下降 | 行为基线偏离：episode 完成速率、工具成功率、轨迹长度分布 vs 7 天基线（呼应 Day 156） | **最阴险的一档**：不摘出会拖累整个 batch 的吞吐和 reward 分布；隔离进"观察池"、降权重、限制领新任务，同时保留现场归因 |

三档背后是一条通用纪律：**故障域划分先于处置**——

- **进程内隔离**：一个 worker 节点跑多个 sandbox / 多条 episode 时，单条 episode 卡死（工具死循环）不能拖死整节点 → cgroup 限额、超时熔断、每条 episode 独立超时预算。
- **服务间隔离**：rollout worker 的故障不能传染 reward server，反过来 reward 慢了不能让 worker 的请求无限堆积 → **请求舱壁（bulkhead）**：worker 对 reward 的调用带超时 + 有界队列，排满了就走降级而不是堆积。
- **vLLM 11/11 的 EPD 就是这个思想的产品化**：官方博客《Encoder Disaggregation》把多模态请求的 encoder 从 decode 进程里拆成专用执行单元，metadata-only 请求调度过去、跨 encoder 缓存——**decode worker 的故障不再连带 encoder，encoder 的慢不再拖住 decode 的队列，还能独立扩缩容。把"不同故障性格"的组件拆开，就是故障域划分的教科书操作。**

### 3. APRIL buffer：把"失败"存起来排队复用

Day 159 报过数字：partial rollout +44% 吞吐、最高 +8% 精度。今天把它拆开讲——APRIL 本质是一个**带淘汰策略的半成品仓库**：

- **存什么**：episode 片段（已完成 segment 的 prefix + 轨迹 + 已得 reward），挂 (task_id, segment_index) 键
- **谁复用**：新 rollout 领到同一任务时，先查 buffer 有没有现成片段可续跑，有就从断点续，没有从头来
- **淘汰谁**：容量有限——被续跑次数多、最近被续跑、信息密度高的留（**LFU + 新近性 + 加权**，就是今天的算法题）；长期没人续跑的片段（对应任务已被消化/任务池轮换）踢掉
- **为什么反而提精度**：buffer 偏向已完成 episode → 续跑片段的信息密度天然高于随机重采——**复用旧片段让 advantage 估计的方差更小**（同一 prefix 下比较不同后缀，受控对比）

面试加分话术：**"APRIL 的 44% 可以读成缓存命中率：worker 故障/抢占本来要全额重跑，有了 buffer 就命中了历史片段。所以它的淘汰策略就是缓存淘汰策略——LFU 守热度、新近性守时效、权重守信息密度。这跟 reward server 的 L1 缓存是同一个数据结构在训练管线里出现了两次。"**

### 4. 失败 episode 归因与重试（九成候选人的认知盲区）

**RL 里"失败的 episode"不等于废数据。** 归因决定处置，分四类：

| 归因 | 特征 | 处置 | 重试？ |
|---|---|---|---|
| 环境/基础设施故障 | sandbox 挂、工具超时、reward 服务挂、worker 被抢占 | 轨迹保留则重打分，轨迹丢失则重新 rollout | **安全**（episode 生成非确定，重试 = 新采样，天然幂等——这是 RL 相对传统系统重试的免费午餐） |
| reward 服务故障 | trajectory 完整但没分 | 重放回打分队列，幂等键 = trajectory hash | **安全**，且最优先做（episode 已生成，重打分零成本） |
| 任务本身不可解 | 环境 bug、题目错误、工具缺失 | 剔除出任务池 + 记录 + 反哺任务生成器 | **不要重试**——重试是在反复验证一个错误 |
| **模型答错** | episode 完整走完、reward 已打分（低分/负分） | **保留！这是训练信号** | 不重试——负 reward 和正 reward 一样值钱，扔掉它等于把训练分布剪成"只会成功" |

三条铁律：

1. **"没有 reward 的 episode 才是废数据，低 reward 的 episode 是负样本。"** 把"episode 失败"和"episode 无分"混为一谈，是面试官 fishing 的重灾区。agentic RL 里最容易出的账是：环境故障率 2%，意味着每步都有 2% 的轨迹没 reward——**训练侧要容忍"当期小比例缺分"，用最终一致补账（延迟 reward 到达后并入下一 step），而不是同步等所有分齐了才更新**。
2. **重试四件套照搬 Day 156**（退避 + 抖动 + 预算上限 + 错误分类），但 RL 有一个特殊简化：episode 重试天然幂等（不存在"上次其实成功了"的问题——每次采样都是新的），**幂等负担落在副作用上**：episode 里如果有工具调用产生了外部副作用（写文件、发请求），重试前要确认副作用已回滚或幂等。
3. **归因要留痕**：每次失败打上 (worker_id, task_id, 故障档位, 归因类别) 进审计（哈希链，Day 149 的老朋友）。**归因分布本身是训练健康度的一等指标**——环境故障率突增 = 集群出问题，"任务不可解"占比上升 = 任务生成器退化，两者都是 P0 信号。

### 5. 设计题：十万 QPS 奖励服务的负载均衡与降级

**三澄清**（开口必问）：
1. 奖励构成？——纯规则（好办，无状态水平扩）/ 纯 LLM-judge（核心在 judge 池容量）/ 混合（按比例估资源）
2. 打分时效？——同步（卡 episode 收尾）还是可异步（先入队，延迟到账）？agentic RL 通常允许小比例延迟
3. 确定性要求？——规则 reward 必须确定性（同轨迹同分，才可缓存）；judge 可容忍温度，但同轨迹同 checkpoint 重放要有界（呼应 Day 155 确定性重放）

**四步答法**：

1. **分层架构**：入口 LB → 规则引擎层（无状态副本水平扩，本地完成 <1ms）→ 语义缓存层（trajectory hash → score，LFU 淘汰，命中 <5ms）→ judge 推理池（GPU 批量推理，动态 batch）→ 持久队列（打分结果落盘，供训练侧拉取 + 审计重放）。
2. **负载均衡**：规则层 round-robin 即可；judge 层用**最少在途请求数（least in-flight）**而不是 round-robin——judge 请求长度方差极大（500 token 轨迹和 30K token 轨迹差 60 倍），轮询会把长请求堆到同一副本。**least in-flight 是推理服务的正确 LB 算法，面试说出这四个字秒杀一半候选人。**
3. **降级路径（按代价升序）**：judge 超时 → ① 重试一次（带抖动）→ ② 规则兜底分（启发式：格式合法给中性分，标注 degraded）→ ③ 历史均值/同任务均分 → ④ 延迟打分（episode 挂 pending，标注延迟 reward，训练侧最终一致吸收）。**每一档都要在结果里打降级标记——降级分混进正常分等于污染 reward 分布，下游 advantage 会被系统性拉偏（这是 reward hacking 的合法近亲）。**
4. **隔离与背压**：规则层与 judge 层独立部署（不同进程池/节点，EPD 同款故障域）；入口按排队水位背压——水位 > 80% 时新请求直接进 pending 队列不占用 judge 连接，> 95% 时非关键任务拒收。**容量参考今天的热点：Anthropic 的承销商面对数倍超额认购，选择是"绿鞋机制（1.2375 亿股计划内弹性容量）+ 上调定价"，而不是无限接单一我们的 reward 服务也一样：预留弹性容量（judge 池按 P99 的 1.3 倍配），超了就用降级和排队兜底，永远不要让打分延迟失控地传染给 rollout。**

### 6. 模拟面试速答五连

**Q1：rollout worker heartbeat 正常但产出归零，怎么排查？**
> 这是"卡死"档，不是"失联"档。先看 L3 进展探针：进展事件时间戳停在哪一步 → 卡在哪个工具调用（工具超时预算生效了吗？）→ 单 worker 还是批量（批量 = 下游服务故障，单点 = 该 episode 触发死循环/长等待）→ 处置三层递进：换策略重试 → 回滚 checkpoint 续跑 → 升级人工。**金句收尾：心跳证明线程活着，progress 证明任务活着——92 字节的 /healthz 回答不了"agent 是不是在空转"。**

**Q2：reward server P99 突然从 800ms 涨到 8s，训练还没挂，你先做什么？**
> 先背压后止损：入口把排队阈值收紧（保已进队的），judge 池看 batch 大小和排队深度（变大了 = 容量不足；没变 = 单条长尾，看是不是某类超长轨迹）→ 打开降级开关保吞吐（标注 degraded）→ 查缓存命中率是不是掉了（prompt 漂移/哈希键变更，命中率是最便宜的报警器）→ 最后才是扩容。**顺序不能反：先让系统别恶化，再让系统恢复，最后才加资源。**

**Q3：worker 半残（慢但能干活），摘还是不摘？**
> 先隔离进观察池再定量：限制它领新任务的速度，同时看两个数——它的 episode 完成速率和 reward 分布 vs 集群基线。偏离小 → 留在观察池自愈（可能是网络抖动）；偏离大 → 摘掉重启，现场封存归因。**半残 worker 最大的危害不是少产 episode，而是用异常的轨迹分布污染 batch 的 advantage 估计——吞吐损失是线性的，分布污染是收敛性的。**

**Q4：episode 失败率多少算正常？怎么设阈值？**
> 分账设阈值，不设总阈值：环境故障率红线 1-2%（超了 = 集群有病），任务不可解率盯趋势（缓慢上升 = 任务生成器退化，是 P1），模型答错率不设上限（那是训练信号本身）。**归因之后再报警，混合指标报警=没有报警——币安案监控盯金额阈值不盯速率分位数的教训，在训练集群里就是"盯失败总数不盯归因分布"。**

**Q5：为什么 APRIL 用"偏向已完成 episode"的 buffer 反而提升最终精度？**
> 三个机制：① 复用片段信息密度高（已完成 segment 带 reward，续跑只需补后缀，方差更小）② 受控对比——同一 prefix 上比较不同后缀，advantage 估计更干净 ③ 等效于给难任务（长 episode、易中断）加采样权重。**缓存淘汰策略就是数据课程策略：LFU 选出来的"高频片段"，恰好是该任务分布里梯度信息最密集的部分。**

### 7. 今日金句

> **Reward 是训练系统的收银台：rollout 生产 episode，reward 给它们定价——定价慢了，整条流水线在等一张小票；定价挂了，今天的货全部白跑。所以 reward server 的全部要义是三句话：打分要快（缓存分层，让重复的轨迹直接记账）、坏 worker 要分档（失联的拉起来，卡死的踢出去，半残的隔离掉——最危险的是那个还能干活但越来越慢的）、失败要归因（环境坏了重试，judge 坏了重打分，模型答错了——那是训练信号，千万别扔）。Anthropic 的承销商在数倍超额认购面前选择绿鞋机制和上调价格而不是无限放量，智元给机器人接上心跳与记忆，vLLM 把 encoder 拆成独立故障域——三个行业，同一门课：容量永远给热度让路，故障永远先分域再处置。而 APRIL 用 44% 的吞吐告诉我们：失败不是垃圾，是没排好序的资产——每一条失败的 episode，都应该是下一次更便宜复用的库存。**

### 8. 自检清单（10 项）

- [ ] LFU 三哈希表结构能默写（kv / freqMap / minFreq），并能说出 minFreq 的两个变更点（旧桶空且是 minFreq 时 +1、插入新 key 时复位 1）
- [ ] get/put 已存在 key 时都要 touch 提频，且能解释为什么（频率是访问驱动的，不是插入驱动的）
- [ ] 能对比 LRU 与 LFU 各自的失效场景（burst 刷榜 vs 新数据爬坡），并说出 2Q/TinyLFU 是怎么补的
- [ ] 能报 reward server 三本账（QPS / P50·P99 / 打分成功率）并各配一个工程手段
- [ ] worker 三档故障（失联/卡死/半残）的探测信号和处置路径能脱口而出
- [ ] 能解释 bulkhead 在 worker → reward 调用链上的作用（有界队列 + 超时，不堆积）
- [ ] 能复述 APRIL buffer 四要素：存什么 / 谁复用 / 淘汰谁 / 为什么提精度
- [ ] 失败 episode 四类归因（环境/reward 服务/任务不可解/模型答错）处置表能默写，重点是不扔负样本
- [ ] 设计题四步：三澄清 → 分层架构 → least in-flight LB → 按代价升序的降级路径（含 degraded 标记）
- [ ] 金句能背（收银台三句话 + 失败是资产）

---

## 三、明日预告

**Day 162（Week 25 Day 4 🔐 成本日）：训练安全与成本工程**——reward hacking 的攻防（规格博弈/退化策略/审计 reward）、训练成本四本账（GPU 时/带宽/存储/人力）、spot 实例与抢占的艺术（RLinf $5,357→$3,571 复盘）、预算熔断与成本护栏、收官前的训练安全检查表。候选题：LFU 带 TTL 进阶 / 设计题「训练成本实时监控与自动熔断系统」。时事前瞻：Anthropic 11/19 挂牌日逼近，开盘定价 = 一场全球直播的负载测试；Fable 5.2 API 降价 20% 后，token 经济学的成本曲线再校准。
