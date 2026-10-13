# Day 131 — 钥匙和房间 + 沙箱与权限工程

> **Week 20 · Day 3** 🛡️ Agent 安全与对齐工程
> 算法题：钥匙和房间（Keys and Rooms, LC 841）· 权限可达性分析的算法原型
> 面试主题：沙箱与权限工程 —— 隔离技术谱系 / 权限模型 / 最小权限 / agent 爆炸半径治理

---

## 一、今日算法题：钥匙和房间（Keys and Rooms）

### 题目

有 `n` 个房间，标记为 `0 ~ n-1`。每个房间有一把钥匙列表 `rooms[i]`，其中 `rooms[i][j]` 是房间 `i` 里能打开房间 `rooms[i][j]` 的钥匙。最初，除了 `0` 号房间，其他房间都是锁着的。

问：能否从 `0` 号房间出发，**拿到所有房间的钥匙并最终进入所有房间**？

```
输入: rooms = [[1],[2],[3],[]]
输出: true
解释: 0→拿 1 的钥匙→进 1→拿 2 的钥匙→进 2→拿 3 的钥匙→进 3。全部可访问。

输入: rooms = [[1,3],[3,0,1],[2],[0]]
输出: false
解释: 房间 2 的钥匙只在房间 2 里（chicken-and-egg），谁也进不去。
```

**约束**：`1 <= n <= 1000`，`0 <= rooms[i].length <= 1000`，总钥匙数 `sum(rooms[i].length) <= 3000`。

### 为什么今天选这道题（主题同构）

昨天的 AC 自动机管**检测**（注入来了拦不拦得住），今天管**权限**——就算注入了，agent 能摸到的东西也必须是有限集合。钥匙和房间就是权限工程的最小模型：

| 算法概念 | 权限工程对应 |
|---|---|
| 房间 | 资源 / 服务 / 数据目录 / API endpoint |
| 钥匙 | 权限 / 凭证 / capability |
| 0 号房间 = 初始持有 | 进程启动时的初始权限集（token、env、挂载） |
| 从 0 出发能到哪些房间 | **权限可达性分析**：这个 agent 最坏能摸到哪里 |
| 到不了的房间 | 权限缺口清单（该开的没开 = 故障；不该开的没开 = 安全） |
| 钥匙散落在别的房间 | **权限放大链**：拿到 A 的凭证 → 解锁 B → 拿到 B 的凭证 → 解锁 C……**横向移动（lateral movement）的图模型** |
| 房间 2 的钥匙在房间 2 里 | 权限死锁 / 循环依赖：谁也别想进去（也是启动顺序设计 bug） |

安全工程里这叫 **privilege reachability**：红队渗透的本质就是在这张图上找从"初始权限"到"目标资产"的路径——和 Day 129 单词接龙（最短注入链）是一对：**攻击找路径，防守量可达集**。

### 思路：一张有向图 + 一次 BFS/DFS

建图已经天然建好（邻接表 = `rooms`），问题退化为**从节点 0 可达的节点数是否等于 n**。

```python
from typing import List
from collections import deque

class Solution:
    def canVisitAllRooms(self, rooms: List[List[int]]) -> bool:
        n = len(rooms)
        seen = [False] * n
        seen[0] = True
        queue = deque([0])
        count = 1                     # 已访问计数，避免最后 O(n) 扫 seen

        while queue:
            room = queue.popleft()
            for key in rooms[room]:   # 在该房间捡到的所有钥匙
                if not seen[key]:
                    seen[key] = True
                    count += 1
                    queue.append(key) # 拿到钥匙 = 房间已解锁 = 直接入队
        return count == n
```

DFS 版同样 O(V+E)：

```python
def canVisitAllRooms(self, rooms):
    def dfs(room):
        for key in rooms[room]:
            if key not in seen:
                seen.add(key)
                dfs(key)
    seen = {0}
    dfs(0)
    return len(seen) == len(rooms)
```

**复杂度**：`O(V + E)` 时间（每个房间进出队一次、每条"钥匙边"看一次），`O(V)` 空间（visited + 队列）。n ≤ 1000、边 ≤ 3000 的约束对这个复杂度毫无压力。

