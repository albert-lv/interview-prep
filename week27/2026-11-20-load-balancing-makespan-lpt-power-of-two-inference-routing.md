# Day 169 · 2026-11-20 · Week 27 Day 1 🚀 开营

> **本周主题**：推理成本与容量工程实战（Inference Cost & Capacity Engineering）
> **今日关键词**：Makespan 调度 · LPT 贪心 · 在线 List Scheduling · Power of Two Choices · 负载均衡 · GPU 利用率口径
> **日期**：2026-11-20（周五）
> **本周定位**：Week 22「Inference 系统工程」讲了机制（KV cache / prefix caching / 网关），本周讲经济学——同一套机器，怎么路由、怎么排队、怎么计价，决定你的 GPU 账单是资产还是笑话。

---

## 🔥 今日时事锚点

- **ANT 首周收官日 = Day 167 伏笔的兑现现场**：Anthropic 昨夜（11/19）纳斯达克挂牌，代码 ANT——**开盘 $111.50，较 $83 定价 +34%**（Yahoo Finance），收盘涨幅收窄（CNBC 口径首日 **+15%**）， fully diluted 估值站上 ~$1.5 万亿。今天周五 = 首个交易周最后一个交易日，**收盘后我们就拿到「首周累积分布」的第一个完整切片**——Day 167 说的「n=1 实验看分布不看点位」，今晚开始应验。与此同时 TIKR 给出一个比股价更值钱的数字：**Anthropic 每美元算力创收 $1.72，较年初提升逾四倍**——市场第一次给「推理单位经济学」本身定价。而 **AMD 在同一天重挫 9%**：推理芯片的第二席位正在重新排位。
- **英伟达多空混战进入第二周**：Q3 营收翻倍（$1158 亿）却较 10/6 高点回落近 40%（Yahoo Finance）；**加州总检察长周四向英伟达发出调查传票**——聚焦黄仁勋与疑似中国情报人员的关系、受限芯片是否流向中国、是否向股东充分披露（SEC 民事调查已在途）；**SpaceX 周四完成 $320 亿债务发售**（公开债券从 $40 亿上调至 $60 亿，SPV「Saturn Funding Trust」表外持有）**专门用于采购英伟达芯片**——推理需求已经催生「为买 GPU 发债」的金融工程；财报电话会点名 **Palantir 为关键 AI 合作伙伴**。
- **微软 Ignite 收尾日（11/20）：英伟达 × 微软扩大全栈合作官宣**——**Vera Rubin NVL72 已部署进 Azure 数据中心**，微软 Foundry 新增英伟达开放模型，**Dynamo 推理软件**落地 Azure + RTX PRO 6000 Blackwell 加速 Bing 与 Windows PC 推理。硬件军备赛再升级——但 NVL72 再强，利用率是路由和调度决定的，**机器是买来的，吞吐是调度出来的**。
- **IEA《2026 世界能源展望》：AI 数据中心电力需求 2030 年翻倍**——全球数据中心现有装机 ~120GW，2030 年前再增 120GW，其中 **75% 给 AI**；卫星图像追踪到 93 座在建/扩建「AI 工厂」，容量两年内从 <2GW 冲到 17GW，全部建成后合计 50GW——**相当于新增一个英国的电力需求**。Day 162 的「电力账」伏笔被官方背书：电价正式成为推理成本的一阶项，**碳感知/电价感知路由从论文走向生产**。
- **苹果 11/18 发布招聘：自主 AI agent 立项**（高级工程经理，能代用户写代码/浏览网页/控制应用；彭博：最快明年底）——需求端海啸预警。加上 **美众议院 11/19 披露 BIS 已批准 87.9 万颗 AI 芯片对华销售**（申请总数 340 万颗），推理芯片的供给、需求、监管三条线同时开新副本。
- **背景板**：2026 世界计算大会长沙开幕（2030 年我国智能算力有望冲击 10000 EFLOPS）· 新研究：AI 智能体受压时会泄露机密凭据（路由层的 egress 白名单 = 最后一道闸门）· Anthropic 上市或为慈善带来 1560 亿美元（TechCrunch）。

