# Day 105 — 交替打印 FooBar + 分布式锁设计与实现

> 📅 2026-09-17 | Week 16 Day 5 | 主题：分布式系统核心协议 🔗

---

## 今日算法题

### 交替打印 FooBar（Print FooBar Alternately）

**题目来源**：[LeetCode 1115](https://leetcode.cn/problems/print-foobar-alternately/)

**描述**：设计一个类 `FooBar`，它有两个方法：`foo()` 打印 `"foo"`，`bar()` 打印 `"bar"`。同一个 `FooBar` 实例会被两个线程同时调用：**线程 A 调 `n` 次 `foo()`，线程 B 调 `n` 次 `bar()`**。要求输出严格交替：`foobarfoobar...`（共 n 组），不能出现 `foofoo` 或 `barbar`。

**示例**：
```
输入: n = 2
输出: "foobarfoobar"
解释: 两个线程各打印 2 次，foo 必须在 bar 之前，bar 打完后 foo 才能继续。

输入: n = 3
输出: "foobarfoobarfoobar"
```

**约束**：`1 <= n <= 1000`，两个线程会**同时启动**，谁先拿到 CPU 不确定。

---

### 解题思路：同步原语控制执行顺序

#### 本质：一道"谁有资格执行"的调度题

剥掉并发外衣，这题就是：**维护一个布尔状态 `isFoo`，只有轮到你才能打印，打印完把资格让给对方**。

难点不在算法，在于**用对同步原语**。三种主流解法，面试全讲出来是满分答案：

#### 解法一：互斥锁 + 条件变量（最经典，必答）

```
锁保护状态 isFoo：
- foo 线程：拿到锁 → 发现不轮到自己（!isFoo）→ cond.Wait() 挂起
           → 轮到自己 → 打印 → isFoo = false → 唤醒对方
- bar 线程：同理，等待 isFoo == false
```

> 💡 **两个关键细节（面试官必抠）**：
> 1. **`Wait()` 必须放在 `while` 循环里，不是 `if`** —— 防止虚假唤醒（Spurious Wakeup）。POSIX 规范允许 `wait` 在没有 `signal` 的情况下返回，用 `while` 重新检查条件才安全。
> 2. **用 `Broadcast()` 唤醒，而不是 `Signal()`** —— 单唤醒可能恰好唤醒同类线程造成丢失唤醒（两个 foo 都睡着，bar 唤醒了一个 foo）；全部唤醒由 `while` 条件二次把关，最稳。

#### 解法二：两个信号量（最优雅，工程推荐）

信号量的语义天生就是"许可证"：

```
fooSem = 1（foo 手里先有一张证）
barSem = 0（bar 没证，先等着）

foo: 先 acquire(fooSem) 拿证 → 打印 → release(barSem) 把证给 bar
bar: 先 acquire(barSem) 拿证 → 打印 → release(fooSem) 把证给 foo
```

**初始值就编码了执行顺序**，不需要共享状态、不需要锁，两个 semaphore 各管各的，天然无竞态。

#### 解法三：Go channel（令牌传递，Go 的地道写法）

channel 当"接力棒"：打印权 = 通道里的一个令牌，谁拿到令牌谁打印，打印完把令牌丢给对方。

---

### 代码实现

```go
package main

import (
	"fmt"
	"sync"
)

// ========== 解法一：互斥锁 + 条件变量 ==========
type FooBar struct {
	n     int
	mu    sync.Mutex
	cond  *sync.Cond
	isFoo bool // true = 轮到 foo
}

func NewFooBar(n int) *FooBar {
	fb := &FooBar{n: n, isFoo: true}
	fb.cond = sync.NewCond(&fb.mu)
	return fb
}

func (fb *FooBar) Foo(printFoo func()) {
	for i := 0; i < fb.n; i++ {
		fb.mu.Lock()
		for !fb.isFoo { // ⚠️ while 而非 if：防虚假唤醒
			fb.cond.Wait()
		}
		printFoo()
		fb.isFoo = false
		fb.cond.Broadcast() // 唤醒所有等待者，由 while 条件二次把关
		fb.mu.Unlock()
	}
}

func (fb *FooBar) Bar(printBar func()) {
	for i := 0; i < fb.n; i++ {
		fb.mu.Lock()
		for fb.isFoo {
			fb.cond.Wait()
		}
		printBar()
		fb.isFoo = true
		fb.cond.Broadcast()
		fb.mu.Unlock()
	}
}

// ========== 解法三：channel 令牌传递（Go 推荐） ==========
func foobarWithChannel(n int) {
	fooCh := make(chan struct{}, 1)
	barCh := make(chan struct{}, 1)
	fooCh <- struct{}{} // foo 先拿令牌
	var wg sync.WaitGroup
	wg.Add(2)
	go func() { // foo 线程
		defer wg.Done()
		for i := 0; i < n; i++ {
			<-fooCh
			fmt.Print("foo")
			barCh <- struct{}{} // 把令牌传给 bar
		}
	}()
	go func() { // bar 线程
		defer wg.Done()
		for i := 0; i < n; i++ {
			<-barCh
			fmt.Print("bar")
			fooCh <- struct{}{} // 把令牌传回 foo
		}
	}()
	wg.Wait()
}

func main() {
	foobarWithChannel(3) // 输出: foobarfoobarfoobar
}
```

```python
import threading

class FooBar:
    """解法二：两个信号量 —— 初始值编码执行顺序"""
    def __init__(self, n: int):
        self.n = n
        self.foo_gate = threading.Semaphore(1)  # foo 先持证
        self.bar_gate = threading.Semaphore(0)  # bar 等证

    def foo(self, printFoo: 'Callable[[], None]') -> None:
        for _ in range(self.n):
            self.foo_gate.acquire()   # 拿自己的证（首次直接通过）
            printFoo()
            self.bar_gate.release()   # 发证给 bar

    def bar(self, printBar: 'Callable[[], None]') -> None:
        for _ in range(self.n):
            self.bar_gate.acquire()   # 等 foo 发证
            printBar()
            self.foo_gate.release()   # 发证回 foo
```

```python
# 解法一：条件变量版（Java/C++ 同样思路）
class FooBarCond:
    def __init__(self, n: int):
        self.n = n
        self.cond = threading.Condition()
        self.is_foo = True

    def foo(self, printFoo) -> None:
        for _ in range(self.n):
            with self.cond:
                while not self.is_foo:   # ⚠️ while 防虚假唤醒
                    self.cond.wait()
                printFoo()
                self.is_foo = False
                self.cond.notify_all()   # notify_all + while 二次检查

    def bar(self, printBar) -> None:
        for _ in range(self.n):
            with self.cond:
                while self.is_foo:
                    self.cond.wait()
                printBar()
                self.is_foo = True
                self.cond.notify_all()
```

---

### 复杂度分析

| 维度 | 结果 | 说明 |
|---|---|---|
| 时间复杂度 | **O(n)** | n 次打印，每次同步原语操作均摊 O(1) |
| 空间复杂度 | **O(1)** | 常数个锁/信号量/通道（忽略线程栈） |
| 同步开销 | 每轮 1 次上下文切换 | 被挂起线程的唤醒成本 |

> 💡 这题没有"更快"的算法——**打印次数是硬约束 O(n)，比的是同步原语的正确性和语义贴合度**。面试里 signal 三解全讲 = 展示你对并发工具箱的完整掌握。

---

### 面试官会怎么追问

> 💬 **面试官**：扩展到 k 个线程按序循环打印 1,2,3,...,k,1,2,3,... 怎么办？

🎯 **你**：信号量数组泛化：`sem[i]` 初始 `sem[0]=1`、其余为 0；线程 i 打完后 `release(sem[(i+1) % k])`。条件变量版则把 `isFoo` 换成 `turn` 计数器，等待条件是 `turn != 我的编号`。Go 版最省事：k 个 channel 围成环，令牌绕圈传。

> 💬 **面试官**：为什么 `wait` 要放在 `while` 里而不是 `if`？

🎯 **你**：三个原因：
> 1. **虚假唤醒**：JVM/OS 允许没有 notify 就返回，JLS 白纸黑字写了必须用 while 防御
> 2. **信号被偷**：`notify_all` 唤醒两人，后醒的必须重新检查条件，否则会双打印
> 3. **条件已变更**：唤醒你时轮次可能已经转过一轮了
> 一句话：**"醒来后重新验证条件"是并发编程的军规**。

> 💬 **面试官**：`notify` 和 `notify_all` 怎么选？惊群问题呢？

🎯 **你**：等待者是**同类线程竞争同一资源**且只有一个能走 → `notify`（减少惊群）；像本题等待条件互斥、唤醒后靠 while 过滤 → `notify_all` 更安全。惊群的代价是多次无效唤醒 + 重新抢锁，竞争不激烈时可忽略，竞争激烈用**分段锁/条件队列**隔离等待者。

> 💬 **面试官**：这个题的同步模式和分布式锁有什么关系？

🎯 **你**：**同构关系**。条件变量 = "本地等待-通知"，分布式锁的 ZK watch / etcd watch 就是它的分布式版——"锁被释放时通知下一个等待者"和 `notify` 一模一样。而信号量的"许可证"思想就是 Redis 锁 SET NX 的语义：**拿到许可证（key 不存在）才能进临界区**。本地锁管线程，分布式锁管进程/实例，设计哲学一脉相承。——正好引出今天的主题 👇

---

## 面试技巧

### 分布式锁设计与实现

> 单机的 `synchronized` / `ReentrantLock` 只能锁住一个进程里的线程。服务一上多实例（水平扩容、微服务），本地锁就失效了——**同一个定时任务在 3 台机器上同时跑，库存被扣成负数**。分布式锁就是解决"跨进程的互斥"问题。

---

### 1. 分布式锁的硬性要求（先说标准，再谈实现）

| 要求 | 含义 | 不达标会怎样 |
|---|---|---|
| **互斥** | 任意时刻只有一个客户端持有锁 | 锁了个寂寞，超卖/重复执行 |
| **防死锁** | 持有者崩溃，锁最终能释放 | 全系统卡死（必须 TTL / 会话自动过期） |
| **容错** | 锁服务部分节点故障仍可工作 | 锁服务成了新单点 |
| **谁加谁删** | 只能释放自己持有的锁 | A 删了 B 的锁，互斥被破坏 |
| （加分项）**可重入** | 同一线程可多次加锁 | 自己把自己锁死 |
| （加分项）**自动续约** | 业务没完，锁不过期 | 任务跑到一半锁没了 |
| （加分项）**fencing** | 给每次持锁发单调递增编号 | 过期锁"复活"后写脏数据 |

> 🔥 **面试一句话**："评价一把分布式锁，我先看三个硬指标——**互斥、防死锁、容错**；再看工程加分项——**谁加谁删、看门狗续约、fencing token**。后面所有实现方案都是围绕这张打分表展开的。"

---

### 2. 三大实现方案全景对比

| 维度 | 数据库 | Redis | Zookeeper | etcd |
|---|---|---|---|---|
| 实现方式 | 唯一索引 / `for update` | `SET NX EX` + Lua | 临时顺序节点 + watch | lease + revision |
| 一致性模型 | 取决于 DB（主从异步则弱） | AP（主从复制有损） | **CP** | **CP**（Raft） |
| 防死锁 | 超时字段轮询删 | TTL 过期 | **会话断开自动删** | **lease 到期自动删** |
| 释放通知 | 轮询（慢） | 轮询 / Pub-Sub | **watch 事件**（快） | **watch 事件**（快） |
| 性能 | 差 | **最好**（10w+ QPS） | 中等 | 好 |
| 典型封装 | 手写 | Redisson | Curator | clientv3 concurrency |
| 适用 | 无中间件兜底 | 高性能、可容忍小概率失效 | 强一致不容双持 | K8s 生态、强一致 |

---

### 3. Redis 分布式锁的四代演进（面试必讲的完整故事）

#### v1：`SETNX` —— 死锁之城

```redis
SETNX lock:order 1
# 业务处理...
DEL lock:order
```

**致命伤**：客户端拿到锁后**进程崩溃 / 宕机 / 忘了 DEL**，锁永久不释放——死锁。所有人排队等一个永远不会开的门。

#### v2：`SET NX EX` + 唯一 value + Lua 原子删 —— 解决误删

```redis
SET lock:order uuid-线程ID NX EX 30   # 原子完成：不存在才设置 + 30s 过期
```

为什么 value 要唯一？经典**误删时间线**：

```
1. 客户端 A 拿到锁，TTL 30s
2. A 业务卡顿（GC / 网络抖动），锁 30s 后自动过期
3. 客户端 B 趁机拿到锁
4. A 终于缓过来，执行 finally 里的 DEL —— 把 B 的锁删了！
5. 客户端 C 又进来……互斥从此形同虚设
```

**解法**：DEL 前先判断 value 是不是自己的，且**判断+删除必须原子**（Lua 脚本），否则判断完到删除之间锁刚好过期，又误删：

```lua
-- 原子「判断 + 删除」
if redis.call("get", KEYS[1]) == ARGV[1] then
    return redis.call("del", KEYS[1])
else
    return 0
end
```

#### v3：看门狗（Watchdog）—— 解决"业务没跑完锁就过期"

锁 TTL 设多长都尴尬：设短了业务没完锁失效（双持），设长了持有者崩溃后恢复期变长（死锁窗口大）。

**Redisson 看门狗方案**：加锁成功后启动后台线程，**每 TTL/3 续约一次**（比如 TTL 30s，每 10s 续一次）；客户端崩溃 → 续约线程死了 → TTL 到期自然释放。锁"租约"随业务生命周期伸缩，两全。

#### v4：Redlock —— 解决主从切换丢锁

即使单机 Redis 锁再完美，也敌不过**主从异步复制**：

```
1. 客户端 A 在 master 拿到锁
2. master 还没把锁同步给 slave 就宕机了
3. slave 升主，锁丢了！客户端 B 在新 master 又拿到同一把锁
4. A 和 B 同时持锁
```

**Redlock 算法**（Antirez 提出，向 N 个**独立** master 节点申请）：

```
1. 向 5 个互相独立的 Redis master 顺序申请同一把锁
   - 每个申请设置较短 TTL（如 50ms）和唯一 value
   - 每个请求都记耗时，超时立即放弃该节点
2. 在大多数节点（≥3/5）申请成功，且总耗时 < TTL
   → 加锁成功；有效持锁时间 = TTL - 总耗时
3. 否则向所有节点发起释放，过一段随机时间后重试
```

> 💡 核心理念：**不把命运押在单个 master 上，用多数派对抗单点故障**——和 Paxos/Raft 的多数派思想一脉相承（呼应 Day 101-103）。

**但 Redlock 有争议**：Martin Kleppmann（《DDIA》作者）撰文质疑——GC 停顿、网络延迟、时钟回拨都可能让"多数派成功"的客户端实际已经死了，锁照样被双持。**Antirez 反驳，社区至今无定论**。

> 🔥 **面试高分答法**（争议的收尾）："Redlock 的争论本质是**时钟与停顿不可控**。工程共识是：**对正确性要求极高的场景**（资金、库存）别赌 Redlock，用 CP 系统（ZK/etcd）+ fencing token；**普通场景**（防重、任务调度、幂等）单实例 Redis + 看门狗足够，真出问题概率远小于业务本身 bug。"

---

### 4. Zookeeper 分布式锁：临时顺序节点（CP 标杆）

**为什么 ZK 适合做锁**：ZK 是 CP 系统（ZAB 协议，类 Paxos），数据强一致；且它有**会话**和**临时节点**两大神器。

**加锁流程**：

```
1. 客户端在 /locks/order 下创建临时顺序节点：
   /locks/order/lock-00000001
   /locks/order/lock-00000002  （后来的）
2. 判断自己是不是**序号最小**的节点：
   - 是 → 拿到锁 ✅
   - 否 → watch 自己**前一个**节点（不是最小的那个！）
3. 前一个节点删除（释放锁 / 会话断开）→ 收到 watch 事件
4. 重新判断自己是否最小 → 是则持锁
```

**为什么 watch 前一个节点而不是最小节点？——羊群效应**

如果所有等待者都 watch 最小节点：锁一释放，**全部等待者同时被唤醒**去抢锁，瞬间打爆 ZK（惊群的分布式版）。只 watch 前驱节点，则**每次只唤醒一个**——也就是下一个顺位者，排队秩序天然公平。

**临时节点 = 天然防死锁**：客户端和 ZK 的会话断开（崩溃/断网），临时节点自动删除，锁自动释放——不需要 TTL 兜底，不会出现"持有者死了锁还在"的死锁。

> 💡 对应到今天的算法题：ZK 的 **watch = 分布式条件变量的 notify**，临时顺序节点 = **带排队号的信号量**。

---

### 5. etcd 分布式锁：lease + revision（现代 CP 方案）

etcd 与 ZK 思路同构，但基于 Raft，K8s 生态原生：

- **创建带 lease 的 key**：lease 有 TTL，客户端周期性 `KeepAlive` 续约（= ZK 心跳 + 看门狗二合一）；崩溃 → lease 到期 → key 消失 → 锁释放
- **revision 决定归属**：每个写入都有全局递增 revision，**创建成功的节点中 revision 最小者持锁**；其余节点 watch 自己前驱的 key
- **公平**：revision 顺序 = 申请顺序，先到先得

```go
// clientv3 官方并发包，几行搞定
sess, _ := concurrency.NewSession(cli, concurrency.WithTTL(30))
m := concurrency.NewMutex(sess, "/locks/order")
m.Lock(ctx)   // 等待 + 竞争 revision 最小
defer m.Unlock(ctx)
```

---

### 6. Fencing Token：给锁加"防伪编号"

分布式锁最刁钻的漏洞是**"锁过期了，但持有者没死"**——GC 停顿几十秒、线程卡死，锁早就易主，老客户端复活后照样写数据。

**Fencing Token 方案**（Martin Kleppmann 提出）：

```
1. 锁服务每次发锁时，附带一个单调递增的 fencing token（如 38, 39, 40...）
2. 客户端访问共享资源（DB / 存储）时必须带上 token
3. 存储端记住见过的最大 token，拒绝比它小的写入
```

```
时间线：
- 客户端 A 持锁（token=38），GC 停顿 60s
- 锁过期，B 拿到锁（token=39），正常写入，存储端记下 max=39
- A 复活，带着 token=38 来写入 → 存储端发现 38 < 39，直接拒绝 ✅
```

> 🔥 **面试一句话**："fencing token 把'锁的互斥'转移成'存储端的数据校验'——即使锁服务判断错了谁是合法持有者，**最后一道防线在数据层**，旧 token 永远写不进去。"

---

### 7. 可重入与公平性（加分项）

- **可重入**：同一线程重复加锁不能死锁自己。Redis 实现：不用 string 用 **hash** —— `field = 线程标识（uuid+threadId），value = 重入计数`，加锁 `HINCRBY`、解锁减到 0 才 DEL（Redisson 的 `RLock` 就是这么做的）
- **公平锁**：先到先得。ZK/etcd 的顺序节点天然公平；Redis 锁不保证（靠撞运气抢），要公平得额外维护等待队列（Redisson 的 `FairLock`）

---

### 8. 面试决策树：到底选哪个？

```
Q1: 场景对正确性要求多高？
├─ 资金/库存/核心数据，绝不能双持 → CP 方案
│   ├─ 已有 ZK（老 Hadoop 系技术栈）→ ZK 临时顺序节点 + Curator
│   ├─ K8s / Go 生态 → etcd lease + clientv3
│   └─ 终极保险：CP 锁 + fencing token + 存储端校验
└─ 防重复执行 / 定时任务 / 幂等，允许极小概率失效 → Redis
    ├─ 有 Redisson → SET NX EX + Watchdog + Lua 删（生产标配）
    └─ 裸 Redis → SET NX EX 30 + 唯一 value + Lua 原子删

Q2: 什么中间件都没有？
└─ MySQL 兜底：唯一索引 insert（竞争失败=没拿到锁）
    + 超时字段轮询清理 + 重试退避（性能差，应急用）
```

---

### 9. 高频面试连环问

> 💬 **面试官**：Redis 锁为什么不用 `SETNX + EXPIRE` 两条命令？

🎯 **你**：**原子性问题**。`SETNX` 成功后客户端崩溃，`EXPIRE` 没执行上 → 死锁。必须用 `SET key value NX EX 30` **一条命令**完成加锁+过期。原子性是分布式锁的生命线，所有操作（判断+删除、判断+续约）都必须原子——Lua 或单命令。

> 💬 **面试官**：为什么解锁要用 Lua 脚本？`get` 判断一下再 `del` 不行吗？

🎯 **你**：`get` 和 `del` 之间锁可能刚好过期又被别人拿到——**判断时是自己的，删除时已经是别人的**。把两步合成 Lua 脚本交给 Redis 单线程原子执行，才能闭环。

> 💬 **面试官**：Redis 主从架构下分布式锁安全吗？

🎯 **你**：不安全，主从异步复制会丢锁（master 加锁后宕机未同步）。两个出路：**Redlock**（多 master 多数派，但有争议）；或者**承认风险、换 CP 系统**（ZK/etcd）。选型看场景容忍度。

> 💬 **面试官**：ZK 锁和 Redis 锁性能差多少？为什么？

🎯 **你**：Redis 是内存 + 单线程 + 简单命令，单次操作微秒级，10w QPS 轻松；ZK 每次创建节点要 ZAB 写日志+多数派同步，还要维持会话心跳，单次毫秒级。差一个数量级往上。**但 ZK 买的是强一致**——和 CAP 呼应：你要 CP 就得付延迟（呼应 Day 102）。

> 💬 **面试官**：有了分布式锁还需要幂等设计吗？

🎯 **你**：**需要，锁是减概率不是兜底**。锁可能过期、可能误删、可能服务重启后重放。幂等（唯一键去重 / 状态机校验 / 请求指纹）是第二道防线，分布式锁是第一道。大厂标准答案：**幂等是必做的，锁是优化**。

> 💬 **面试官**：设计一个跨机房可用的分布式锁？

🎯 **你**：跨机房 = 网络分区高发，CP 锁在分区时直接不可用（多数派凑不齐），AP 锁则可能双持。务实方案：
> 1. 每个机房本地 CP 锁（etcd/ZK）管本地资源
> 2. 全局资源用**单主多从 + fencing token**，接受分区期间全局锁短暂不可用
> 3. 更激进的：放弃全局互斥，改**冲突检测 + CRDT 合并**（ Day 106 的副本一致性正好接上）

---

### 10. 自检验查清单一分钟版

面试前快速过一遍，能答上 6/8 就稳了：

- [ ] 分布式锁三个硬性要求？（互斥 / 防死锁 / 容错）
- [ ] Redis 锁为什么要 `SET NX EX` 一条命令？（SETNX+EXPIRE 非原子会死锁）
- [ ] 误删锁的完整时间线？（A 过期 → B 持锁 → A 复活 DEL）
- [ ] 看门狗干嘛的？（后台线程每 TTL/3 续约）
- [ ] Redlock 算法流程？（N 节点多数派 + 有效时间 = TTL - 耗时）
- [ ] Redlock 争议双方观点？（Kleppmann：时钟/GC 不可控；Antirez：重试+TTL 可接受）
- [ ] ZK 为什么 watch 前驱节点？（避免羊群效应惊群）
- [ ] fencing token 解决什么问题？（过期锁复活后写脏数据）

---

## 参考资源

- [Redis 官方分布式锁文档（含 Redlock）](https://redis.io/docs/latest/develop/use/patterns/distributed-locks/)
- [Martin Kleppmann: How to do distributed locking](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html) — Redlock 质疑原文，面试引用加分
- [Antirez: Is Redlock safe? 反驳](http://antirez.com/news/101) — 看双方辩论全貌
- [ZooKeeper Recipes: Locks](https://zookeeper.apache.org/doc/current/recipes.html#sc_recipes_Locks) — 官方锁配方
- [etcd clientv3 concurrency 文档](https://pkg.go.dev/go.etcd.io/etcd/client/v3@v3.5.0/concurrency)
- [Redisson 官方文档（Watchdog 机制）](https://github.com/redisson/redisson/wiki/8.-distributed-locks-and-synchronizers)
- 昨日内容：[Day 104 — 01 矩阵 + Gossip 协议与成员发现](./2026-09-16-01-matrix-gossip.md)
- 明日预告：**副本一致性与冲突解决（CRDT / OT）** 🔜

---

> 🎯 **今日金句**：*"本地锁管的是线程间的秩序，分布式锁管的是进程间的秩序——而所有分布式锁方案，本质上都在回答同一个问题：'持有者死了，锁怎么办？' TTL、临时节点、lease、看门狗……答案千奇百怪，但评分标准只有一个：互斥、防死锁、容错。"*