**易错点**：
1. **入队即标记（seen 在 enqueue 时置位，不是 dequeue 时）**——否则同一把钥匙被两个人递给你会重复入队。和 BFS 所有题同一个纪律（Day 122 转盘锁）。
2. `seen[0] = True` 先置位再入队——起点自己会发自己的钥匙（`rooms[i]` 可以包含 `i`），不置位就死循环。
3. 不要尝试"统计钥匙总数 ≥ n-1 就 true"——反例 `rooms = [[1,1],[2],[0]]`：钥匙一大把，但访问依赖顺序，贪心计数必翻车。**持有钥匙 ≠ 到过房间，到过房间才能发新钥匙**——这是"可达性"和"拥有"的区别。

### 正确性为什么显然

BFS/DFS 的不变量：出队（或递归进入）房间 r 时，`seen[r]` 已置位且 r 的所有钥匙边都会被检查。归纳可得**从 0 出发沿边可达的所有节点恰好被访问一次**。题目问的就是可达集大小——标准教科书结论，无需额外证明。

### 连环问（面试官的十八般武艺）

**Q1：不要 true/false，返回所有到不了的房间列表。**
逐房间扫 `seen`，收集 `false` 项。面试话术："这个列表在生产里就是**权限缺口清单**——配合审计，它回答两个问题：该开的没开（故障排查）和 不该开的没开（安全确认）。"

**Q2：最少需要在初始就持有多少把"外部钥匙"，才能保证进入所有房间？（最小初始权限集）**
这是本题最有安全味的变体。贪心地想：从 0 可达的集合闭包是 R₀；若 R₀ ≠ 全部，必须**从外部**给一把能打开 R₀ 之外房间的钥匙。经典 O(V+E) 做法：
- 跑一遍可达集 R₀；
- 对 R₀ 外的每个房间 r，看它的"入边"是否全部来自 R₀ 之外——统计每个外部房间的**来自可达集内部的入边数**；
- 每组"互相够不着"的外部环（如 2 的钥匙在 3、3 的钥匙在 2）至少需要从外部给一把钥匙 → **对外部子图数强连通分量（或简单点：对外部图再做可达闭包，每次闭包扩张都 +1 并选闭包里的任意房间）**。
- 贪心正确性：每次给外部钥匙都解锁一个最大闭包，交换论证。
映射到权限工程：**最小初始权限集 = 系统启动时必须显式授予的 capability 清单**，多一把都是过度授权。

**Q3：如果把"拿到钥匙"改成"花 cost[i][j] 精力偷钥匙"，进齐所有房间的最小总成本？**
边带权 + 需要"激活"所有节点 → 状态是 `(当前所在房间, 已收集钥匙集合)`，2ⁿ 状态 × n 转移 = **TSP 味的 NP-hard**（ Held-Karp O(n²·2ⁿ)）。诚实告诉面试官"这个问题退化了"，并给出近似/启发式（先可达再补缺口），比硬凹 DP 加分。

**Q4：怎么检测"权限循环继承"（A 的钥匙在 B、B 的钥匙在 A，都进不去）？**
本题只需判可达性不用判环，但权限系统里环 = 死锁 = 启动 bug。三色素描版 DFS（Day 89 课程表 II 同款）：递归栈上遇到灰色节点即成环；或对外部子图跑 Tarjan 数 SCC，SCC 大小 > 1 或自环 = 循环依赖点。工程话术："这是权限配置 CI 的静态检查项——上线前扫一遍环，比运行时死锁强。"

**Q5：反向问题——给定"必须到达的房间列表" must，求最小初始钥匙集？**
反向建图 + 必达点标记：must 中每个点沿反图向上找最近"外部入口"。变形自 Q2，面试答出"反向可达 + 覆盖"的骨架即可。

**Q6：和 Day 123「钥匙和迷宫」什么关系？**
那题状态是 `(r, c, mask)`——**锁的门可能重进**（拿到钥匙后原路折返），所以 visited 必须是三元组；本题钥匙**拿到即永久持有**，状态退化成"房间集合"，visited 一维就够。一句话区分：**持有物是否可逆**——迷宫里钥匙改变的是"你能通过哪些边"，房间题钥匙改变的是"哪些节点已解锁"。两个都是 agent 状态建模的经典教材。

