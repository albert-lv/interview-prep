# Day 122 — 打开转盘锁 + Week 19 开启：Agentic 系统与智能体工程 🎭

> 📅 2026-10-04 · Week 19 Day 1 · 连续更新第 122 天
>
> 主题：Agentic 系统与智能体工程 🎭 · 本周地图总览日

---

## 1) 今日算法题

### 打开转盘锁（Open the Lock）

**题意**：一个转盘密码锁有 4 个拨轮，每个拨轮可以是 `0~9` 中的一个数字。你可以把任意一个拨轮**向上或向下拨一位**（`9→0` 或 `0→9` 是合法的循环拨动）。锁的初始状态是 `"0000"`，另有一个代表解锁密码的字符串 `target`。同时给定一组 `deadends`，表示**禁止停留**的状态——一旦拨到这些状态，锁会永久锁死，后续无法继续操作（拨过去也算触发）。

返回从 `"0000"` 拨到 `target` 的**最少拨动次数**；如果不可能解锁，返回 `-1`。

```
输入: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
输出: 6
解释: "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202"

输入: deadends = ["8888"], target = "0009"
输出: 1
解释: 直接把最后一位拨到 9。

输入: deadends = ["0000"], target = "8888"
输出: -1
解释: 起点就是死亡状态，开局即锁死。
```

**关键约束**：状态空间 = 10⁴ = 10000 个节点；每个状态 8 条出边（4 个位置 × 2 个方向）。数据规模小到可以整个装进内存——这是**无向图上的单源最短路径**问题。

### 思路：把「拨锁」翻译成「状态空间 BFS」

这道题有 99% 的面试者第一眼会想着去模拟拨锁过程。把它拆开翻译：**每个锁的状态是一个节点，每次拨动是一条边（权值为 1），deadends 是被删除的节点**。求最少拨动次数 = 无权图单源最短路径 = **BFS 分层展开**，模板直接套：

1. **建模三件套**：状态编码成 4 字符字符串（或一个 0~9999 的整数——`state = d1*1000 + d2*100 + d3*10 + d4`，整数编码比字符串 hash 快且省内存）；邻接不显式建图，**现场生成 8 个邻居**（拨第 i 位：`+1` 模 10、`-1` 加 10 再模 10）；deadends 进 `set` 判重，`visited` 与 deadends 合并处理（禁停 = 禁入队）。
2. **起点特判**：`"0000"` 本身在 deadends 里直接返回 `-1`（题面坑点，很多 AC 挂在这里）。
3. **BFS 标准动作**：初始状态入队、步数 `steps = 0`；每轮处理当前层的全部节点（`for _ in range(len(queue))`），未访问邻居入队；出队即命中 target 时返回 `steps`。
4. **为什么不用 DFS/Dijkstra**：所有边权都是 1，BFS 第一次到达即最优（Dijkstra 的特化）；DFS 不保证最短，DFS + 记忆化求的是「能否到达」而不是最少步数——这是图论面试里被问烂、但还是要答完整的送分追问。

> 💡 **和 Week 19 的同构（今天的主角）**：这道题就是 agent 世界的微缩模型——**状态 = context，拨动 = action，deadends = 环境的惩罚性反馈，target = 任务目标**。Agent 的「规划」本质就是在这个巨大的状态图上搜索一条到目标的路径，而 Week 17 学的 RL 是「不知道图长什么样时的搜索策略」，Week 18 学的 test-time compute 是「步与步之间的思考预算」。今天起一整周，我们把智能体系统从骨架讲到牙齿。

### 代码

**Python（面试推荐写法）：**

```python
from collections import deque

class Solution:
    def openLock(self, deadends: list[str], target: str) -> int:
        dead = set(deadends)
        start = "0000"
        if start in dead:
            return -1
        if start == target:
            return 0

        visited = set(dead)          # 禁停 = 永不访问，合并进 visited
        queue = deque([start])
        visited.add(start)
        steps = 0

        while queue:
            # 一次处理一整层 → 队列里的节点到起点的距离都是 steps
            for _ in range(len(queue)):
                cur = queue.popleft()
                if cur == target:
                    return steps
                # 现场生成 8 个邻居，而不是预建图
                for i in range(4):
                    d = int(cur[i])
                    for nd in ((d + 1) % 10, (d - 1) % 10):
                        nxt = cur[:i] + str(nd) + cur[i+1:]
                        if nxt not in visited:
                            visited.add(nxt)
                            queue.append(nxt)
            steps += 1
        return -1
```

**Go（工程向，位运算生成邻居）：**

