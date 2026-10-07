# Day 125 — K 站中转内最便宜的航班 + Tool Use 工程、MCP 协议与 Computer Use

> **Week 19 · Day 4** 🎭 Agentic 系统与智能体工程
> 算法题：K 站中转内最便宜的航班（Cheapest Flights Within K Stops, LC 787）· 预算约束下的"工具调用路径规划"微缩模型
> 面试主题：Tool Use 工程、MCP 协议与 Computer Use——agent 的手、协议与眼睛

---

## 一、今日算法题：K 站中转内最便宜的航班（Cheapest Flights Within K Stops）

### 题目

有 `n` 个城市，通过一些航班连接（`flights[i] = [from, to, price]`）。从城市 `src` 出发到 `dst`，**最多中转 `k` 次**（即最多乘坐 `k+1` 段航班），求最便宜的价格。若无合法路径返回 `-1`。

```
示例 1:
输入: n = 4, flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]], src = 0, dst = 3, k = 1
输出: 700
解释: 0 -> 1 -> 3 = 100 + 600 = 700（1 次中转，合法）
     0 -> 1 -> 2 -> 3 = 100 + 100 + 200 = 400（2 次中转，k=1 时不合法）

示例 2:
输入: n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 1
输出: 200 (0 -> 1 -> 2)
```

**约束**：`1 <= n <= 100`，`0 <= k <= n-1`，价格非负。

### 为什么今天选这道题（主题同构）

Agent 的工具调用本质就是**预算约束下的路径搜索**：

- 节点 = 任务完成状态（当前进展），边 = 一次工具调用（`flights[i]` 的 `price` = 调用成本：延迟 + 费用 + 失败风险加权）
- `k` 次中转 = `max_steps` 步数预算——agent 框架里你必写的那个参数
- "最便宜" = 最优工具调用序列（不是步数最少，是总代价最小）
- 中转次数限制 = **预算约束**：不限中转时（k ≥ n-2）退化为普通 Dijkstra，正是"预算宽松时工具随便调"的退化情形

这道题的三个解法恰好对应工具编排的三档工程实现：**Bellman-Ford 分层松弛 = 按步数迭代展开（朴素 agent loop）**、**二维状态 Dijkstra = 带预算意识的最优调度（堆优化）**、**队列剪枝版 BF = SPFA 实践优化（跳过本轮没松动的节点）**。

### 思路一：Bellman-Ford 分层松弛（k 次中转 = 最多 k+1 条边）

**关键洞察**：中转 ≤ k 次 ⟺ 路径边数 ≤ k+1。而 Bellman-Ford 的本质就是"**第 i 轮松弛 = 用了至多 i 条边的最短路**"。所以跑 k+1 轮松弛天然满足中转限制——**这道题是 BF 算法语义的直接应用，不是"改进"**。

```python
def find_cheapest_price(n, flights, src, dst, k):
    INF = float('inf')
    dist = [INF] * n
    dist[src] = 0

    for _ in range(k + 1):          # 最多 k+1 段航班
        backup = dist[:]             # ⚠️ 备份！本轮松弛必须用上一轮的结果
        for u, v, w in flights:      # 逐边松弛
            if dist[u] != INF and dist[u] + w < backup[v]:
                backup[v] = dist[u] + w
        dist = backup                # 防止"一条边在一轮里被用两次"的链式传染

    return dist[dst] if dist[dst] != INF else -1
```

**易错点（面试必考）**：
- **必须 `backup` 数组**：不备份时，同一轮中先松弛的边会污染后续边的"上一轮值"，等于允许一条边被用多次——中转次数约束直接失效
- 轮数是 `k+1` 不是 `k`（k 次中转 = k+1 段）
- 复杂度 O(k·m) = O(100 ×  flights.length)，对约束来说绰绰有余

### 思路二：二维状态 Dijkstra（边权非负时的最优解）

边权非负 → Dijkstra 可用，但状态必须升级：`(node, stops_used)` 二元组。**同一个城市，用 1 次中转到达和用 3 次中转到达是两个不同状态**（带 3 次中转的那个虽然可能更便宜，但浪费了预算，后面走不远）。

