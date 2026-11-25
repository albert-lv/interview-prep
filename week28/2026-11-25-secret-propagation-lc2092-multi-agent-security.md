# Day 174 · Week 28 Day 1 🚀 开营 —— 秘密传播与多智能体通信安全

> 📅 2026-11-25（周三）· 主题周：Multi-Agent 系统架构实战（开营日）
> 🔥 今日时事锚点 = **Microsoft Agent Framework 遭遇 PyPI 供应链攻击（11/17-19：恶意版本 agent-framework 1.26.1 / 1.26.2 窃取 Azure 凭证·GitHub Actions 密钥·CI 环境变量——Python 启动即自动执行·上线不到一小时·微软安全公告：「凡安装过这两个版本的系统应视为已失陷，所有可达凭证必须全量轮换」= 多智能体基础设施本身成为攻击目标——你用来构建 agent 的框架，就是攻击者的入口）** × **Agent 协议栈正式补完（11 月：MCP[agent→工具] + A2A[agent→agent] + AG-UI[agent→用户] 三层协议齐备，媒体称「智能体时代的 TCP/IP」——但协议 ≠ 安全：TCP/IP 诞生到 TLS 普及隔了二十年，agent 协议的安全层现在在哪？）** × **Gartner 预警：2027 年底前 40%+ 的 agentic AI 项目将被取消（ROI 不清晰 + 风险控制薄弱——解药原话：「治理架构必须与编排系统并行建设，不能事后补票」·McKinsey 2026 AI Trust 调查：近 2/3 受访者把安全列为扩展 agentic AI 的最大障碍）** × **Grok 多智能体辩论架构实测（4 agent 默认 / 16 agent Heavy 模式·幻觉率 12%→4.2%——但早期 beta 出现「能力幻觉」：模型声称自己没有的能力或否认已有的能力 = 多 agent 辩论能纠错但也能集体陷入同样盲区——4 个 agent 用同一个底层模型，偏见是相关的）** × **Fireworks AI 泄露事件持续扩大（Notion AI 聊天日志受影响·Cursor 发行密钥暴露 → 历史 agent traces 只读桶泄露·窃取凭证引发 SaaS 横向移动 = agent 间通信日志本身就是秘密载体）** × **Black Friday（11/27）倒计时 2 天——周四感恩节美股休市·周三 = 节前最后完整交易日 = agent 系统年度大考 + 年度最大攻击面同时到来**

---

## 一、今日算法题：得知秘密的所有专家（LC 2092 · 带时间约束的信息传播）

### 题目

一场秘密会议从时间 0 开始。person 0 掌握一个秘密，并在 t=0 告诉了 `firstPerson`。之后人们两两会面，`meetings[i] = [xi, yi, timei]` 表示 `xi` 和 `yi` 在时刻 `timei` 会面。**如果其中一人已知秘密，会面后两人都知道**。秘密只能沿时间正向传播（t=5 知道秘密的人无法影响 t=3 的会面）。返回最终所有知道秘密的人。

```
输入：n = 6, meetings = [[1,2,5],[2,3,8],[1,5,10]], firstPerson = 1
输出：[0, 1, 2, 3, 5]
解释：t=0：0 告诉 1
     t=5：1 告诉 2
     t=8：2 告诉 3
     t=10：1 告诉 5
```

### 思路：Dijkstra 变形 —— 「最早得知时间」最短路径

这道题表面是图论，本质是**带时间约束的单向信息传播**：

- 每个人 = 节点，每次会面 = 一条带时间戳的边
- `dist[i]` = 第 i 个人**最早**在什么时刻知道秘密
- 初始：`dist[0] = 0`（0 号天然知道），`dist[firstPerson] = 0`（0 号在 t=0 直接告知）
- 传播规则：u 在 `dist[u]` 时刻已知秘密，若 u 和 v 在时刻 t ≥ dist[u] 会面 → v 可在时刻 t 得知

**为什么不是普通 BFS**：BFS 的隐含假设是边权恒定（每步代价 = 1）。本题的边权是**时间戳**，传播条件是非对称的——只有会面时间 ≥ 已知时刻才能传播。我们要最小化的是「得知时间」而非「步数」，这是最短路径问题的变形：

**关键松弛条件**：