**Q7：数据量放大——10⁹ 房间、钥匙边 10¹⁰，单机放不下？**
- 外存 BFS：分区 + 迭代式 MapReduce 传递"frontier 集合"（Day 77 Pregel 思想，Giraph/GraphX 就是干这个的）；
- bitset 存 seen（10⁹ bit ≈ 125MB，单机内存其实塞得下）；
- 如果只需要"是否全覆盖"而不需要清单 → HyperLogLog 近似计数（Day 78 流式思维的图版）；
- 真实安全场景：这张图本来就是稀疏的（凭证散落在有限 vault 里），一般不会爆炸。

### 工程映射一句话

> **钥匙和房间 = 权限可达性的最小完备模型。安全团队拿它做横向移动面测算（从任意一台被攻陷的机器出发能摸到哪里），架构师拿它做启动权限审计（最小初始 capability 清单）。复杂版本（带权、缺口、SCC）就是云厂商 IAM reachability analyzer 的算法内核——AWS IAM Access Analyzer、GCP Policy Intelligence 底层全是这张图。**

---

## 二、面试技巧：沙箱与权限工程

### 总纲：沙箱不是挡注入的，是限爆炸半径的

接 Day 130 的金句——**"注入一定会成功，所以爆炸半径必须有限"**。沙箱（sandbox）和权限工程就是"爆炸半径"的具体施工：

```
纵深防御五层（Day 129 总表）：
L1 输入过滤      → 拦显性攻击        （Day 130 AC 自动机）
L2 归一化+模糊   → 拦变体            （Day 130 两层式）
L3 语义/嵌入检测 → 拦改写            （embedding 相似度）
L4 运行时沙箱    → 拦后果  ← 今天的主战场
L5 出向审计      → 兜底动作          （Day 130 出向 DLP）
```

面试官问"怎么保证 agent 安全"，**只答 prompt 防护 = 只修了 L1~L3，L4/L5 一字不提 = 没有生产思维**。今天的目标是把 L4 讲穿。

### 1. 沙箱技术谱系（按隔离强度 × 性能代价排）

| 层级 | 技术 | 隔离内核 | 启动延迟量级 | 代表场景 |
|---|---|---|---|---|
| 语言级 | seccomp-bpf / AppArmor / SELinux | 系统调用过滤 / MAC 策略 | 无（进程内） | Chrome 渲染进程、容器默认seccomp |
| 进程级 | chroot(1979) → FreeBSD jail(2000) → OpenVZ | 文件系统视图 / 内核分区 | 毫秒 | 上古 hosting，已淘汰 |
| 容器级 | namespaces + cgroups | 共享宿主内核，隔离视图 | 100ms 档 | Docker、K8s pod |
| 用户态内核 | gVisor (Google) | 应用内核截获 syscall，用户态重实现 | 100~200ms | GKE Sandbox、不可信多租户 |
| 轻量 VM | Kata Containers / Firecracker microVM | 真实硬件虚拟化（KVM）+ 极简设备模型 | 125ms 档（Firecracker 论文数据） | AWS Lambda、Fargate、Fly.io |
| Wasm | Wasmtime / V8 Isolate | 线性内存 + capability 式系统接口（WASI） | 毫秒级，冷启动碾压容器 | Cloudflare Workers、Shopify Functions |

**面试三板斧（背这三句就够立住）**：
1. **共享内核是容器安全的阿喀琉斯之踵**——容器进程和宿主共用同一个 kernel，内核 0day 就是逃逸通行证。所以多租户跑不可信代码 = 至少要 gVisor 用户态内核（拦截+重实现 syscall，攻击面从整个内核缩到 gVisor 本身）或 Kata/Firecracker（硬件虚拟化，宿主机内核压根不可见）。
2. **Firecracker 的洞察 = 设备模型做减法**：传统 QEMU 虚拟化 2~3GB 内存+分钟级启动，Firecracker 砍到 <5MB 内存 + ~125ms 启动，秘诀是"一个 VM 只给一个 virtio-net + virtio-block，砍掉 VGA/PCI 热插拔一切花活"——**减设备模型=减攻击面=减启动路径**，AWS Lambda 用它跑百万租户。
3. **Wasm 是沙箱的新答案**：线性内存 + 无 syscall 直访 + WASI capability 式文件打开（只能打开显式传入的 fd）= 默认拒绝一切的反向安全模型；冷启动毫秒级适合 per-request 沙箱。代价：跑不了所有 Linux 二进制、CPU 密集有 10~30% 损耗、GPU 支持才刚起步。