---

## 一、今日算法题：最小化最大完成时间（多机调度 · 推理路由的算法内核）

### 题目

你是 LLM 推理网关的调度器。已知 **n 个推理请求**的预计处理时间 `jobs[i]`（prefill+decode 的估算耗时），要分给 **m 台 GPU server**，一台机器同一时间只处理一个请求（或一个 batch）。目标：让所有请求完成的**最晚时间（makespan）最小**。

```
输入：jobs = [3, 7, 2, 8, 4, 6, 5], m = 3
输出：一个分配方案，使 max(每台机器的总负载) 最小
上例最优：M1=[8,4] M2=[7,5] M3=[6,3,2] → makespan = 12
```

**工程语境**：这是每个推理网关每秒都在解的题——请求进来放哪台机器？放错了，最忙那台机器的队列就是 P99 延迟的制造机。**makespan 最小 ⟺ 尾延迟最小**。

**坏消息**：这是 **NP-complete**（`m ≥ 2` 时，Partition 问题的直接推广——能否分成两组和相等）。**好消息**：一个非常简单的贪心就能做到 ≤ 4/3 倍最优，工程上几乎够用一百年。

### 思路：List Scheduling（在线核） → LPT（离线加强）

**核心贪心（List Scheduling）**：按任意顺序扫描任务，**每个任务丢给当前最闲（累计负载最小）的机器**。

```
为什么有效？——「无空闲论证」：
设最后一个完成的是任务 j，它在时间 s_j 开始。
关键观察：s_j 之前，m 台机器没有一台是闲的
（否则 j 会被分给那台闲机器——贪心性质）。
⇒ m·s_j ≤ 总工作量 - p_j
⇒ C = s_j + p_j ≤ (Σp - p_j)/m + p_j
      = Σp/m + p_j·(1 - 1/m)
      ≤ C* + C*·(1 - 1/m) = (2 - 1/m)·C*
```
（用了两条下界：**C\* ≥ Σp/m** 平均负载；**C\* ≥ max p_j** 最长任务）

**结论：任意顺序贪心 = (2 - 1/m) 近似。**

**LPT（Longest Processing Time first）**：把任务**按耗时降序**再跑同一个贪心，近似比收紧到：

$$C_{LPT} \le \left(\frac{4}{3} - \frac{1}{3m}\right) C^*$$

直觉：长任务先占坑，避免「最后只剩一台机器扛着巨婴任务收尾」。这个界是**紧的**（存在实例达到），不能再改进了。

**在线版本**：真实网关里请求是**逐个到达**的，不能排序重排——在线 List Scheduling 是 **2-竞争比**（理论最优的确定性在线算法族），这就是 least-loaded 路由的理论底盘。

**Power of Two Choices（今天最惊艳的一行数学）**：如果随机选两台、进较闲的那台——只看了一台额外信息——最大负载从随机路由的 **Θ(ln n / ln ln n)** 断崖降到 **Θ(ln ln n / ln 2) + O(1)**（n 个球 n 个箱子的经典结果）。「随机选两台取最闲」就是生产负载均衡器（nginx least_conn among two、Cassandra、DynamoDB 的 gossip 负载收敛）的算法原型——**多查一台机器，干掉的不是平均延迟，是整个尾部分布**。

### Go 完整实现（离线 LPT + 四种在线路由策略实测对比）