```
对于边 (u, v, t)：
  如果 t >= dist[u] 且 (dist[v] == -1 或 t < dist[v])：
      dist[v] = t      // v 可以在时刻 t 通过 u 得知秘密
      将 (v, t) 推入最小堆
```

**贪心正确性**：一旦从堆中弹出 time 最小的节点，其 dist 即最终答案——任何后续路径的会面时间必然 ≥ 当前最小值，不可能更早。这与 Dijkstra 的最优子结构同构，只是「边权」从累加和换成了取时间戳。

```go
package main

import (
	"container/heap"
	"fmt"
)

type Item struct{ person, time int }
type PQ []Item

func (p PQ) Len() int            { return len(p) }
func (p PQ) Less(i, j int) bool  { return p[i].time < p[j].time }
func (p PQ) Swap(i, j int)       { p[i], p[j] = p[j], p[i] }
func (p *PQ) Push(x interface{}) { *p = append(*p, x.(Item)) }
func (p *PQ) Pop() interface{} {
	old := *p
	n := len(old)
	it := old[n-1]
	*p = old[:n-1]
	return it
}

func FindAllPeople(n int, meetings [][]int, firstPerson int) []int {
	// 建图：邻接表 person → [(other, time)]
	adj := make([][][2]int, n)
	for _, m := range meetings {
		x, y, t := m[0], m[1], m[2]
		adj[x] = append(adj[x], [2]int{y, t})
		adj[y] = append(adj[y], [2]int{x, t})
	}

	// dist[i] = 最早得知秘密的时间（-1 = 不可能得知）
	dist := make([]int, n)
	for i := range dist {
		dist[i] = -1
	}
	dist[0] = 0
	dist[firstPerson] = 0

	pq := &PQ{}
	heap.Push(pq, Item{0, 0})
	heap.Push(pq, Item{firstPerson, 0})

	for pq.Len() > 0 {
		it := heap.Pop(pq).(Item)
		u, t := it.person, it.time
		if t > dist[u] {
			continue // 过期条目——已被更优时间更新过
		}
		for _, e := range adj[u] {
			v, mt := e[0], e[1]
			// 核心：会面时间必须 >= u 的已知时刻（时间单向性）
			if mt >= t && (dist[v] == -1 || mt < dist[v]) {
				dist[v] = mt
				heap.Push(pq, Item{v, mt})
			}
		}
	}

	var result []int
	for i := 0; i < n; i++ {
		if dist[i] != -1 {
			result = append(result, i)
		}
	}
	return result
}

func main() {
	// 例1：基础链式传播
	fmt.Println(FindAllPeople(6, [][]int{{1, 2, 5}, {2, 3, 8}, {1, 5, 10}}, 1))
	// [0 1 2 3 5]

	// 例2：时间陷阱——t=0 时 1和2 都不知道秘密，即使 2 在 t=1 得知也无法回到 t=0 告诉 1
	fmt.Println(FindAllPeople(4, [][]int{{3, 1, 3}, {1, 2, 0}, {2, 0, 1}}, 3))
	// [0 1 2 3]（1 通过 3 在 t=3 得知，不是通过 2）

	// 例3：最短路径——4 同时和 0(t=0)、1(t=3) 会面，走 0 这条路更早
	fmt.Println(FindAllPeople(5, [][]int{{1, 4, 3}, {0, 4, 0}}, 1))
	// [0 1 4]（4 在 t=0 得知，t=3 的会面无新传播）

	// 例4：反向时间阻断——4 在 t=2 才从 0 得知，t=1 和 5 会面时还不知道 → 5 永远不知
	fmt.Println(FindAllPeople(6, [][]int{{0, 1, 1}, {1, 2, 2}, {2, 3, 3}, {0, 4, 2}, {4, 5, 1}}, 1))
	// [0 1 2 3 4]（5 被排除：4-5 会面在 t=1，早于 4 得知秘密的 t=2）
}
```

### 实测（Go 全跑通 ✓）

```
例1 → [0 1 2 3 5]    基础链式传播 ✓
例2 → [0 1 2 3]      时间陷阱：t=0 的 1-2 会面双方都不知情 ✓
例3 → [0 1 4]        最短路径：4 从 t=0 的 0-4 会面更早得知 ✓
例4 → [0 1 2 3 4]    反向时间阻断：5 被排除（t=1 < dist[4]=t=2）✓
```