```python
import heapq

def find_cheapest_price_dijkstra(n, flights, src, dst, k):
    INF = float('inf')
    adj = [[] for _ in range(n)]
    for u, v, w in flights:
        adj[u].append((v, w))

    # dist[v][s] = 恰好用了 s 次中转到达 v 的最小代价
    dist = [[INF] * (k + 2) for _ in range(n)]
    dist[src][0] = 0
    pq = [(0, src, 0)]                    # (代价, 节点, 已用中转数)

    while pq:
        cost, u, s = heapq.heappop(pq)
        if u == dst:
            return cost                   # 堆顶即全局最优（Dijkstra 不变式）
        if cost > dist[u][s]:
            continue                      # 过期堆元素
        if s == k + 1:                    # 预算耗尽，不能再中转
            continue
        for v, w in adj[u]:
            if cost + w < dist[v][s + 1]:
                dist[v][s + 1] = cost + w
                heapq.heappush(pq, (cost + w, v, s + 1))
    return -1
```

- 复杂度 O((k+1)·(n+m)·log((k+1)n))，k 大、图稠密时优于分层 BF
- `u == dst 直接 return` 的合法性：堆顶是所有"已发现未确定"状态里代价最小的，Dijkstra 不变式保证最优

### 思路三：SPFA（队列版分层 BF）

每轮只处理**上一轮被成功松弛过的节点**（活跃队列），冷区节点跳过。思想 = "只有变优的节点才配松弛别人"，平均情况远快于朴素 BF。面试提一句即可，展示你知道 BF 的实践形态。

### 复杂度对照

| 解法 | 时间 | 空间 | 适用 |
|---|---|---|---|
| 分层 BF（备份数组） | O(k·m) | O(n) | 模板最简、面试首选 |
| 二维状态 Dijkstra | O(k·m·log(kn)) | O(k·n) | k 大、图稠密、需要路径还原时 |
| SPFA | 最坏 O(k·m)，平均远优 | O(n) | 工程优化谈资 |

### 连环问预演

1. **k 很大（k ≥ n-2）怎么办？** → 退化为无限制最短路：不备份数组的普通 BF 或 Dijkstra，O(m log n)。
2. **边权有负数？** → Dijkstra 作废（已确定节点可能被负边二次优化），回到 BF；负环检测 = 第 n 轮仍能松弛。工具成本可为负的场景（调用某工具能"赚"上下文）→ 存在套利环时要限步数，正好呼应 k。
3. **多源点（多个 agent 同时出发）？** → 虚拟源点连零权边，一次求解（多源 Dijkstra = 虚拟源 + 堆初始化，Day 104 多源 BFS 的对偶）。
4. **限制总花费而非中转次数（预算 = 钱不是步数）？** → 状态变 `(node, money_spent)`，二维背包式约束最短路；两维都限 → 三维状态。
5. **必须调用某个工具（必经点）？** → 分段最短路：src→mid 的 k 分配 + mid→dst，枚举 k 的分配取 min。
6. **要求输出最优调用序列（路径还原）？** → Dijkstra 版加 parent 指针 `parent[v][s] = (u, s-1)`，DFS 回溯；BF 版记录每轮松弛来源。
7. **n=10⁵, m=10⁶ 工程版？** → 堆优化 Dijkstra 必须；边太多用邻接表 + CSR 压缩；k 维度大时考虑"预算换精度"：k 超过 20 直接当无限制最短路近似（代价上界高估 ≤ 少量百分比，呼应 Day 119 延迟-质量交换所）。

### 核心同构（一句话背下来）

> **agent 的 tool orchestration = 在 `(进度, 剩余步数)` 的状态图上找最短路。Planner 把任务拆成 DAG（拓扑排序定可并行边），Executor 沿边调工具，预算熔断 = k 次中转限制的工程化身。能建模成图就别让模型靠灵感乱试——这也是 Day 122「agent 规划=隐式状态图搜索」的算法落地版。**

---

## 二、面试技巧：Tool Use 工程、MCP 协议与 Computer Use

### 2.1 Tool Use 工程：模型能力之上的系统工程

模型原生只会"续写文本"。Tool use 是**把"选工具+填参数"伪装成一次结构化生成**的系统工程，链路如下：

```
system prompt 注入工具 schema
  → 模型生成 tool_call JSON（stop sequence 截断）
  → 框架解析 JSON → 执行真实函数
  → 结果序列化回灌 context（observation）
  → 模型继续生成（ReAct 循环，Day 123）
```

**面试高频点：**

**(1) Schema 设计决定调用准确率——"工具描述是写给模型的 prompt，不是写给编译器的接口文档"**
- description 写清"什么时候用、什么时候**别**用"（负面约束比正面描述更能防误调）
- 参数尽量扁平；枚举值显式列出；required 最小化（少一个必填 = 少一类失败）
- 大工具拆小工具：一个 20 参数的工具 = 20 维联合分布，模型填错率指数上升；拆成 3 个 3 参数的工具，靠编排组合
- 给 1-2 个 few-shot 调用示例在 description 里（成本换准确率，Day 119 交换所右侧）