**Agent 场景的选型决策树**：
- 跑的是**你自己写的工具代码**、语言可控 → Wasm isolate（ Workers 式，便宜快）；
- 跑**任意开源代码 / LLM 生成的代码**（code interpreter、SWE-agent）→ Firecracker/Kata 级 microVM（要完整 Linux  ABI）；
- 跑**可信团队代码、只是防误操作** → 容器 + 只读 rootfs + drop ALL caps + seccomp + 非 root，够用；
- 云厂商兜底 → gVisor/runsc，多租户不可信负载的默认答案。

### 2. 权限模型：从 Unix rwx 到 capability

**四种模型一句话各配一个锚点**：

| 模型 | 一句话 | 代表 | agent 场景映射 |
|---|---|---|---|
| DAC（自主访问控制） | 资源主人说了算，Unix rwx 三读三写 | 传统文件系统 | 单机脚本默认状态，别带进生产 |
| MAC（强制访问控制） | 系统级策略，进程能标标签，SELinux/AppArmor | RHEL 默认 enforcing | 沙箱内的最后防线（容器默认 seccomp 即此味） |
| RBAC（基于角色） | 权限绑角色，人绑角色，扁平好审计 | K8s RBAC、AWS IAM roles | 中等规模默认答案；"角色爆炸"是公认痛点 |
| ABAC（基于属性） | 策略表达式：`允许 if 部门==X && 时间==上班 && 设备受管` | AWS IAM condition、OPA | 细粒度场景；策略复杂度是税 |
| ReBAC（基于关系） | "权限=图上的边"，Google Zanzibar | Google Drive 共享、OpenFGA | 资源之间有深层关系的场景（文档树、组织架构） |

**面试必讲的三个概念**：

**① 最小权限原则（least privilege）**：默认拒绝，按需授予，用完即收。反面教材=容器跑 root + `--privileged` + 挂载 `/var/run/docker.sock`（拿到 docker.sock = 拿到宿主 root，等于没沙箱）。正确姿势四件套：`USER nonroot` + `readOnlyRootFilesystem` + `drop: ["ALL"]` capabilities + 空 `runAsGroup`。

**② Capability vs ACL（ambient authority 之毒）**：
- **ACL 模型**（Unix 传统）：进程靠"自己是谁"（uid）行使权限，环境里任何代码都自动继承全部权限——**ambient authority，环境里的权威**。
- **Capability 模型**：权限是显式持有的**不可伪造令牌**，用的时候必须出示。Linux fd 就是最好的 capability——`open()` 返回的 fd 只能由持有者使用，不能猜。
- **confused deputy（Day 129  callback）**：编译器收到你的代码 + 你的凭证，替攻击者写了文件——编译器被"ambient authority"搞糊涂了，用别人的权威替别人办事。解药=**把权限从环境变量挪进显式 capability 传递**（这正是 Wasm WASI 的设计哲学：没有全局文件系统，只有传入的 fd）。
- agent 版落地：agent 的 token 不该躺在环境变量里等任何 prompt 注入都能读——**短期凭证 + 按需签发（just-in-time）+ 单工具单 scope**，这就是把 ambient authority 拆成 capability。

**③ just-in-time access（JIT 权限）**：不要给 agent 一张永久万能卡，要"这把钥匙 15 分钟有效、只开这三个房间"（呼应今天的题：钥匙本身带 TTL）。参照物：AWS STS 临时凭证、Vault dynamic secrets、Google 的 BeyondCorp 零信任（默认不信任内网，每次访问都验）。