例4 是面试手推的杀手锏：它同时考察**时间单向性**和**最短路径取 min**——4 有两条得知路径（0→4 在 t=2、1→4 在 t=3），取更早的 t=2；但 4-5 会面在 t=1 < t=2，所以 5 永远不可能得知。**一条边的时间戳错位，切断了一整条传播链**。

### 复杂度

- 时间 **O((n + m) log n)**：建图 O(m)，每条边最多入堆一次 O(m log n)，弹出处理 O(m log n)。
- 空间 **O(n + m)**：邻接表 O(n + m) + dist 数组 O(n) + 堆 O(m)。

### 面试三坑

1. **直接 BFS 不做时间检查 = 当场挂**。普通 BFS 按步数扩散，会错误地认为「只要连通就能传播」。被追问「为什么不能 BFS」要答出本质：*BFS 的隐含假设是边权恒定，而本题的传播条件是非对称的时间约束——t=5 知道秘密的人无法影响 t=3 的会面。这不是步数最短路径，是时间最早到达*。**无权图 BFS = 计数；带时间戳 Dijkstra = 决策**——和昨天（Day 173）无权/加权区间调度的分水岭完全同构。

2. **忘记初始化 firstPerson**：`dist[firstPerson] = 0` 是题意（person 0 在 t=0 告诉了 firstPerson）。漏掉这一行，firstPerson 只能靠后续会面间知，答案直接错一半。面试手写时先写初始化三行（`dist[0]=0`、`dist[fp]=0`、两个 Push），再写主循环——顺序不能反。

3. **Dijkstra 过期条目**：堆中可能有过期的 `(person, time)`——一个节点先以较大 dist 入堆，之后被更小 dist 更新。弹出时必须检查 `if t > dist[u] { continue }`，否则可能用错误（更晚）的时间去松弛邻居，导致传播链断裂或结果偏大。**手写 Dijkstra 忘写过期检查的候选人 = 面试官心里的「没在生产用过 Dijkstra」标签**。

### 进阶六连

1. **反向问题——最小化初始知情人**：只能选 k 个人作为初始知情人，要让秘密传遍全网，最小化 k = **最小连通支配集**（NP-hard）。工程映射：*多 agent 系统中，选哪些节点做「种子」广播配置更新最经济？* 被问 NP-hard 怎么解 → 贪心近似（每次选覆盖最多未覆盖节点的种子）+ 承认近似比不保证 = 工程诚实。

2. **带衰减的秘密——信息级联模型（IC / Independent Cascade）**：秘密传播 n 次后失真（传话游戏），每条边加成功概率 p → 求「准确知道秘密」的期望人数 = **蒙特卡洛模拟**（跑 10⁴ 次取均值）或 **期望线性方程组**。面试话术：*「Fireworks AI 事件中，一个被窃的 Cursor 发行密钥传播到下游系统的概率，就是一个信息级联模型——每条边（系统间数据交换）有独立的传播概率」*。

3. **并发会面的批处理——Union-Find + 回滚**：同一时间戳的所有会面**同时发生**（不是串行），不能逐个传播。正解：按时间排序 → 相同时间的所有边先 union → 然后检查每组内是否有节点与源点连通 → **不连通的组回滚 union**（rollback Union-Find）。这是进阶面试的高光时刻：*「我可以用并查集做到近线性，但需要支持回滚——按时间分组、组内 union、检查连通性、不连通的撤销」*。

4. **最小化全网传播时间——RTO 估计**：目标改为 `min(max(dist[i]))` → Dijkstra 跑完后取全局 max。工程映射：*agent 配置更新 / 密钥轮换的传播 RTO——Black Friday 期间你有多少秒完成全网凭证轮换？如果答案是 300 秒而 SLA 要求 60 秒，就要加广播节点（把星型拓扑改成树形分层广播）*。**信息传播的 max-dist 就是配置管理的 RTO**。

5. **阻断传播——最小割（Max-Flow Min-Cut）**：删掉最少的边使得秘密传不到目标节点 = **最小 s-t 割**。工程映射：*防止 API key 在 agent 网络中扩散 = 找到信息瓶颈边并切断——这就是 egress 白名单 / 网络隔离的理论基础。被问「怎么防止一个被攻陷的 agent 泄露全网凭证」→ 最小割视角：在 agent 通信图上找 s-t 最小割，在割集上加 ACL*。