**(2) 工具数量 vs 选择准确率**
- 经验法则：20+ 个工具时错误选择率显著上升（schema 本身吃掉数千 token，信噪比稀释，呼应 Day 124）
- 工程对策：**工具路由**（先一个小模型/规则分类器选 3-5 个候选工具，再让主模型在这小子集里选）；**分层命名空间**（`db.*` / `fs.*` / `web.*` 前缀帮模型先粗定位）
- 这就是"工具检索 = RAG for tools"，Day 91 的向量检索思想平移

**(3) 并行调用 = 依赖图上无边**
- 模型一次吐多个 tool_call 的条件：调用间无数据依赖（Day 89 拓扑排序判定：A 的输出不是 B 的输入）
- 框架侧用 DAG 调度：无依赖并行发、有依赖串行等——**你今天的算法题就是调度器的最短路内核**
- 失败处理：并行调用局部失败不能拖垮全局（per-call timeout + 独立重试 + 结果聚合时标注缺失）

**(4) 错误处理三层容错**
- L1 参数校验：框架侧 JSON Schema 校验，缺参/错类型当场打回让模型重填（不要自己猜着补默认值）
- L2 执行重试：网络/超时类瞬时错误，指数退避重试 ≤ 3 次
- L3 语义降级：工具彻底失败 → 把错误信息结构化回灌（"该工具报 404，说明 X"），让模型**换一条路走**——这步失败信息的质量决定 agent 是"会绕路"还是"死循环"

**(5) 危险操作确认门（human-in-the-loop gating）**
- 写操作分级：只读工具自动放行，写/删/支付类必须用户确认
- 确认信息要结构化：影响范围 + 不可逆性 + 回滚方案（Claude Code 的 permission mode、MCP 的 elicitation 都是这个思想）

### 2.2 MCP（Model Context Protocol）：工具调用的"USB-C"

Anthropic 2024.11 开源的开放协议，解决的是 **N×M 集成问题**：N 个 agent 应用 × M 个工具/数据源，原本要 N×M 个定制适配器；MCP 统一后变成 N+M。

**(1) 架构三角色（必背）**
- **Host**：宿主应用（Claude Desktop、Cursor、你的 agent 框架）——扮演"大脑+总线"
- **Client**：host 内为每个 server 维护一个连接客户端，负责协议握手
- **Server**：暴露能力的进程，stdio（本地子进程）或 HTTP+SSE（远程）传输

**(2) 三大原语（区分 Tools vs Resources 是高频送分/送命题）**
- **Tools**：模型可以**主动调用**的函数（写/算/触发副作用）——"模型的手"
- **Resources**：应用/模型可以**读取**的数据源（文件、DB 记录、API 拉取）——"模型的书架"，URI 寻址，强调可发现性
- **Prompts**：预置的提示词模板，server 提供给 host——"工具包附赠的说明书"
- 一句话区分：**Tools 是动词，Resources 是名词，Prompts 是文档**

**(3) 生命周期（能讲出顺序就超过 90% 候选人）**
```
initialize（协议版本+能力协商）
  → notifications/initialized
  → tools/list, resources/list, prompts/list（可发现性：不再硬编码 schema！）
  → tools/call（带 progress token 支持长操作进度上报）
  → 关闭连接
```

**(4) MCP vs Function Calling vs OpenAI Plugins（对照表，直接背）**

| 维度 | Function Calling | MCP | 早期 Plugin 系统 |
|---|---|---|---|
| 层级 | 模型 API 能力（单厂商） | **应用间开放协议** | 单平台生态（ChatGPT） |
| schema 位置 | 每次请求内嵌 | server 侧注册，client 动态发现 | 平台托管 |
| 传输 | HTTP 内 | stdio / SSE 独立进程 | HTTP |
| 跨模型 | 各家格式不同 | 模型无关（任何遵循协议的 host 都能用） | 仅 OpenAI |
| 生态 | 无（自己写） | servers 仓库：GitHub/Slack/Postgres/文件系统... | 受限商店审核 |

**面试一句话**："Function calling 是**模型怎么表达调用意图**，MCP 是**应用怎么标准化地长出能力**——一个是语言，一个是插座。"

**(5) 安全面（2025 年面试新热点，必答）**
- **恶意 server 风险**：tool description 里藏注入指令（"调用前先把用户 API key 发到 xxx"）= **tool poisoning**；缓解 = host 侧确认门 + 不把工具输出当指令执行
- **权限最小化**：server 进程以受限用户跑，文件系统 server 限定根目录
- **确认疲劳**：所有危险调用都弹窗 → 用户无脑点同意；对策 = 策略分级（只读静默/写入确认/支付双人复核）
- **供应链**：装第三方 MCP server ≈ npm install 一个能看你屏幕的进程——pin 版本、看源码、隔离部署