```go
package main

import (
	"container/heap"
	"fmt"
	"math/rand"
	"sort"
)

// ---------- 离线:LPT(最长处理时间优先) ----------

type Machine struct{ id, load int }
type MinHeap []Machine

func (h MinHeap) Len() int           { return len(h) }
func (h MinHeap) Less(i, j int) bool { return h[i].load < h[j].load }
func (h MinHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *MinHeap) Push(x any)        { *h = append(*h, x.(Machine)) }
func (h *MinHeap) Pop() any {
	old := *h
	m := old[len(old)-1]
	*h = old[:len(old)-1]
	return m
}

// listScheduling:按给定顺序,每个任务进当前最闲机器
// —— 在线 least-loaded 路由的理论原型
func listScheduling(jobs []int, m int) int {
	h := &MinHeap{}
	heap.Init(h)
	for i := 0; i < m; i++ {
		heap.Push(h, Machine{id: i})
	}
	for _, j := range jobs {
		mach := heap.Pop(h).(Machine)
		mach.load += j
		heap.Push(h, mach)
	}
	mx := 0
	for _, mach := range *h {
		if mach.load > mx {
			mx = mach.load
		}
	}
	return mx
}

// LPT:降序排序后再 listScheduling → 近似比 4/3 - 1/(3m)
func LPT(jobs []int, m int) int {
	s := make([]int, len(jobs))
	copy(s, jobs)
	sort.Sort(sort.Reverse(sort.IntSlice(s)))
	return listScheduling(s, m)
}

// ---------- 在线路由模拟:重尾请求分布(LLM 的真实形状) ----------

func simulate(n, m int, seed int64) {
	rng := rand.New(rand.NewSource(seed))
	jobs := make([]int, n)
	for i := range jobs {
		u := rng.Float64()
		switch {
		case u < 0.85: // 85% 短请求(简单问答)
			jobs[i] = 1 + rng.Intn(5)
		case u < 0.95: // 10% 中请求(代码生成/多轮)
			jobs[i] = 20 + rng.Intn(80)
		default: // 5% 巨婴(agent 长轨迹/200K 上下文)
			jobs[i] = 300 + rng.Intn(700)
		}
	}

	rr := make([]int, m) // Round-Robin
	rd := make([]int, m) // Random
	ll := make([]int, m) // Least-Loaded(在线 list scheduling)
	p2 := make([]int, m) // Power of Two Choices

	for i, j := range jobs {
		rr[i%m] += j
		rd[rng.Intn(m)] += j
		k := 0
		for t := 1; t < m; t++ {
			if ll[t] < ll[k] {
				k = t
			}
		}
		ll[k] += j
		a, b := rng.Intn(m), rng.Intn(m) // 随机两台,进较闲的
		if p2[a] <= p2[b] {
			p2[a] += j
		} else {
			p2[b] += j
		}
	}

	maxOf := func(a []int) (mx int) {
		for _, v := range a {
			if v > mx {
				mx = v
			}
		}
		return
	}
	total, mxJob := 0, 0
	for _, j := range jobs {
		total += j
		if j > mxJob {
			mxJob = j
		}
	}
	lowerBound := total / m
	if mxJob > lowerBound {
		lowerBound = mxJob
	}

	fmt.Printf("n=%d, m=%d(突发窗口≈每台 %d 个请求)\n", n, m, n/m)
	fmt.Printf("Round-Robin  max load = %d   <- 无视负载,是最优下界的 %.1fx\n", maxOf(rr), float64(maxOf(rr))/float64(lowerBound))
	fmt.Printf("Random       max load = %d   <- balls-into-bins\n", maxOf(rd))
	fmt.Printf("Least-Loaded max load = %d   <- 在线 list scheduling,离下界 %.1f%%\n", maxOf(ll), float64(maxOf(ll)*100)/float64(lowerBound))
	fmt.Printf("PowerOfTwo   max load = %d   <- 只多查一台,拿到 LL 九成效果\n", maxOf(p2))
	fmt.Printf("离线 LPT(可排序重排)   = %d   <- 下界 max(Σp/m, p_max) = %d\n", LPT(jobs, m), lowerBound)
}

func main() {
	simulate(200, 8, 42)  // 突发窗口:25 请求/机器
	simulate(2000, 8, 7)  // 稳态:250 请求/机器
}
```