6. **多秘密并行传播**：网络中同时传播 k 种秘密（API key、用户 PII、模型权重各自独立传播）→ 每个秘密独立跑 Dijkstra = O(k·(m+n) log n)。工程优化：*共享邻接表结构、按秘密分组批处理、对高频秘密建索引*。被问性能 → 答：*「k 个秘密的 Dijkstra 可以合并成一次多维 Dijkstra（dist[i][secret] 二维状态），但常数项变大——工程上独立跑 k 次 + 并行更实际」*。

---

## 二、工程映射：多智能体通信图的「信息流动控制」

LC 2092 是多 agent 系统安全性的**最小完备模型**：

| 算法概念 | Multi-Agent 系统实体 |
|---|---|
| Person（节点） | Agent 实例（推理 agent、工具 agent、编排器） |
| Meeting（带时间戳的边） | Agent 间通信事件（A2A 消息、MCP 工具调用、AG-UI 交互） |
| Secret（秘密） | API Key、Bearer Token、用户 PII、模型权重、系统 prompt |
| dist[i] = 最早得知时间 | 凭证在网络中的传播时延 |
| 时间单向性 | 凭证传播不可逆——撤销前的窗口期 = 攻击者的自由行动时间 |
| 结果集 = 所有知情人 | 攻击面评估：多少个 agent 持有这个秘密 |

**微软 Agent Framework 供应链攻击的算法视角（11/17-19）**：

```
时间线（LC 2092 反向——攻击者已知秘密，看谁被感染）：
  t=0:   PyPI 发布 agent-framework 1.26.1（含恶意代码）
  t=0:   CI/CD 流水线 pip install → 恶意代码自动执行
  t=0:   Azure token / GitHub Actions secrets / 环境变量被窃取
  t=?:   窃取凭证沿 agent 通信图传播到下游系统
  t=???: 攻击者用凭证横向移动（SaaS lateral movement）
```

这就是 LC 2092 的**反问题**：不是「谁会知道秘密」，而是**「攻击者已知道秘密，他能影响到谁？」**——把 Dijkstra 源点换成被攻陷的 CI runner，跑一遍就知道爆炸半径（blast radius）。

**Fireworks AI 事件中的秘密传播链**：
- Cursor 发行密钥被暴露 → 解锁历史 agent traces 只读桶 → traces 中含 0.01% 客户的 API 凭证 → 窃取凭证引发 SaaS 横向移动。
- LC 2092 语言：节点 F（Fireworks R&D 机器）在 t=Oct 23-24 被攻陷，通过边（API 调用、数据交换）把秘密传播到下游节点集合。Cursor 的「全部推理迁离 Fireworks」= **删除图中所有与 F 相连的边**——阻断传播。但已经传播出去的（ traces 里的凭证）= **已知的 dist[i] 集**——必须全部轮换。

**上下文不一致 = 多 agent 的头号杀手**（AI Agent Orchestration 研究，11/13）：

生产多 agent 系统失败的首要原因不是编排模式选错，而是 **context inconsistency**——agent A 修改了共享状态但 agent B 读到旧值。这正是 LC 2092 的**对偶问题**：
- LC 2092：秘密传播**太快**（你不希望 B 知道却知道了 = 信息泄露）
- 上下文不一致：状态同步**太慢/不一致**（你期望 B 知道却不知道 = 决策错误）

两个问题的共同解：**显式状态作用域规则**——每个共享变量写明 owner（谁可以写）、reader（谁可以读）、版本号（写操作带 version，读操作校验 version）。这就是 agent 通信的 ACL。

---

## 三、面试技巧：Week 28 开营 + Agent 安全实时案例

### 🔥 时事锚点一：微软 Agent Framework 供应链攻击（11/17-19）—— 框架本身就是攻击面

**事件还原**：

11/17，PyPI 上出现两个恶意版本的 Microsoft Agent Framework：`agent-framework 1.26.1` 和 `1.26.2`。这两个版本含有额外的凭证窃取代码，目标包括：
- Azure 认证 token
- Azure DevOps 凭证
- GitHub Actions secrets
- 敏感环境变量