### 3. Agent 场景的五道施工线

**① 代码执行沙箱（最重的一块）**
LLM 生成代码、数据分析、SWE-agent 跑测试——执行环境必须假设"这段代码充满敌意"：
- 文件系统：临时容器/ microVM，跑完即焚（ephemeral，AWS Lambda 式）；
- 网络：**默认断网，出口走 allowlist 代理**（域名白名单 = Day 130 出向审计的具体形态；编译装依赖可以走固定 pypi/npm 镜像代理）；
- 资源：CPU/内存/磁盘/进程数四上限（cgroups）+ 墙钟超时（wall-clock timeout，不是 CPU timeout——`sleep(10000)` 也要能杀）；
- 产出：只能带走声明的产物文件（artifact 目录），其余丢弃。
参照物：**E2B、Modal、Daytona** 三家都是这个生意；Claude Code 的 bash 工具自带权限确认分级。

**② 工具权限分级（Day 125 的确认门，今天给理论根基）**

| 级别 | 例子 | 策略 |
|---|---|---|
| L0 只读 | 读文件、grep、查日历 | 自动放行（仍然沙箱内） |
| L1 可逆写 | 写草稿、建分支、发非公开评论 | 放行 + 日志留痕 |
| L2 不可逆写 | 删文件、发公开消息、改生产 DB | 用户确认门（结构化确认=影响范围+回滚方案，Day 125） |
| L3 资损敏感 | 支付、转账、下单 | 双人确认 / 硬编码限额 / 独立审批流 |

**分级本质 = 对每类动作预计算"爆炸半径"，把确认成本花在半径大的地方**。

**③ 凭证隔离（credential isolation）**
- 不让 agent 直接持有高价值 token，**代持模式**：agent 只拿到"代持服务的句柄"，实际签名动作发生在沙箱外的签名器（HSM/Vault sidecar），并带策略校验；
- 高敏操作（生产库写）用**短期凭证 + 双人确认**，呼应 JWT 短 TTL 设计；
- 蜜罐凭证（Day 130 canary 的凭证版）：vault 里埋一个假 AWS key，被使用 = 报警 + 定位泄漏路径。

**④ 网络隔离**
- 归一化出口：所有出向 HTTP 走企业代理，代理层做域名分级 + 内容 DLP（呼应 AC 自动机检测出向正文）；
- 入向假定恶意：任何网页/邮件/文档内容进 context 前过"不可信标注包裹"（Day 130 四件套）；
- eBPF 层兜底：Cilium NetworkPolicy 做 pod 级 L3/L7 策略，agent 容器默认只许 DNS + 白名单。

**⑤ 人机确认门（HITL）的工程学**
确认门最怕**确认疲劳**（Day 125 callback：无脑点同意 = 门形同虚设）。三件套：
- **批量+延迟确认**：攒 5 分钟低风险写操作一次性确认（把"每次都问"变"定期对账"）；
- **风险自适应**：同一动作第 1 次问，连续 3 次同类干净执行后降档为日志留痕（信任是挣来的——但降档上限到 L2，L3 永不降）；
- **可解释确认**：确认卡片展示 diff/影响面/回滚方案，而不是一个干巴巴的 "Allow?"。

### 4. 连环问速答

**Q1：沙箱逃逸了怎么办？（面试官最爱）**
"**假设逃逸，设计纵深**。Firecracker 级的硬件虚拟化把宿主内核藏起来；网络默认断 + allowlist 代理让逃逸者够不到内网元数据（169.254.169.254 是经典突破口）；凭证用短期+最小 scope，偷到 15 分钟后作废；出向 DLP 审查 payload；全程轨迹审计回放（Day 128）定位从哪步开始的。逃逸一层不是失败，**每一层都让攻击者付出时间代价、给防御方留下检测窗口**——和 Day 130 检测器栈同一个哲学。"

**Q2：性能 vs 隔离怎么 trade？**
"三层回答：第一，选最弱够用的隔离——可信团队代码容器+seccomp 就够，不要上 microVM；第二，热路径优化冷启动——pool 预热（常驻 N 个空沙箱）、snapshot restore（CRIU/Firecracker snapshot resume，毫秒级复活）、Wasm 跑简单工具；第三，按租户信任分级分流——匿名流量进 Firecracker，企业租户容器，内部工具裸进程。"