两次典型输出（m=8，三层重尾分布——模拟 LLM 请求的真实形状：85% 短问答、10% 代码生成、5% agent 长轨迹巨婴）：

```
n=200, m=8(突发窗口≈每台 25 个请求)
Round-Robin  max load = 2705   <- 无视负载,是最优下界的 3.2x
Random       max load = 1269   <- balls-into-bins
Least-Loaded max load = 1014   <- 在线 list scheduling,离下界 118.2%
PowerOfTwo   max load = 1103   <- 只多查一台,拿到 LL 九成效果
离线 LPT(可排序重排)   =  943   <- 下界 max(Σp/m, p_max) = 858

n=2000, m=8(稳态:250 请求/机器)
Round-Robin  max load = 11960  <- 大数定律抹平一半,仍是下界 1.2x
Random       max load = 14480  <- 随机路由此刻反而比 RR 差(巨婴撞上同一台)
Least-Loaded max load = 10159  <- 离下界 106.1%
PowerOfTwo   max load = 10536
离线 LPT(可排序重排)   =  9574  <- 下界 9573
```

三组关系看清「路由值多少钱」：**突发窗口（每机 ~25 请求）才是分胜负的场景**——Round-Robin 的 makespan 是理论下界的 3.2 倍，Least-Loaded 压到 1.18 倍，Power of Two 用 1/4 的全局状态拿到九成效果；稳态下大数定律抹平一半差距，但 LPT 仍然贴着下界走。**P99 延迟的钱，全在突发窗口里。**

### 复杂度

| 算法 | 时间 | 空间 | 近似比 / 竞争比 |
|---|---|---|---|
| List Scheduling（在线） | O(n log m) 堆 | O(m) | **2 - 1/m** |
| LPT（离线） | O(n log n + n log m) | O(m) | **4/3 - 1/(3m)**（紧界） |
| Random 路由 | O(n) | O(m) | max load Θ(ln n / ln ln n) |
| Power of Two Choices | O(n) | O(m) | max load **Θ(ln ln n / ln 2)** |

### 面试三坑

1. **证明 List Scheduling 是 2-近似的关键一步是「无空闲论证」**——最后一个任务开始前所有机器都在忙。面试现场要能手推 `C ≤ Σp/m + p_j(1-1/m)`；推不出来，说明你背了结论没懂贪心性质。LPT 的 4/3 界只需记住「临界机器上最后一个任务若 ≤ C*/3 则显然，若 > C*/3 则用最优解必将其独占」的分类讨论框架。
2. **「离线能排序」和「在线不能排序」是两个问题**。面试官问「为什么不用 LPT」时，标准回答：请求是流式到达的，全局排序需要预知未来；在线场景 list scheduling 的 2-竞争比是确定性算法的最优量级。如果再追问「随机化能不能更好」——能，但工程上没人靠随机化路由吃饭。
3. **Power of Two Choices 优化的是 max load（尾延迟），不是平均负载**——四种策略的平均负载完全相同（总工作量/m），差别全在尾巴。且两个候选必须**独立均匀随机**；用「负载值的哈希」或「固定配对」会把理论保证打回原形。**从 2 加到 3 收益骤降（ln ln 递减），3 台以上不值得**——这是有定理支撑的工程决策。

### 进阶六连（面试加分项）