最狠的是：**恶意代码在 Python 启动时自动执行，即使应用没有显式 import 该包**。两个版本存活不到一小时即被下架。微软安全公告原话：*「任何安装过 1.26.1 或 1.26.2 的系统应视为已失陷（treat as compromised），所有可达凭证必须轮换。」* Wiz 和 Python 安全公告补充：1.26.0 是安全版本。

**面试话术四连**：

1. **「你正在构建的 agent 框架本身就是攻击面」**：被问「agent 系统安全怎么考虑」，不要只答 prompt injection（那是应用层），要答**依赖层**——`pip install` 一个包 = 在你的运行时里执行陌生人的代码。微软自己的框架被 hijack = 没有厂商免疫。防御三件套：*①版本锁定（lockfile + SHA-256 hash 校验）②私有 PyPI 镜像（所有依赖经安全扫描的代理层）③CI 沙箱安装（隔离 runner + 最小权限 token + 禁止联网出站）*。

2. **「上线不到一小时」的数学含义**：恶意包存活 ~60 分钟。CI/CD 流水线如果用 `pip install agent-framework>=1.26.0` 这种松散约束，在这 60 分钟内**自动拉取并执行了恶意代码**——无数个 CI 实例在不知情的情况下被「感染」，这就是 LC 2092 的 t=0。面试话术：*「依赖解析的自动升级是双刃剑——你是第一时间拿到安全补丁，还是第一时间拿到 0-day？答案取决于你锁不锁版本」*。

3. **凭证传播 = 图中的秘密传播**：Azure token 在 CI 中被窃取后，攻击者可以用它访问什么？= 从被攻陷节点跑 Dijkstra = **爆炸半径评估**。被问「一个 CI runner 被攻陷的影响面」→ 不要拍脑袋列系统清单，要说：*「先列出该 runner 持有的所有凭证，再沿凭证可达的资源画传播图——影响面 = 从每个凭证出发可达的所有节点。这就是 LC 2092 的反向 Dijkstra」*。

4. **「视为已失陷」的工程含义**：微软说 treat as compromised → 不是「检查一下有没有异常再决定」，而是**全量轮换**（rotate everything）。因为排查期间攻击者可能已建立持久化（persistent foothold）。标准应急响应：*①立即锁定版本到 1.26.0 ②全量凭证轮换（不是排查后再轮换）③审计受影响时段的所有 API 调用日志 ④建立私有镜像 + hash 校验防重蹈覆辙*。

### 🔥 时事锚点二：Agent 协议栈补完 —— MCP + A2A + AG-UI = 「能通信 ≠ 该通信」

**事件**：11 月，GitHub Trending 上的 AG-UI 项目达 18,522 stars，补齐了 agent 协议栈最后一块。三层协议：

| 协议 | 层 | 功能 | 状态 |
|---|---|---|---|
| MCP | agent ↔ 工具 | 标准化工具调用 | 已标准化（Claude Code/Codex/OpenCode 等 20+ harness） |
| A2A | agent ↔ agent | 跨厂商 agent 协作 | Linux Foundation 维护 |
| AG-UI | agent ↔ 用户 | 事件驱动的人机交互协议 | 18,522 stars · 快速普及 |

媒体称「智能体时代的 TCP/IP 栈」。

**面试翻译**：

- **协议栈 ≠ 安全栈**：TCP/IP 从 1983 年标准化到 1994 年才有 HTTPS（TLS），中间隔了十余年明文传输。Agent 协议现在处于同样的「明文时代」——MCP 定义了**怎么**调工具，但没有内建**谁可以调**的认证；A2A 定义了**怎么**协作，但没有内建**谁可以协作**的授权。面试话术：*「MCP/A2A/AG-UI 解决了『能通信』的问题，『该通信吗』的问题还没有标准答案——这需要一个 agent 版的 TLS 时刻」*。

- **被问「多 agent 系统怎么设计通信架构」**：先给协议分层（MCP 管工具、A2A 管协作、AG-UI 管交互），再补安全层：*「每个协议层都要加信任边界——MCP 层做工具调用白名单（哪些工具可以被哪些 agent 调用）、A2A 层做 agent 身份认证（每个 agent 独立 NHI 身份，非共享凭证）、AG-UI 层做用户确认门（destructive 操作必须 human-in-the-loop）」*。McKinsey 2026 调查：*个体 agent 身份 + 最小权限作用域（而非跨 agent 舰队共享凭证）是任何生产多 agent 编排的最低安全基线*——这句话背下来，面试直接用。