```go
func openLock(deadends []string, target string) int {
    dead := make(map[string]bool, len(deadends))
    for _, d := range deadends {
        dead[d] = true
    }
    if dead["0000"] {
        return -1
    }
    visited := map[string]bool{"0000": true}
    for d := range dead {
        visited[d] = true
    }
    queue := []string{"0000"}
    for steps := 0; len(queue) > 0; steps++ {
        for size := len(queue); size > 0; size-- {
            cur := queue[0]
            queue = queue[1:]
            if cur == target {
                return steps
            }
            b := []byte(cur)
            for i := 0; i < 4; i++ {
                orig := b[i]
                for _, nd := range [2]byte{(orig-'0'+1)%10 + '0', (orig-'0'+9)%10 + '0'} {
                    b[i] = nd
                    s := string(b)
                    if !visited[s] {
                        visited[s] = true
                        queue = append(queue, s)
                    }
                }
                b[i] = orig
            }
        }
    }
    return -1
}
```

### 复杂度

- **时间**：状态数上界 10⁴，每状态生成 8 邻居、set 判重 O(1) → **O(10⁴ × 8 × L)**（L=4 为字符串长度，约等于 O(1)，整体可视为 O(状态数 × 分支因子)）。BFS 每个节点最多入队一次。
- **空间**：`visited` 最坏装下全部 10⁴ 状态 → **O(10⁴)**。

> 📌 面试表达：「状态空间只有 10⁴，BFS 稳过；但状态数一旦爆炸到 2ⁿ（比如 n 个开关的锁），就要换双向 BFS 或者 A*——这就是我接下来要说的 follow-up。」

### 面试官连环问（这道题的高产追问区）

1. **「如果锁有 n 位，状态空间 10ⁿ 怎么办？」** → 双向 BFS：从起点和 target **同时**做 BFS，两层一交替，相遇即停。复杂度从 O(b^d) 降到 O(b^(d/2))——**指数减半是数量级的胜利**。关键点：frontier 用 `set` 而不是队列，每轮用**较小的一侧**扩展（谁小扩谁），deadends 对两侧都生效。
2. **「如果给每个状态一个『到 target 的启发式距离』（比如海明距离），怎么用上？」** → A*：优先级 = 已走步数 + 启发式。h = 不同位的个数（每拨一位最多修正一个位置，满足可采纳性 admissible——永不高估），保证最优性的同时剪枝大量分支。
3. **「deadends 改成『拨过就算锁死』（路径上经过也死）呢？」** → 状态必须携带「是否已触碰过死区」信息，状态空间翻倍（state, poisoned）二维化；或者在生成邻居时判断——但注意**题面是停留才死**，经过不死，这也是常见 WA 点。
4. **「如果允许一次同时拨两位（甚至可以不相邻）？」** → 分支因子从 8 涨到 4×2 + C(4,2)×4 更多，但 BFS 框架纹丝不动——**建模对了，扩展规则随便换**。这是考察「搜索框架与状态定义解耦」意识的好题。
5. **「10 位转盘、deadends 有 10⁶ 个，内存不够怎么办？」** → 外存 BFS（分层落盘 + 归并去重）、布隆过滤器近似判重、或 IDA*（迭代加深 A*，只需要 O(d) 内存，用启发式砍掉深层分支）。
6. **「并行化怎么做？」** → 单层内节点互相独立，frontier 切分多线程/多进程扩展，visited 用分片 hash set 或中心式去重——和 Day 104 Gossip 的 membership、Day 115 数独第一层 9 路并行同一个套路：**搜索的第一层永远天然并行**。

---

## 2) 面试技巧

### Week 19 开启：Agentic 系统与智能体工程 🎭

Week 17 我们学会了**训练**模型（RL 全流程），Week 18 我们学会了让模型**更会想**（test-time compute）。Week 19 回答下一个问题：**模型会想了之后，怎么让它真正干活？** 这就是 Agentic 系统——2024 年以来工业界投入最凶、岗位 JD 里出现频率最高的方向（SWE Agent / Agent Engineer / AI Infra 岗几乎必问）。

#### 一张图：Agent = 感知 → 规划 → 行动 → 观察的闭环

```
 ┌────────────────────────────────────────────┐
 │                Agent Loop                  │
 │                                            │
 │   ┌──────┐   ┌──────┐   ┌──────┐          │
 │   │ 感知 │ → │ 规划 │ → │ 行动 │ ─┐        │
 │   │Observe│  │Plan  │  │Act   │  │        │
 │   └──────┘   └──────┘   └──────┘  │        │
 │       ↑                          │        │
 │       └────────── 环境反馈 ←──────┘        │
 │                    (工具返回/页面变化)        │
 └────────────────────────────────────────────┘
```