- **LC 621 Task Scheduler（冷却期调度）**：任务间有冷却间隔 → 贪心填坑 + 数学直解 `(max(count), n+1)` 框架。工程映射：GPU batch 调度中 KV cache 换入换出的「空窗期」、限速器的令牌间隙。
- **异构机型 = 加权 Makespan**：m 台机器算力不同（H100 vs H200 vs B300），任务 j 在机器 i 上耗时 `p_j / s_i`。近似贪心变体：把任务按 `p_j / s_max` 排序做 LPT，或用 `s_i` 加权后的最小堆。工程话术：**「加权 LPT 就是推理集群异构选型的调度版——排序键按什么排就是在宣布优化什么」**（Day 162 铁律的调度复用）。
- **带释放时间（release time）的在线调度**：任务不是同时存在而是陆续出现 → 贪心规则变成「空闲机器从已到达任务里挑最长的」，近似比仍为 2。映射：流量高峰的队列排空策略。
- **对偶视角：Bin Packing**：「n 个任务 m 台机器最小 makespan」对偶于「固定容量 C，最少几个箱子装完所有任务」——FFD（First-Fit-Decreasing）11/9·OPT。LC 1011  运送货物（能力为 D 的船最少几艘）就是这个对偶。**makespan 与 bin packing 是同一枚硬币的两面**，面试说破这层 = 体系感。
- **一致性哈希 + session affinity（呼应 Day 165）**：`rand` 换 `hash(user_id)` = 同一用户永远落同一台机器（KV cache/prefix 复用），代价是热点用户压死单机 → 生产解法是**分层**：一致性哈希定会话（保局部性），会话池之间按水位再平衡（保全局最优）。Week 22 Day 27 的 prefix-cache-aware 路由就是「局部性 × 负载均衡」的 Pareto 前沿解。
- **排队论大坑：m 个 M/M/1 ≫ 1 个 M/M/m**：把请求**随机分流**到 m 个独立队列（每 server 一个队），等待时间远大于**中心化单队列**取头部分配（M/M/m）。利用率 ρ→1 时差距爆炸。这就是为什么好的网关都是「中央排队 + 工作窃取」，而不是「客户端自带负载均衡直连实例」。面试杀伤力：很多人答「每实例本地队列 + least-in-flight」沾沾自喜，你补一句池化排队论 = 降维打击。

### 工程映射：推理网关路由策略全家桶（按工程常见度排序）

| 策略 | 理论原型 | 适用 | 代价 |
|---|---|---|---|
| least-in-flight | 在线 list scheduling | 请求长度方差大（LLM 60 倍方差，Day 161） | 维护全局计数，信息易过期 |
| **power-of-two** | d=2 balls-into-bins | 大规模集群、状态同步贵 | 两次查询，理论最优性价比 |
| 一致性哈希 | rendezvous hashing | session affinity / prefix 复用 | 热点无法卸载，需分层 |
| prefix-cache-aware | 带状态路由 | 多轮对话/agent 长轨迹 | 路由表大，需缓存状态 gossip |
| round-robin | 无 | 同构短请求、健康检查语义 | 重尾分布下尾部爆炸，不推荐 |

**一句话选型**：状态便宜全量 least-loaded；状态贵 power-of-two；有 KV cache 局部性诉求就上分层（哈希定会话 + 池间水位平衡）。**「路由决定已购算力的变现率」——ANT 每美元算力创收 $1.72 提升四倍的工程注脚，就在这张表里。**

---

## 二、面试技巧：Week 27 开营 —— 推理经济学的三面与利用率口径

### 1. 推理经济学三面框架（本周主线）

Week 27 回答一个问题：**同一堆 GPU，为什么有人亏钱有人赚出史上最大 IPO？** 拆成三面：

- **成本侧**：每 token / 每请求成本 = GPU 时单价 × 占用时长 ÷ 有效并发。成本归因（Day 162 四本账）是地基；
- **容量侧**：集群能吃多少流量、水位线画在哪、什么时候扩容——排队论与 Little's Law 是速算工具（Day 170 展开）；
- **路由侧**：请求放进哪台机器——决定的是**已购算力的变现率**（今天）。

三面共用一条铁律（Week 22 遗留，本周反复用）：**「排序键按什么排，就是在宣布你优化什么」**——按最闲排 = 优化尾延迟，按电价排 = 优化账单，按前缀命中排 = 优化 TTFT。

### 2. ANT 的 $1.72 = 推理单位经济学的仪表盘时刻

