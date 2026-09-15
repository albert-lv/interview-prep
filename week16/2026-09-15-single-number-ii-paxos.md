# Day 103 — 只出现一次的数字 II + Paxos 算法与 Multi-Paxos

> 📅 2026-09-15 | Week 16 Day 3 | 主题：分布式系统核心协议 🔗

---

## 今日算法题

### 只出现一次的数字 II（Single Number II）

**题目来源**：[LeetCode 137](https://leetcode.cn/problems/single-number-iii/)

**描述**：给你一个整数数组 `nums`，其中**除了某个元素只出现一次**外，其余每个元素都恰好出现**三次**。找出那个只出现了一次的元素。要求时间复杂度 `O(n)`，空间复杂度 `O(1)`。

**示例**：
```
输入: nums = [2,2,3,2]
输出: 3

输入: nums = [0,1,0,1,0,1,99]
输出: 99
```

---

### 解题思路：位运算 — 三进制计数器

前两天的 Single Number I（全员异或）和 III（异或分治）都建立在「**出现 2 次**」这个前提下——二进制天然支持模 2。

现在每个数出现 **3 次**，异或就失效了（三个相同的数异或 = 它自己）。怎么办？

**核心洞察**：逐位统计。一个 int 有 32 位，对每一位统计整个数组中该位为 1 的次数 `count`。如果唯一数该位为 0，`count % 3 == 0`；如果为 1，`count % 3 == 1`。把每一位的 `count % 3` 拼起来就是答案。

**但逐位统计需要 32 次遍历 × 每个数**，能不能一遍过？

#### 升级版：ones/twos 双变量状态机

把每一位的计数状态建模为**模 3 计数器**，状态：`00 → 01 → 10 → 00`（出现 0、1、2 次）：

- `ones`：第 i 位为 1 ⟺ 数字 i 出现次数 ≡ 1 (mod 3)
- `twos`：第 i 位为 1 ⟺ 数字 i 出现次数 ≡ 2 (mod 3)

每来一个新数 `num`，状态转移：

```
ones = (ones ^ num) & ~twos    // 原来是 00 或 01 且 num 该位为 1 → 进位；原来是 10 → 清零
twos = (twos ^ num) & ~ones    // 用更新后的 ones 防止 01→10 和 10→00 互相干扰
```

**为什么正确？** 用真值表验证（`o` = 旧 ones 位，`t` = 旧 twos 位，`n` = num 该位）：

| o | t | n | 次数→新状态 | 新 ones | 新 twos |
|---|---|---|---|---|---|
| 0 | 0 | 0 | 0→0 | 0 | 0 |
| 0 | 0 | 1 | 0→1 | **1** | 0 |
| 0 | 1 | 0 | 2→2 | 0 | **1** |
| 0 | 1 | 1 | 2→0 | 0 | 0 |
| 1 | 0 | 0 | 1→1 | **1** | 0 |
| 1 | 0 | 1 | 1→2 | 0 | **1** |

`ones = (o ^ n) & ~t` 和 `twos = (t ^ n) & ~new_ones` 恰好覆盖全表。三遍后所有出现 3 次的数归零，`ones` 就是答案。

> 💡 **本质**：`ones ^ num` 是"无进位加 1"，`& ~twos` 是"进位约束"——两个数合起来就是一个**三进制计数器**，和全加器的半加器/全加器思想一脉相承。

---

### 代码实现

```go
package main

import "fmt"

func singleNumber(nums []int) int {
    ones, twos := 0, 0
    for _, num := range nums {
        ones = (ones ^ num) & ^twos  // 第一次出现 → 记到 ones
        twos = (twos ^ num) & ^ones  // 第二次出现 → 从 ones 移到 twos
    }
    // 第三次出现时：ones 该位为 1（旧）& ~twos... 
    // 实际第三次：ones ^ num = 0, & ~twos = 0 → ones 清零
    //            twos ^ num = 0（已被 ^ones 消掉）→ 归零
    return ones
}

func main() {
    fmt.Println(singleNumber([]int{2, 2, 3, 2}))                  // 3
    fmt.Println(singleNumber([]int{0, 1, 0, 1, 0, 1, 99}))        // 99
    fmt.Println(singleNumber([]int{-2, -2, 1, 1, -3, 1, -3, -3, -4, -2, -3})) // -4
}
```

```python
def single_number(nums):
    ones = twos = 0
    for num in nums:
        ones = (ones ^ num) & ~twos
        twos = (twos ^ num) & ~ones
    return ones
```

---

### 复杂度分析

| 维度 | 结果 | 说明 |
|---|---|---|
| 时间复杂度 | **O(n)** | 单次遍历，位运算 O(1) |
| 空间复杂度 | **O(1)** | 两个整型变量 |

> 💡 对比：哈希表计数 O(n) 空间；逐位统计 32 遍 O(32n) = O(n) 但常数大。ones/twos 是**理论最优**。

---

### 面试官会怎么追问

> 💬 **面试官**：如果每个数出现 **k 次**，一个数出现 1 次呢？

🎯 **你**：把模 3 推广到模 k——需要 `log₂k` 个状态变量（k=3 时用 2 个：ones/twos；k=4 时用 2 个就够，因为 2²=4）。通用模板：

```python
def single_number_general(nums, k):
    bits = [0] * 32
    for num in nums:
        for i in range(32):
            bits[i] += (num >> i) & 1
    ans = 0
    for i in range(32):
        if bits[i] % k != 0:
            ans |= (1 << i)
    return ans
```

时间 O(32n)，空间 O(1)。面试时先写通用版（清晰），再被追问优化时掏出 ones/twos（惊艳）。

> 💬 **面试官**：如果数组里有**两个**数各出现一次，其余出现三次呢？（Single Number II+III 混合版）

🎯 **你**：**不能**直接套用异或分治（三次的元素在组内不会抵消）。通用解：模 3 后得到 `a*2 + b`（a、b 是出现 1 次的两个数……不对）。正确做法是**先用模 3 通用解法筛出所有"按位贡献"**，但两个唯一数会纠缠。实战中最优解是哈希表 O(n) 空间；位运算技巧可以逐位确定每个唯一数，但需要知道答案的数值范围。这个问题本质是**信息论下界**：区分两个唯一数至少需要 Ω(log n) 比特的信息，无法 O(1) 空间解决。诚实说出这个下界比硬套公式更加分。

> 💬 **面试官**：为什么是 `& ~twos` 而不是 `& ~ones`？

🎯 **你**：防止**状态冲突**。考虑一个数已经出现了 2 次（twos=1），此时第三次出现：`ones ^ num` 会把 ones 从 0 变 1（错误进位），必须用 `~twos` 把这个进位挡住。同理 `twos` 更新时用 `~ones`（新值），防止 01→10 和 10→00 在同一轮互相干扰。两行的**先后顺序和取反对象**是这道题的精髓。

---

## 面试技巧

### Paxos 算法与 Multi-Paxos

> 昨天学了 Raft，今天上它的"老祖宗"——Paxos。面试时最经典的问题永远是：**"Raft 和 Paxos 有什么区别？"**

---

### 1. Paxos 是什么？为什么需要它？

**问题**：分布式系统中多个节点可能同时收到写请求，网络会丢消息、延迟、乱序，节点会宕机——**如何在不可靠的组件上构建可靠的共识？**

Paxos 由 Leslie Lamport 于 1990 年提出（论文直到 1998 年才发表——因为审稿人觉得"希腊议会比喻"太不严肃）。它是**第一个被严格证明正确**的分布式共识算法，也是后续所有共识算法（Raft、Zab、VR）的理论根基。

---

### 2. 角色体系

| 角色 | 职责 | 是否可兼任 |
|---|---|---|
| **Proposer** | 发起提案（proposal），推动共识达成 | ✅ 可多个 |
| **Acceptor** | 对提案进行**投票**（accept/reject），是算法的核心 | ✅ 通常 2f+1 个 |
| **Learner** | 学习已被选定的值（不参与投票） | ✅ 可多个 |

> 💡 **关键**：Paxos 只要求**多数派**（majority）接受即可达成共识。5 个 Acceptor 中 3 个接受 = 多数派 = 值被选定。

---

### 3. 两阶段协议（Basic Paxos）

#### Phase 1：Prepare（准备）

```
Proposer → Acceptor:  Prepare(n)    // n 是提案编号（全局递增）

Acceptor 收到后：
  if n > 已见过的最大编号:
    承诺不再接受编号 < n 的提案
    回复 Promise(n, 已接受的最大编号提案的值)
  else:
    拒绝
```

#### Phase 2：Accept（接受）

```
Proposer 收到多数派 Promise 后：
  如果有 Acceptor 返回了已接受的值 → 用**编号最大的那个值**作为自己的提案值
  否则 → 可以用任意值

Proposer → Acceptor:  Accept(n, value)

Acceptor 收到后：
  if n ≥ 已承诺的最小编号:
    接受该提案
    回复 Accepted(n, value)
  else:
    拒绝

Proposer 收到多数派 Accepted → 值被选定（chosen）
```

> 🔥 **面试必背一句话**：**"Phase 1 抢锁 + Phase 2 提交"**。Prepare 是"我要提一个编号为 n 的提案，你们别再理比我小的了"；Accept 是"这是我的正式提案，同意吗？"

---

### 4. 为什么需要两阶段？（面试连环追问核心）

> 💬 **面试官**：为什么不能一阶段直接提值？

🎯 **你**：因为**冲突**。两个 Proposer 同时提案：

```
P1 提案值=A，P2 提案值=B
- P1 发给 A1,A2,A3；P2 发给 A3,A4,A5
- A3 先收到 P1 后收到 P2
- 结果：A1,A2 接受 A；A3,A4,A5 接受 B
- 没有多数派 → 无法达成共识
```

Phase 1（Prepare）的作用是**抢占**：编号大的提案让 Acceptor 拒绝所有更小的提案，从而保证**任何时刻最多只有一个提案能获得多数派**。

> 💬 **面试官**：Paxos 能保证什么？不能保证什么？

🎯 **你**：

| 性质 | 内容 | 说明 |
|---|---|---|
| ✅ **安全性（Safety）** | 只有一个值会被选定；一旦选定，不再改变 | 两阶段 + 多数派交集保证 |
| ✅ **多数派可终止** | 如果多数派存活且能通信，最终能选定值 | 活锁风险见下 |
| ❌ **活性（Liveness）** | 不保证在有限时间内达成共识 | 两个 Proposer 互相抢编号 → 活锁 |

**活锁问题**：P1 发出 Prepare(1)，P2 发出 Prepare(2) → P1 的 Accept(1) 被拒绝 → P1 重新 Prepare(3) → P2 的 Accept(2) 被拒绝 → P2 重新 Prepare(4) → …… 无限循环。

**工程解法**：**随机退避**（随机等待一段时间再重试），或者直接进入 Multi-Paxos 用 Leader 避免竞争。

---

### 5. Multi-Paxos：从"一次共识"到"一串共识"

Basic Paxos 只能对一个值达成共识。实际系统需要**连续确定一系列值**（日志复制）。

**朴素方案**：对每个值跑一次 Basic Paxos → 每轮 2 次 RPC，太慢。

**Multi-Paxos 优化**：

```
第一步：选举一个稳定的 Leader（通过一次 Basic Paxos 选定 Leader ID）
后续：Leader 直接发起 Phase 2（跳过 Phase 1）
  - 每个日志槽位（slot/instance）一个提案编号
  - Leader 保持权威 → 无竞争 → 无活锁
  - 一轮 RPC 即可提交
```

> 💡 这里就能看出 **Raft 就是 Multi-Paxos 的工业实现**——强 Leader、日志槽位、心跳维持权威。区别在表述：Raft 用"术语"（term）和"日志索引"，Paxos 用"提案编号"和"实例号"。

---

### 6. Paxos vs Raft 终极对比（面试必考题）

| 维度 | Paxos | Raft |
|---|---|---|
| **角色** | Proposer / Acceptor / Learner（可兼任） | Leader / Follower / Candidate（互斥） |
| **Leader** | 无强 Leader（可竞争） | 强 Leader，一切经 Leader |
| **日志** | 每个 slot 独立编号 | 全局日志索引 + term |
| **选举** | 隐含在 Phase 1 中 | 显式投票，随机超时 |
| **理解难度** | 😱 论文晦涩，缺少工程细节 | 😊 论文清晰，直接可实现 |
| **工程实现** | 需自行补充（成员变更/日志压缩） | 完整定义（Snapshot/成员变更） |
| **典型系统** | Google Chubby、PaxosStore | etcd、Consul、TiKV |

> 🔥 **一句话速答**：**"Paxos 是理论最优解，Raft 是工程最优解。Paxos 告诉你'什么是正确的'，Raft 告诉你'怎么把它写出来'。"**

---

### 7. 高频面试连环问

> 💬 **面试官**：Paxos 和 2PC 有什么区别？

🎯 **你**：三个核心区别：
> 1. **目标不同**：Paxos 解决**共识**（多个节点对一个值达成一致），2PC 解决**事务原子性**（多个节点要么都提交要么都回滚）
> 2. **故障容忍不同**：Paxos 容忍少数派宕机（f < n/2），2PC 的协调者单点宕机则阻塞
> 3. **阻塞性不同**：2PC 在协调者宕机时阻塞（参与者持有锁），Paxos 只要多数派存活就能推进

> 💬 **面试官**：Chubby 用 Paxos，为什么不是 Raft？

🎯 **你**：Chubby 2006 年上线，Raft 2014 年才发表。而且 Chubby 的场景（分布式锁/元数据存储）需要的是**强一致性 + 高吞吐读**——Multi-Paxos 的 Learner 角色天然支持只读副本（Learner 不参与投票，只同步数据），架构上比 Raft 更灵活。历史 + 架构双重原因。

> 💬 **面试官**：Paxos 的提案编号怎么设计？

🎯 **你**：必须**全局唯一且单调递增**。经典方案：`编号 = (轮次 << 32) | 节点ID`。节点 1 第 5 轮 → `(5 << 32) | 1`。高 32 位是轮次（比较优先），低 32 位是节点 ID（保证唯一）。这样即使不同节点同时发起，编号也不会冲突，且可以通过比较判断新旧。

> 💬 **面试官**：为什么 Acceptor 要"承诺不再接受编号更小的提案"？

🎯 **你**：这是 Paxos 安全性的基石。如果 Acceptor 接受了编号 5 的提案后，又接受编号 3 的——那么另一个 Proposer 可能用编号 4 获得多数派，导致**两个不同的值都被"多数派接受"**。承诺机制保证：**一旦值 v 被选定（获得多数派），任何编号更大的提案在 Phase 1 都会发现 v 已被接受，从而在 Phase 2 沿用 v**，不会推翻。

---

### 8. 自检验查清单一分钟版

面试前快速过一遍，能答上 6/8 就稳了：

- [ ] Paxos 三个角色？（Proposer/Acceptor/Learner）
- [ ] 两阶段各做什么？（Prepare 抢锁 / Accept 提交）
- [ ] 为什么需要 Phase 1？（防止并发冲突，保证唯一提案能获得多数派）
- [ ] 活锁怎么解决？（随机退避 / Multi-Paxos 用 Leader）
- [ ] Basic Paxos vs Multi-Paxos？（单值 vs 连续日志，后者跳过 Phase 1）
- [ ] Paxos vs Raft 一句话？（理论 vs 工程）
- [ ] 提案编号怎么设计？（全局唯一单调递增，轮次<<32 | 节点ID）
- [ ] Paxos vs 2PC？（共识 vs 事务原子性，多数派容忍 vs 协调者单点）

---

## 参考资源

- [Paxos Made Simple (Lamport, 2001)](https://lamport.azurewebsites.net/pubs/paxos-simple.pdf) — 原始论文，建议配合讲解食用
- [Paxos Made Moderately Complex](https://www.cs.cornell.edu/courses/cs7412/2011sp/paxos.pdf) — 教学版，更容易理解
- [Raft 可视化](https://raft.github.io/) — 交互式演示共识过程
- 昨日内容：[Day 102 — 只出现一次的数字 III + 分布式一致性模型与 CAP/BASE](./2026-09-14-single-number-iii-consistency-models.md)
- 明日预告：**Gossip 协议与成员发现** 🔜

---

> 🎯 **今日金句**：*"Paxos 就像民主制度——设计得很美，运行起来到处都是妥协。但它证明了在混乱中达成一致是可能的，这就是它的伟大之处。"*