和 chatbot 的本质区别就一句话：**chatbot 是函数（输入→输出，一次调用），agent 是进程（在环境里持续运行、维护状态、追求目标）**。面试里能讲出这句，就已经超过一半候选人。

#### Agentic 系统的六个核心议题（本周七座山头）

| 议题 | 一句话 | 哪天讲 |
|---|---|---|
| **架构骨架** | ReAct 循环 / Plan-and-Execute / Reflexion 三范式怎么选 | Day 123 |
| **Context Engineering** | 上下文是新的稀缺资源：压缩、筛选、结构化、谁该进 prompt | Day 124 |
| **Tool Use 工程** | Function Calling 的鲁棒性、MCP 协议、Computer Use 的坑 | Day 125 |
| **记忆系统** | 短期 buffer / 中期摘要 / 长期向量库的分层与失效处理 | Day 126 |
| **多智能体编排** | 主管-工人 / 流水线 / 辩论 / 市场四种模式的适用边界 | Day 127 |
| **评估与观测** | Agent 怎么打分？SWE-bench 的教训、轨迹级评估、线上 guardrails | Day 128 |

> 📍 今天的总览日先把「agent 第一性原理」焊死：**模型不变，世界在变；prompt 是快照，context 才是现场**。后面六天全部是这句话的工程展开。

#### 今日高频 Q&A 速记

- **Q：Agent 和 Workflow（工作流）有什么区别？**
  A：Workflow 的**控制流是人写死的**（if/else 都在代码里），LLM 只是其中的节点；Agent 的**控制流是模型自己决定的**（下一步做什么由模型根据观察输出）。工程上混合架构最实用：确定性强的部分用 workflow，开放决策的部分放给 agent。

- **Q：为什么说「搜索就是规划」？**
  A：今天的转盘锁就是答案——agent 面对的任务可以建模为状态图（状态=context，动作=tool call），规划=图上找路径。区别只在于：算法的图是显式给定的，agent 的图是隐式的、要靠模型在线展开。**Week 17 的 RL 教模型如何估值（V/Q），Week 18 的 test-time compute 教模型展开时多想几步，Week 19 教它把展开结果变成行动。** 三周是一条线。

- **Q：Agent 最大的敌人是什么？**
  A：**失控循环（infinite loop）与上下文腐化（context rot）**。前者靠最大步数/超时/循环检测/预算熔断兜底；后者靠 context engineering——不断把对话历史压缩、筛选、重排，只让高信号信息占用宝贵的窗口。Day 124 细讲。

- **Q：单机 demo 和生产级 agent 差在哪？**
  A：差在**可观测性、容错、成本治理**三件事。生产系统里每一次 tool call 都要记录（trace）、每一次失败都要有降级策略（重试/换工具/求助人）、token 与延迟都要有预算和账单（成本围栏）。这正是你 Week 13-16 学的可观测性/SRE 思路在 agent 领域的复用——**agent 工程 = 系统工程 + 模型能力**。

- **Q：现在面试 agent 岗，什么项目最加分？**
  A：能讲清楚「失败模式」的项目。不是跑通 SWE-bench 的 demo，而是「我的 agent 在 X 类任务上 60% 概率陷入循环，我加了 Y 机制后降到 15%」这种**带数据、有归因、有迭代**的工程故事。Week 19 每天的连环问都在往这个方向训练你的表达。

#### 本周自检清单（Day 128 复盘用）

- [ ] 能徒手画出 ReAct 循环并说出每一环的失败模式
- [ ] 能解释 context engineering 为什么比 prompt engineering 更值钱
- [ ] 能列出 MCP 协议解决的问题和它的架构（client/server/tool）
- [ ] 能设计一个 agent 记忆系统的三层结构与失效降级
- [ ] 能对比四种多智能体编排模式并给出选型决策树
- [ ] 能讲出 SWE-bench 的评测设计和一个它的已知缺陷
- [ ] 能列出生产级 agent 的 5 个必备 observability 指标

---

> 🎭 **今日金句**：「Chatbot 是函数，agent 是进程；转盘锁教我们的不是 BFS，而是——**凡是能建模成状态搜索的任务，就别指望模型靠灵感，给它图、给它验证器、给它预算。**」
>
> 明日 Day 123 预告：算法题 + Agent 架构三范式深讲（ReAct / Plan-and-Execute / Reflexion 选型与失败模式）