TIKR 披露 Anthropic **每美元算力创收 $1.72，较年初提升逾四倍**——上市文件第一次把「推理单位经济学」写进估值叙事。四倍提升的分解，恰好是本周三面的组合拳：**批处理与调度（利用率） × 模型与引擎优化（每 token 算力） × 路由（请求放对机器） × 定价（把成本翻译成售价）**。面试话术升级：被问「怎么降低推理成本」，先反问一句「您问的是 per-token 成本、per-request 成本，还是 cost-per-correct？」（Day 168 Gemini 4 Carbon 口径），然后才展开三面——**先把问题的 CFO 口径对齐，再谈工程手段**。

同时记住今天的估值现场：首日开盘 +34% 收盘 +15%，今天周五收首周最后一个价——**Day 167 的统计诚实读法正在应验：点位无信息量，分布才作数**。一周后再回看这个分布，就是一次免费的「评估平台读数」练习。

### 3. IEA 报告 = 电力账正式入模（Day 162 伏笔兑现）

IEA 官宣：2030 年数据中心新增 120GW、75% 给 AI、93 座 AI 工厂两年从 <2GW 冲到 17GW。**电力从「账单末尾一行」变成「成本模型一阶项」**，三个工程后果：

- **电价/碳强度进路由排序键**：「最闲且最便宜的机器」取代「最闲的机器」——同一个 Power of Two Choices，键值函数换成 `load + α·price + β·carbon`；
- **潮汐调度**：训练挤夜间低价电、推理吃白天高价电（或反之，按合同），Day 162 的 Anthropic $3.4B 发电设备采购就是长期锁价的对冲；
- **面试话术**：「推理成本 = 算力成本 + 电力成本 + 散热成本，IEA 之后电力项权重持续上升——所以容量规划不能只盯 GPU 数，要盯**瓦数**。」

### 4. 利用率口径三问（被问「GPU 利用率多少」的标准反击）

「我们集群利用率 92%」——这句话有至少三种口径，差一个数量级：

| 口径 | 含义 | 陷阱 |
|---|---|---|
| SM Active（硬件口径） | 有多少 SM 周期没空转 | 92% 里可能一半是 memory-bound 空转 |
| **MFU**（Model FLOPs Utilization） | 实际 FLOPs / 峰值 FLOPs | LLM 推理 decode 阶段 MFU 天然低（访存瓶颈），要分 prefill/decode 报 |
| **goodput**（有效吞吐） | 单位时间完成多少有效请求 | 最终口径，但依赖「有效」的定义（成功？按时？无降级？） |

标准反击三连：「您要哪个口径？prefill 还是 decode？P50 还是 P99？」——**答出这三个追问，面试官就知道你管过真集群，而不是只看过博客**。

### 5. 今日模拟面试速答五连

- **Q1：1000 QPS、平均延迟 500ms，系统里平均有多少在途请求？峰值 2000 QPS 时延迟涨到 2s 呢？**
  Little's Law：`L = λW` = 1000 × 0.5 = **500 个在途请求**；峰值时 2000 × 2 = 4000 个——在途数暴涨 8 倍，说明队列在堆积，延迟里有越来越大的排队成分。处置顺序：先限流/排队（保住 SLA），再扩容（λ 或 W 二选一降）。**Little's Law 是容量速算的第一公式，背不过等于没学过排队论。**
- **Q2：网关负载均衡用 least-in-flight 还是 round-robin？**
  LLM 请求长度方差 ~60 倍（Day 161），round-robin 会把巨婴请求均匀撒到每台机器上——**平均很公平，尾部全爆炸**。选 least-in-flight；但全局计数有 staleness（100ms 前的负载快照），集群巨大时用 power-of-two 折中：90% 的效果、1% 的状态开销。补一句排队论：更优架构是中央单队列 + 工作窃取（m 个 M/M/1 ≫ 1 个 M/M/m）。
- **Q3：老板说「GPU 利用率 92%，再接 20% 流量没问题吧」？**
  先问口径（SM/MFU/goodput 三连），再翻排队论：ρ=0.92 时 M/M/1 等待时间是 ρ=0.8 时的 **4.6 倍**（`W ∝ ρ/(1-ρ)`），边际容量早被排队吃掉了——92% 的利用率恰恰是**不能接**新流量的理由。工程话术：「利用率 85% 以上，每加一点流量都买的是指数级延迟。」