- **CognitoForge Nexus 开源（11/20）**：新的多 agent 编排框架，引入图任务分解引擎 + 自适应 agent 间通信协议（实时协商、冲突解决、自愈）。信号：**多 agent 编排正在从「写 prompt 的 art」变成「设计 agent 生态系统的 engineering」**——新角色正在诞生：agentic system designer、multi-agent debugger。

### 🔥 时事锚点三：Gartner 40% 预警 + Grok 能力幻觉 —— 多 agent 的 ROI 与可靠性边界

**事件一**：Gartner 预测到 2027 年底，**40%+ 的 agentic AI 项目将被取消**，原因 = ROI 不清晰 + 风险控制薄弱。McKinsey 2026 AI Trust 调查：近 2/3 受访者将安全和风险列为扩展 agentic AI 的**头号障碍**。

**事件二**：Grok 4.20 的多 agent 辩论架构（4 agent 默认、Heavy 模式 16 agent）将幻觉率从 ~12% 降到 ~4.2%。但早期 beta 出现 **capability hallucination**——模型声称自己没有的能力，或否认已有的能力。

**面试话术**：

- **「治理必须与编排并行建设」**：Gartner 的解药原话。被问「怎么说服老板投资 agent 安全」→ 用 Gartner 数字：*「40% 的项目死不是因为技术不行，是因为 ROI 算不清 + 风险管不住。我的方案是把治理做成编排系统的并行层——审计日志、权限控制、合规检查是系统的一部分，不是上线前的 review checklist」*。

- **多 agent ≠ 更可靠**：Grok 数据在事实核查上成立（12%→4.2%），但 capability hallucination 说明**辩论能纠错，也能集体陷入同样的盲区——如果 4 个 agent 用同一个底层模型，它们的偏见是相关的**（correlated bias）。面试被问「多 agent 会不会比单 agent 更可靠」→ 答：*「事实性错误会降（Grok 数据），但系统性的自信错误不一定——特别是当所有 agent 共享同一个底层模型时。工程解：异构模型组合（不同家族模型交叉验证），成本翻 3-5 倍但偏见去相关」*。

- **Black Friday 倒计时 2 天**：感恩节（11/26 周四）美股休市，黑五（11/27）= agent 系统年度大考。零售 agent 流量暴增 + 攻击面同时扩大（一年中最有利可图的攻击窗口）。面试被问「高流量期间的安全策略」→ *「先限流保可用性，再收紧 agent 权限范围（最小权限：高流量期间临时降权），最后确保审计日志完整——黑五期间最危险的不是系统变慢，是 agent 在高压下做出越权操作而你事后无法追溯」*。

### 模拟面试速答五连

**Q1：设计一个多 agent 系统的安全架构，30 分钟讲。**

按四澄清开场：①agent 拓扑——supervisor-worker（层级）还是 peer-to-peer（网状）？层级有信任根（supervisor = CA），网状需要分布式信任（每对 agent 独立协商密钥）。②通信协议——MCP/A2A/自建 gRPC？决定认证和加密在哪一层做。③数据流——哪些 agent 接触用户 PII？接触 PII 的节点划入「高密区」，进出高密区的边要加审计。④SLA——安全审计的延迟要求（实时拦截 vs 事后追溯）？决定安全层是 inline proxy 还是 sidecar。

然后三层架构：
- **身份层**：每个 agent 独立 Non-Human Identity（NHI），最小权限凭证，**不共享 token**——McKinsey 2026 调查的最低安全基线原话。凭证生命周期管理（自动轮换、短期凭证优先于长期 API key）。
- **通信层**：MCP 工具调用白名单（哪个 agent 可调哪个工具）+ A2A 消息签名（防篡改）+ 传输加密（TLS/mTLS）+ 敏感字段在消息中默认脱敏（Fireworks 教训：traces 里的凭证应该默认打码——不是发现泄露后才打码）。
- **审计层**：每次通信记录五元组（源 agent ID、目标、时间、操作类型、数据分类标签）入 append-only 日志（Week 26 哈希链 + Merkle root），定期跑 LC 2092 式传播分析——*「如果 agent X 被攻陷，沿通信图它的 secret 能传播到哪些节点？这个集合 = 需要轮换凭证的清单 + 需要加 ACL 的边集」*。