**Q3：agent 权限该不该动态伸缩？**
"方向对但要加约束。JIT 签发是对的（按需+短 TTL），但要防**权限爬升（privilege escalation）**：agent 以'我想做 X'为由申请权限，审批者（人或 policy）要校验 X 与当前任务的因果链，否则 agent 学会编理由骗权限——这是 prompt injection 的权限版（'请批准我 rm -rf 以便完成搜索任务'）。工程解：申请必须引用**上游任务工件**（trace 里的父意图），policy 校验引用链完整。"

**Q4：怎么给面试官讲 capability 才不俗？**
"从 Unix fd 讲起：fd 就是 capability——不可伪造、按需持有、用完关。然后对照环境变量里的 AWS key：谁都能 `os.environ.get`，ambient authority 的经典病灶，confused deputy 的温床。最后落到 agent：token 不该在 env 里躺着，该在签名器里躺着，agent 只递意图、递不出凭证。这三句讲完，权限模型这题就赢过 90% 的候选人了。"

**Q5：跟越狱（jailbreak）防御什么关系？**
" jailbreak 破的是模型对齐层，沙箱破不破都该假设它会破——**对齐是概率，沙箱是边界**。越狱成功的 payload 如果只能在一个无网络、无凭证、只读文件系统的沙箱里转，最坏结果是浪费一点 token。所以安全架构里越狱防御（L1~L3 的事）和沙箱（L4）是互补双保险：一个降低发生概率，一个收敛发生损失。"

**Q6：veRL / agentic RL 里沙箱在哪？**
"两个位置：① **rollout 沙箱**——agent 在训练中要和真实环境交互（跑测试、调工具），必须把 rollout worker 关进隔离环境：网络断、文件系统快照化（每 episode 重置到干净 state，否则脏环境造成 reward 漂移）、墙钟超时防死循环（Day 113 长度爆炸的经济兜底）；② **reward 沙箱**——可验证奖励要执行代码/跑单测，判分器自己就是代码执行器，同样要隔离，不然 reward hacking 里最狠的一招是'让判分器给我打满分'。生产参照物：AgentBench/SWE-Gym 的 Docker 化环境、τ-bench 的数据库隔离、agent-arena 思路=环境即评测=环境必须可重置可审计。"

### 5. 自检清单

- [ ] 沙箱谱系背得出 chroot→jail→容器→gVisor→Kata/Firecracker→Wasm 并各说一个数字（延迟/内存）
- [ ] 能徒手讲清"共享内核=容器逃逸"的因果链
- [ ] capability vs ACL + ambient authority + confused deputy 三件套能互相推导
- [ ] Firecracker 三要素：microVM / 精简设备模型 / ~125ms 启动（AWS Lambda）
- [ ] 工具权限分级表 L0~L3 能默写，每级一个例子一个策略
- [ ] 确认疲劳三解法：批量延迟 / 风险自适应降档 / 可解释确认
- [ ] JIT 权限的防爬升设计：任务工件因果链校验
- [ ] 沙箱逃逸五层纵深回答模板（虚拟化→网络→凭证→DLP→审计）
- [ ] veRL 两处沙箱：rollout 环境可重置 + reward 判分器隔离
- [ ] 今天算法题：BFS 可达性 O(V+E)、Q2 最小初始权限集贪心、Q4 环检测三色素描

### 金句

> **"沙箱的目的不是挡住攻击，是让攻击成功之后世界依然可恢复——对齐是概率，沙箱是边界。给 agent 的每一把钥匙都该带 TTL，就像今天的题：进得去多少房间不重要，重要的是你永远清楚它进得去哪些房间。"** 🛡️

---

### 明日预告

**Day 132 · Week 20 Day 4**：红队自动化平台设计 —— 从"手测 prompt"到工业化攻击流水线（候选算法题：模糊测试的覆盖率反馈 / 语法变异树的搜索 / 对抗样本生成的贪心坐标下降 = GCG 的算法骨架）🔴