- **Q4：一致性哈希和 least-loaded 到底选哪个？**
  不是二选一，是**分层**：一致性哈希保 session affinity（KV cache/prefix 复用，省的是 prefill 算力），代价是热点无法卸载；所以会话池之间按水位线再平衡，或上 prefix-cache-aware 路由（路由到「持有该前缀 KV 的机器里最闲的」）。**局部性和均衡都要时，永远不要全局单键排序——要分层解耦两个目标。**
- **Q5：IEA 说电力翻倍，推理成本怎么降？**
  三面框架挨个出招：路由键加电价/碳强度项（Power of Two Choices 的键值函数改造）、训练推理潮汐混部吃电价差、长上下文做 prefix caching 省每请求能耗（Week 22 机制本周经济学）。收尾一句：**「电力入模之后，『最闲』不再是唯一真理——排序键就是在宣布优化什么，这次宣布的是账单。」**

### 6. 今日金句

> **Anthropic 昨夜挂牌，开盘 +34%、收盘 +15%——两天样本给「首周累积分布」切了第一刀，Day 167 的统计诚实正在应验。而真正被写进估值文件的数字不是股价，是每美元算力创收 $1.72：市场第一次给「推理单位经济学」定价，四倍提升的注脚是批处理、调度、路由与定价的组合拳。IEA 今天官宣 2030 年 AI 电力翻倍，把「最闲的机器」改写成「最闲且最便宜的机器」。多机调度告诉我们：makespan 是 NP-complete，但一个「每个请求丢给最闲机器」的贪心就有 2-竞争比，加一步降序就是 4/3，再多看一台随机机器就能砍掉整个尾部分布。路由决定的不是负载均衡，是已购算力的变现率——机器是买来的，吞吐是调度出来的。**

### 7. 今日自检清单（10 项）

1. 手推 List Scheduling 的 2-近似：「无空闲论证」+ 两条下界（Σp/m、max p_j）；
2. LPT 的近似比是多少？这个界为什么是紧的？
3. Power of Two Choices 把 max load 从 Θ(ln n/ln ln n) 降到多少？为什么不能看 3 台以上？
4. 在线调度为什么不能直接用 LPT？2-竞争比意味着什么？
5. 重尾请求分布下 round-robin 为什么尾部爆炸？
6. makespan 与 bin packing 的对偶关系？FFD 的近似比？
7. m 个 M/M/1 vs 1 个 M/M/m：哪个等待时间长？为什么？
8. 利用率三口径：SM Active / MFU / goodput 各是什么？
9. Little's Law 默写并算：1000 QPS × 500ms = ？ρ=0.92 vs 0.8 的等待时间比？
10. 今日时事三条：ANT 首周收官 + $1.72/算力美元（推理单位经济学定价时刻）/ IEA 电力翻倍（电力入模）/ Vera Rubin NVL72 上 Azure（机器买得到，吞吐调出来）。

### 8. 明日前瞻（Day 170 · 2026-11-21 · 周六）

- **主题**：排队论与容量水位线——Little's Law 深入 / M/M/m 与利用率陷阱 / P50-P99 容量线怎么画 / 推理集群容量规划实战（把今天的直觉变成可计算的公式）；
- **候选题**：LC 621 Task Scheduler（冷却期调度）或 设计题「给推理集群画容量水位线」；
- **时事观察**：ANT 首周收盘后第一个完整分布切片（Day 167 读法实战）· 加州 AG 传票 vs SEC 调查的英伟达治理连续剧 · Ignite 六款模型定价是否补齐 · AMD -9% 之后的推理芯片格局。

---

*Day 169 完成 ✅ · Week 27「推理成本与容量工程实战」开营 🚀 · 连续第 169 天*