收尾：*「安全不是上线前的 checklist，是运行时的一部分——就像 Black Friday 的限流器，安全和可用性是同一个系统的两个属性。」*

**Q2：一个 agent 的 API key 泄露了，你怎么评估影响面？**

用 LC 2092 框架五步：
1. **定点**：泄露 key 所在的 agent = Dijkstra 源点。
2. **画通信图**：从审计日志中提取该 agent 所有的 A2A/MCP 调用记录（源、目标、时间、数据类型），构建带时间戳的边。
3. **跑传播**：从源点跑 Dijkstra——「该 agent 在泄露后，和哪些节点有过通信？」`dist[i]` 越小 = 越早接触 = 风险越高。
4. **分级响应**：按 `dist[i]` 分层——0-1h 内通信的 agent 立即隔离 + 凭证轮换，1-24h 的优先轮换，>24h 的排队审计。
5. **阻断**：在关键边上加 ACL 或临时下线，切断进一步传播。

面试加分：*「这就是 Gartner 说的治理与编排并行——没有审计日志你连通信图都画不出来，影响面评估就是拍脑袋。所以审计层要在第一天就建好，不是泄露后补」*。

**Q3：多 agent 系统为什么比单 agent 更难调试？**

三层递进：
1. **状态空间爆炸**：n 个 agent 每个 m 种状态 → 全局状态 m^n——但这不是最痛的，组合问题可以靠分层降维。
2. **非确定性**：agent 间消息顺序不保证（网络延迟、重试、超时重发），同一输入可能产生不同执行轨迹——**你复现不了 bug**。解法：确定性重放（记录所有消息 + 种子 + 版本，按时间戳重放——LC 2092 的时间戳排序思想在调试中的直接应用）。
3. **上下文不一致（最痛）**：AI Agent Orchestration 研究（11/13）确认 context inconsistency 是头号生产杀手——agent A 更新了共享状态，agent B 读到旧值，做出基于过期信息的决策。解法 = **显式状态作用域规则**：每个共享变量写明 owner / reader / version——写操作必须递增版本号，读操作必须校验版本（stale read 拒绝或刷新）。

收尾：*「单 agent 的 bug 是逻辑错误；多 agent 的 bug 是分布式系统错误 + 逻辑错误 + LLM 不确定性的三重奏——调试工具链还在进化，所以现在最好的防御是**可观测性先行**：全链路 trace + 状态版本化 + 确定性重放」*。

**Q4：微软 Agent Framework 被投毒，你的项目用了这个框架，你现在怎么办？**

按应急响应 SOP 五步：
1. **隔离**：立即锁定版本到 1.26.0（确认安全版本），禁止自动升级。所有 CI/CD pipeline 冻结依赖解析。
2. **排查**：查 CI/CD 日志，确认哪些实例在 11/17 恶意版本存活期间执行过 `pip install`。时间窗口 = 恶意包存活时长（<1 小时），用精确时间戳过滤。
3. **轮换**：确认安装过 → **所有可达凭证全量轮换**（Azure token / GitHub secrets / 环境变量 / 任何 CI runner 能触及的凭证）——不是看到异常才轮换，是「视为已失陷」。这就是 LC 2092 的 `dist[i] != -1` 集合——全部处置。
4. **审计**：检查受影响时段的所有 API 调用日志（Azure Activity Log / GitHub Audit Log），看有没有异常访问模式（非工作时间、异常 IP、异常资源类型）。
5. **加固**：建立私有 PyPI 镜像 + 安装时 SHA-256 hash 校验 + CI runner 最小权限 + 网络出站白名单。

面试加分：*「这个流程应该提前写成 runbook 并演练过（game day）——Black Friday 期间出这种事，你不能现场 Google 'what to do when your AI framework is compromised'」*。

**Q5：Gartner 说 40% 的 agentic AI 项目会被取消，怎么让你的项目不进这 40%？**