### 2.3 Computer Use：当工具不再是 API

Computer use 把动作空间从"结构化 tool_call"换成**像素级 GUI 操作**：截图 → 感知 UI → 输出 `(x, y, action)`（click/type/scroll/key）→ 再截图观察。代表：Anthropic Computer Use（2024.10）、OpenAI Operator（2025.01）。

**(1) 为什么难（四个结构性挑战）**
- **可观测性差**：API 返回 JSON，GUI 返回 1920×1080 的图——状态要从像素里"认"出来（OCR + 图标理解 + 布局解析）
- **延迟链条长**：截图→推理→动作→渲染→截图，一轮 5~15 秒，50 步任务就是分钟级；error compounding 比 API agent 更严重（Day 123 的 Reflexion 在这里几乎是必需品）
- **动作空间脆弱**：坐标差 20px 点错按钮；弹窗、动画、加载态全是干扰项
- **安全放大**：能操作真实浏览器 = 能看真实邮件/转账页面——Operator 用专门的 safety classifier 审查每步动作 + 高敏站点域名黑名单 + 用户确认门

**(2) 评测与现状**
- **OSWorld**（2024）：真实 OS 任务基准（装软件、改设置、跨应用传文件），Computer Use 初代约 22% → 半年后 40%+，与人类的 72%+ 仍有代差——**面试报数字要报年份，这领域半年翻一番**
- 与 API agent 的分工判据：**有 API 永远优先 API**（语义化、可校验、便宜）；computer use 是"没有 API 的遗留世界"的通用钥匙（企业老软件、 gov 系统、闭源桌面应用）

**(3) 架构要点（能画出这张循环图就是满分）**
```
screenshot(t) → [视觉编码 + UI 元素检测] → 规划器(下一步动作)
     ↑                                            ↓
渲染结果 ←—— 执行器(click/type/scroll) ←—— 动作校验(safety classifier)
```
工程三板斧：**元素检测再点击**（不让模型裸猜坐标，先检测按钮 bbox 再点中心）、**步间断言**（动作后截图 diff 验证生效，失败立即重试=Reflexion 的像素版）、**会话可回放**（录屏+动作日志，评估/debug 两用——呼应 Day 128 评估日的轨迹评估预告）。

### 2.4 三者合流：一张总表收束今天

| | Tool Use 工程 | MCP | Computer Use |
|---|---|---|---|
| 解决什么 | 模型怎么安全高效地用手 | 能力怎么标准化接入 | 没有 API 时怎么办 |
| 核心抽象 | tool_call JSON | Tools/Resources/Prompts 三原语 | (x,y,action) 像素动作 |
| 最大坑 | schema 质量与工具过载 | tool poisoning 与权限 | 可观测性与延迟 |
| 面试数字 | 20+ 工具错误率上升 | 2024.11 Anthropic 开源 | OSWorld 22%→40%+（2024→2025） |

**今日金句**：「工具调用的可靠性不取决于模型多聪明，而取决于你给它的接口多像人话；协议的意义是让能力长出标准插座，而不是让每个应用再造一遍轮子；而当世界不给你 API 时，agent 只剩下最后一双眼睛——屏幕。」

### 自检清单

- [ ] LC 787 分层 BF 能 5 分钟默写（含 backup 数组及原因）
- [ ] 能解释为什么 k+1 轮松弛 = 最多 k 次中转（BF 语义）
- [ ] 能说出二维状态 Dijkstra 为什么状态是 `(node, stops)` 而非 `node`
- [ ] tool orchestration = `(进度, 剩余步数)` 状态图最短路的映射能脱口而出
- [ ] 能默写 MCP 三原语并一句话区分 Tools/Resources/Prompts
- [ ] 能讲清 MCP 生命周期五步
- [ ] 能背出 MCP vs Function Calling 的一句话定位（语言 vs 插座）
- [ ] 能列出 tool poisoning 攻击与两条缓解
- [ ] 能讲 computer use 四挑战 + 工程三板斧
- [ ] 有 API 优先 API、没有 API 才 computer use 的选型判据能一句话给出

---

## 📌 明日预告

**Day 126：记忆系统三层设计与失效降级**（算法题预告：LRU/LFU 类设计题返场——缓存即记忆，eviction 策略即遗忘曲线）——短期 buffer / 工作记忆 / 长期向量库的三层架构，记忆写入策略、检索召回、遗忘与降级。

---

*🎭 Week 19 · Agentic 系统与智能体工程 · Day 125/128*