三个可执行答案：
1. **从第一天就算 ROI**：每个 agent 的立项就要回答「它替代了什么人力/时间成本？收益量化是多少？」——不是上线后补，是立项时算。评估方法论直接用 Week 26 全套：基线测量（Day 164）→ 指标定义（Day 165）→ MDE 前置（Day 167）。
2. **治理并行**：把安全审计、权限控制、合规检查做成编排系统的一部分——Gartner 原话「治理架构与编排系统并行建设，不是事后补票」。不是上线前 security review 盖章，是运行时持续审计（Week 26 Merkle 链 + 今天 LC 2092 传播分析）。
3. **快速证伪**：设一个「止损点」——如果 N 周内效果达不到预声明的 MDE（最小可检测效应），主动砍。*「Gartner 说 40% 会被取消，但没说取消是坏事——及时砍掉 ROI 不清晰的项目，比硬撑到它自然死亡要专业得多。评估平台的全部意义就是让你敢砍：数据说了不算的，勇气说了更不算」*。

### Week 28 开营导语

> Week 23 我们练了 Multi-Agent 工程实战（协调协议、黑板、预算结算、群体容错、评测红队规模化）。Week 28 时隔五周回归——但视角变了：不再只关注「怎么让多个 agent 协作」，而是**「怎么让它们安全地协作」**。微软 Agent Framework 被投毒、Fireworks AI 泄露扩大、Gartner 40% 预警——所有信号都指向同一件事：**agent 系统的安全问题已从「理论风险」变成「生产事故」**。本周主线：*多 agent 通信图的信任边界*——谁在什么时间知道什么秘密（今天）→ agent 间协议与安全通信（周四）→ 群体容错与拜占庭行为（周五）→ 本周收官综合实战。

### 金句

> **微软用一次 PyPI 投毒告诉全世界：你用来防御的框架，就是攻击者的入口。Agent 协议栈（MCP + A2A + AG-UI）补完了「能通信」的问题，但「该通信吗」的安全层还没有标准答案——TCP/IP 用了二十年才长出 TLS，我们没有二十年。Gartner 说 40% 的 agentic AI 项目会死于 ROI 不清和风控薄弱——翻译过来：光有协议没有治理的系统，注定进那 40%。Black Friday 倒计时 2 天，当你在优化 agent 的推理延迟时，别忘了另一个指标：如果此刻一个 agent 被攻陷，它的 secret 会沿通信图传播到多少个节点？这个数，就是你的攻击面。**

### Day 174 自检清单（10 项）

1. LC 2092 Dijkstra 变形松弛条件默写：`mt >= dist[u] && (dist[v] == -1 || mt < dist[v]) → dist[v] = mt`
2. 为什么普通 BFS 不行（BFS 恒定边权假设 vs 本题非对称时间传播约束）
3. Dijkstra 过期条目的判断与跳过（`if t > dist[u] { continue }`）
4. 初始化三行的顺序（dist[0]、dist[firstPerson]、两个 Push——顺序不能反）
5. Union-Find 解法思路（按时间分组 + union + 连通性检查 + 不连通回滚）
6. 微软 Agent Framework 供应链攻击防御三件套（版本锁定 + hash 校验 / 私有 PyPI 镜像 / CI 沙箱最小权限）
7. MCP + A2A + AG-UI 三层协议各管什么 + 「协议栈 ≠ 安全栈」面试话术
8. 凭证泄露影响面评估五步法（定点 → 画图 → 传播 → 分级 → 阻断）
9. context inconsistency 为什么是多 agent 生产失败首因 + 显式状态作用域规则解法
10. Gartner 40% 预警三个对策（ROI 前置 / 治理并行 / 快速证伪）

---

### 明日预告

**Day 175（Week 28 Day 2 · 周四 2026-11-26）**：感恩节——美股休市。建议方向「Agent 间消息协议与安全通信」或「拜占庭容错与群体决策」。时事前瞻：**感恩节休市 = 交易量枯竭但 agent 系统不休——黑五零点钟声敲响时你的零售 agent 准备好了吗？**·ANT 期权首周期权（12/18 合约）节前持仓变化·黄仁勋 12/3 参议院听证倒计时 7 天·黑五 = agent 安全年度大考（最高流量 + 最大攻击面同时到来）。
