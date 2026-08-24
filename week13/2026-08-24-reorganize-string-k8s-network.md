# Day 81 — 重构字符串 + K8s 网络模型与 CNI 插件深入

> **日期**: 2026-08-24  
> **周主题**: Week 13 — 云原生与容器化基础设施 🐳  
> **难度**: 🟡 Medium  
> **标签**: `贪心` `最大堆` `字符串` `计数`

---

## 📋 今日算法题：重构字符串 (Reorganize String)

**LeetCode 767**: [Reorganize String](https://leetcode.com/problems/reorganize-string/)

### 题目描述

给定一个字符串 `s`，检查是否能重新排布其中的字母，使得两相邻的字符不同。若可行，输出任意可行的结果；若不可行，返回 `""`。

**示例 1**：
```
输入: s = "aab"
输出: "aba"
```

**示例 2**：
```
输入: s = "aaab"
输出: ""
解释: 字母 'a' 出现了 3 次，字符串总长 4，无法满足相邻不同
```

**约束**：
- `1 <= s.length <= 500`
- `s` 只包含小写字母

---

### 🧠 解题思路

这道题和昨天的「任务调度器」是**亲兄弟**——核心都是**贪心：出现次数最多的字符优先安排**，避免同类字符相邻。

#### 核心洞察：什么时候一定无法重构？

设某字符出现次数为 `maxCount`，字符串总长度为 `n`。

如果这个字符出现次数超过半数（向上取整），那它一定会**无处可放**：

```
字符串: a a a b
         ↑ ↑ ↑
         3 个 a，长度 4
         需要放在位置 0, 2（或 1, 3）— 只能放 2 个！
         第 3 个 a 无处可去 → 不可能
```

**不可能条件**：`maxCount > (n + 1) / 2`

```
验证:
n=4, (4+1)/2 = 2, maxCount=3 > 2 → 不可能 ✓
n=5, (5+1)/2 = 3, maxCount=3 <= 3 → 可能（如 abaca）
```

#### 贪心策略：每次选剩余最多的字符（但不能和上一个相同）

**最大堆思路**：
1. 统计每个字符出现次数
2. 按次数放入最大堆
3. 每次取堆顶（剩余最多的字符），追加到结果
4. **关键**：刚用过的字符不能立刻再用，先暂存，下一轮再放入堆

```
初始: {a:3, b:2}
堆: [(3,a), (2,b)]

Step 1: 取 a(3), 结果="a", 暂存 a(2)
        堆: [(2,b)]
        放回暂存: 堆→[(2,a), (2,b)]

Step 2: 取 a(2), 结果="aa", 暂存 a(1)  
        堆: [(2,b)]
        放回暂存: 堆→[(2,b), (1,a)]

Step 3: 取 b(2), 结果="aab", 暂存 b(1)
        堆: [(1,a)]
        放回暂存: 堆→[(1,a), (1,b)]

Step 4: 取 a(1), 结果="aaba", 暂存 a(0) — 0次不放回
        堆: [(1,b)]

Step 5: 取 b(1), 结果="aabab"

✅ 完成！"aabab" 或 "ababa"
```

#### 更优的 O(N) 解法：奇偶位置分治

如果题目只要求判断是否能重构，最大堆够用了。但如果想写得更优雅，可以用**奇偶位置放置**：

1. 找出出现次数最多的字符
2. **先把它放在偶数位置**（0, 2, 4, ...），这样能最大化间隔
3. 其他字符依次填充剩余的偶数位置，然后填奇数位置

```
s = "aaabbc", n=6, maxCount=3 (字符 'a')

偶数位置: 0 _ 2 _ 4 _
先放 a:   a _ a _ a _

剩余偶数位已满（3个偶数位放了3个a），其他放奇数位:
奇数位置: _ b _ b _ c

结果: a b a b a c → "ababac" ✅
```

这个方法的精妙之处：
- 出现最多的字符优先占**偶数位置**，天然最大化间隔
- 如果最多的字符都放不下，说明不可能（提前判断）
- 其余字符随便填，不会冲突

---

### 💻 代码实现

#### Go 实现：最大堆版本（面试推荐，展示数据结构功底）

```go
package main

import (
	"container/heap"
	"fmt"
)

// CharCount 用于最大堆
type CharCount struct {
	ch    byte
	count int
}

type MaxHeap []CharCount

func (h MaxHeap) Len() int           { return len(h) }
func (h MaxHeap) Less(i, j int) bool { return h[i].count > h[j].count } // 最大堆
func (h MaxHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *MaxHeap) Push(x interface{}) {
	*h = append(*h, x.(CharCount))
}
func (h *MaxHeap) Pop() interface{} {
	old := *h
	n := len(old)
	x := old[n-1]
	*h = old[:n-1]
	return x
}

func reorganizeString(s string) string {
	n := len(s)
	if n <= 1 {
		return s
	}

	// 统计频率
	count := make([]int, 26)
	maxCount := 0
	for i := 0; i < n; i++ {
		idx := s[i] - 'a'
		count[idx]++
		if count[idx] > maxCount {
			maxCount = count[idx]
		}
	}

	// 不可能情况：最多的字符超过半数
	if maxCount > (n+1)/2 {
		return ""
	}

	// 构建最大堆
	h := &MaxHeap{}
	heap.Init(h)
	for i := 0; i < 26; i++ {
		if count[i] > 0 {
			heap.Push(h, CharCount{ch: byte('a' + i), count: count[i]})
		}
	}

	// 贪心：每次取最多剩余的字符，但不能和上一个相同
	var res []byte
	var prev CharCount // 上一轮被"冻结"的字符

	for h.Len() > 0 {
		// 取当前最多的字符
		curr := heap.Pop(h).(CharCount)
		res = append(res, curr.ch)
		curr.count--

		// 上一轮冻结的字符现在可以放回堆了
		if prev.count > 0 {
			heap.Push(h, prev)
		}

		// 当前字符这一轮不能用，冻结到下一轮
		prev = curr
	}

	return string(res)
}

func main() {
	fmt.Println(reorganizeString("aab"))     // "aba" 或 "baa"
	fmt.Println(reorganizeString("aaab"))    // ""
	fmt.Println(reorganizeString("vvvlo"))   // "vlvov"
}
```

#### Go 实现：奇偶位置分治（O(N) 最优解）

```go
func reorganizeStringLinear(s string) string {
	n := len(s)
	if n <= 1 {
		return s
	}

	// 统计频率，找出最频繁的字符
	count := make([]int, 26)
	maxCount, maxIdx := 0, 0
	for i := 0; i < n; i++ {
		idx := s[i] - 'a'
		count[idx]++
		if count[idx] > maxCount {
			maxCount = count[idx]
			maxIdx = idx
		}
	}

	// 不可能情况
	if maxCount > (n+1)/2 {
		return ""
	}

	res := make([]byte, n)
	idx := 0

	// Step 1: 把最频繁的字符放偶数位置
	for count[maxIdx] > 0 {
		res[idx] = byte('a' + maxIdx)
		idx += 2
		count[maxIdx]--
	}

	// Step 2: 其他字符继续填充偶数位置，然后填奇数位置
	for i := 0; i < 26; i++ {
		for count[i] > 0 {
			if idx >= n {
				idx = 1 // 偶数位置满了，切换到奇数位置
			}
			res[idx] = byte('a' + i)
			idx += 2
			count[i]--
		}
	}

	return string(res)
}
```

#### Python 实现（简洁版）

```python
import heapq
from collections import Counter

class Solution:
    def reorganizeString(self, s: str) -> str:
        count = Counter(s)
        max_count = max(count.values())
        
        if max_count > (len(s) + 1) // 2:
            return ""
        
        # 最大堆（Python 最小堆，存负数）
        heap = [(-cnt, ch) for ch, cnt in count.items()]
        heapq.heapify(heap)
        
        res = []
        prev = (0, '')  # 上一轮冻结的字符
        
        while heap:
            cnt, ch = heapq.heappop(heap)
            res.append(ch)
            
            # 上一轮的字符可以放回堆了
            if prev[0] < 0:
                heapq.heappush(heap, prev)
            
            # 当前字符冻结到下一轮
            prev = (cnt + 1, ch)  # cnt 是负数，+1 等于减1
        
        return ''.join(res)
```

---

### ⏱️ 复杂度分析

| 解法 | 时间复杂度 | 空间复杂度 | 说明 |
|---|---|---|---|
| **最大堆** | O(N log K) | O(K) | K 为不同字符数，每次堆操作 O(log K) |
| **奇偶位置** | **O(N)** | O(1) | 最优解，两次线性扫描 |

> 💡 **面试建议**：先写最大堆版本（好理解、能展示堆的功底），如果时间充裕再提奇偶位置 O(N) 优化。面试官通常会认可你对更优解的了解。

---

### 🎯 面试官追问（高频）

**Q1: 怎么判断一定无法重构？**  
> 最频繁的字符出现次数 `maxCount > (n + 1) / 2` 时不可能。直观理解：即使把它尽量分散（每隔一个位置放一个），位置也不够。

**Q2: 如果只问"能不能"，不要求构造结果呢？**  
> 只需要统计频率并判断 `maxCount > (n + 1) / 2`，O(N) 时间，O(1) 空间。

**Q3: 如果要求所有字符间隔至少为 k 呢？（类似任务调度器）**  
> 这就是 LeetCode 358 "重排字符串 k 距离间隔"。用最大堆 + 冷却队列：每次取堆顶，执行后入队并标记冷却结束时间，时间复杂度 O(N log K)。

**Q4: 如果字符集不是小写字母，而是 Unicode 呢？**  
> 用 `map[rune]int` 代替固定数组，其他逻辑完全一样。Go 中注意 `range` over string 得到的是 rune。

---

### 🔗 相关题目

| 题目 | 难度 | 关联点 |
|---|---|---|
| [767. 重构字符串](https://leetcode.com/problems/reorganize-string/) | Medium | 本题 |
| [621. 任务调度器](https://leetcode.com/problems/task-scheduler/) | Medium | 昨天刚做，冷却间隔调度 |
| [358. 重排字符串 k 距离间隔](https://leetcode.com/problems/rearrange-string-k-distance-apart/) | Medium | 间隔要求为 k 的扩展版 |
| [1405. 最长快乐字符串](https://leetcode.com/problems/longest-happy-string/) | Medium | 类似贪心，每次选剩余最多的（但不能连续三个相同） |

---

## 🎤 面试技巧：K8s 网络模型与 CNI 插件深入

### 一、K8s 网络设计哲学

K8s 对网络有一个**强约束**：每个 Pod 必须有独立的 IP，且**所有 Pod 之间可以不经过 NAT 直接通信**。

```
┌──────────────────────────────────────────────────────────────┐
│                         宿主机 Node                           │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                   │
│  │  Pod A   │  │  Pod B   │  │  Pod C   │  ...              │
│  │10.244.1.2│  │10.244.1.3│  │10.244.1.4│                   │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘                   │
│       │             │             │                          │
│       └─────────────┴─────────────┘                          │
│                   veth pair                                  │
│       ┌─────────────────────────────┐                        │
│       │      cbr0 / docker0         │  ← 网桥，同一节点 Pod 互通 │
│       │     (Linux Bridge)          │                        │
│       └─────────────┬───────────────┘                        │
│                     │ eth0                                   │
│  10.244.1.0/24  Node IP: 192.168.1.10                       │
└──────────────────────────────────────────────────────────────┘
```

**K8s 网络三大要求**（面试必考）：

| 要求 | 含义 | 为什么重要 |
|---|---|---|
| **Pod ↔ Pod** | 所有 Pod 可以直接通信，不经过 NAT | 微服务之间调用像本地调用一样简单 |
| **Pod ↔ Node** | Pod 可以和所在节点通信 | kubelet 健康检查、日志采集 |
| **Pod ↔ Service** | Pod 可以通过 ClusterIP 访问 Service | 服务发现的基础 |

> 💡 **面试金句**：K8s 的网络模型是"IP-per-Pod"，每个 Pod 有一个独立 IP，相当于一台独立虚拟机。这简化了服务发现和端口管理——不需要像 Docker 那样处理端口映射冲突。

---

### 二、Pod 网络通信的四种场景

#### 场景 1：同一节点内的 Pod 通信

```
Pod A (10.244.1.2) ←→ vethA ←→ cbr0 (网桥) ←→ vethB ←→ Pod B (10.244.1.3)

过程：
1. Pod A 发送数据包，目的 IP 是 Pod B
2. 数据包经过 veth pair 到达宿主机的网桥 cbr0
3. 网桥根据 MAC 地址表转发到 Pod B 的 veth 接口
4. 数据包进入 Pod B

本质：二层网络交换，纯 Linux Bridge，性能最高
```

#### 场景 2：跨节点的 Pod 通信（核心难点）

这是 K8s 网络最复杂的地方。不同节点的 Pod IP 段不同（如 Node1 是 10.244.1.0/24，Node2 是 10.244.2.0/24），需要** overlay 网络**或**底层网络路由**来解决。

```
Pod A (10.244.1.2) on Node1
        │
        ▼
┌───────────────┐         ┌───────────────┐
│   Node 1      │ ───────→│   Node 2      │
│  192.168.1.10 │  物理网  │  192.168.1.11 │
│  10.244.1.0/24│  络连通  │  10.244.2.0/24│
└───────────────┘         └───────────────┘
                                  │
                                  ▼
                          Pod B (10.244.2.3)

关键问题：Node1 怎么知道 10.244.2.3 在 Node2 上？
→ CNI 插件负责维护路由规则或封装隧道
```

**两种实现方式**：

| 方式 | 原理 | 代表插件 | 特点 |
|---|---|---|---|
| **Overlay** | 数据包封装在 UDP/VXLAN 中传输 | Flannel(VXLAN)、Calico(IPIP) | 适应性强，有封装开销 |
| **Underlay** | 直接路由，依赖底层网络 | Calico(BGP)、MACVLAN | 性能好，需要网络设备配合 |

#### 场景 3：Pod 访问 Service

```
Pod A 访问 my-service:80
        │
        ▼
┌─────────────────────────────────────┐
│         Service (ClusterIP)          │
│     10.96.123.45:80 (虚拟 IP)        │
│          │                          │
│    ┌─────┴─────┐                    │
│    ▼           ▼                    │
│ Pod B       Pod C                   │
│ 10.244.1.3  10.244.2.5              │
└─────────────────────────────────────┘

实现方式（kube-proxy 的三种模式）：
```

**kube-proxy 三种模式对比**（面试高频）：

| 模式 | 原理 | 性能 | 适用场景 |
|---|---|---|---|
| **userspace** | kube-proxy 作为用户态代理，转发所有流量 | 差（用户态拷贝） | 已废弃 |
| **iptables** | 使用 iptables DNAT 规则做随机负载均衡 | 中等（内核态，但规则遍历 O(n)） | 中小规模，默认模式 |
| **ipvs** | 使用 IPVS 内核模块，基于哈希表做负载均衡 | 高（O(1) 查找，支持更多算法） | 大规模集群，生产推荐 |

```bash
# 查看当前 kube-proxy 模式
kubectl get configmap kube-proxy -n kube-system -o yaml | grep mode

# 查看 IPVS 规则（如果是 ipvs 模式）
ipvsadm -Ln
```

> 💡 **面试金句**：iptables 模式在 Service 和 Pod 数量增多时，规则链线性增长，性能下降。IPVS 基于内核哈希表，查找复杂度 O(1)，支持 rr/wlc/lc 等负载均衡算法，是大规模集群的首选。

#### 场景 4：Pod 访问外部网络

```
Pod (10.244.1.2) → cbr0 → eth0 → iptables MASQUERADE → 外部网络

K8s 通过 iptables 做 SNAT：
-A POSTROUTING -s 10.244.0.0/16 ! -d 10.244.0.0/16 -j MASQUERADE

Pod IP 是私网地址，出公网需要 NAT 转换
```

---

### 三、CNI 插件详解

CNI（Container Network Interface）是 K8s 的**网络插件标准接口**。K8s 只定义了规范，具体实现由各插件负责。

```
Kubelet 创建 Pod
     │
     ▼
调用 CNI 插件 (exec /opt/cni/bin/xxx)
     │
     ├─ 分配 IP（从 IPAM）
     ├─ 创建 veth pair
     ├─ 配置网桥 / 路由 / 防火墙
     └─ 返回 IP 配置给 Kubelet
```

#### 主流 CNI 插件对比

| 插件 | 网络模型 | 性能 | 网络策略 | 适用场景 |
|---|---|---|---|---|
| **Flannel** | VXLAN / UDP overlay | 中 | ❌ 不支持 | 中小集群，简单场景，快速上手 |
| **Calico** | BGP 路由 / IPIP overlay | 高 | ✅ NetworkPolicy | 大规模生产，需要网络安全策略 |
| **Cilium** | eBPF 内核可编程 | 极高 | ✅ L3-L7 策略 | 云原生高级场景，observability |
| **Weave** | VXLAN overlay | 中 | ✅ 支持 | 简单多主机组网 |
| **Antrea** | OVS / Geneve | 高 | ✅ NetworkPolicy | VMware 生态，NSX-T 集成 |

#### 1. Flannel（最简单，适合入门）

```
Flannel 架构:
┌─────────────┐      ┌─────────────┐
│  Node 1     │◄────►│  Node 2     │
│ 10.244.1.0/24│ VXLAN│ 10.244.2.0/24│
│ flannel.1   │ 隧道  │ flannel.1   │
│  VTEP设备   │      │  VTEP设备   │
└─────────────┘      └─────────────┘

数据包流转:
Pod A → cni0 → flannel.1 → UDP 4789 → flannel.1 → cni0 → Pod B
         (Node1)         (VXLAN封装)          (Node2)

关键组件:
- flanneld: DaemonSet，每个节点一个，负责维护路由和 ARP 表
- etcd: 存储子网分配信息（哪个节点用哪个 IP 段）
```

**Flannel 后端模式**：

| 后端 | 原理 | 性能 | 依赖 |
|---|---|---|---|
| `vxlan` | VXLAN 封装，UDP 端口 4789 | 中 | 无 |
| `udp` | 用户态 UDP 封装（已废弃） | 差 | 无 |
| `host-gw` | 直接主机路由，不封装 | 高 | 节点二层互通 |
| `aws-vpc` / `gce` | 云厂商路由表 | 高 | 对应云平台 |

> ⚠️ Flannel 的局限：**不支持 NetworkPolicy**。如果需要 Pod 级别的网络隔离，必须配合其他方案（如 Calico）。

#### 2. Calico（生产首选，面试重点）

Calico 的独特之处：**默认使用 BGP 路由，而不是 overlay 封装**。

```
Calico BGP 模式（Underlay）:

         ┌─────────────┐
         │   Router    │ ← 物理路由器或 RR（Route Reflector）
         │  (iBGP)     │
         └──────┬──────┘
                │
    ┌───────────┼───────────┐
    ▼           ▼           ▼
┌───────┐  ┌───────┐  ┌───────┐
│ Node1 │  │ Node2 │  │ Node3 │
│BGP Peer│  │BGP Peer│  │BGP Peer│
│直接通告│  │直接通告│  │直接通告│
│Pod路由 │  │Pod路由 │  │Pod路由 │
└───────┘  └───────┘  └───────┘

每个节点作为一个 BGP Peer，把本节点 Pod 的路由通告给路由器。
其他节点学习到这个路由后，Pod 间通信直接走三层路由，无需封装。

优势：无封装开销，性能接近原生网络
前提：底层网络支持 BGP，或节点之间可以直接路由
```

**Calico 的两种模式**：

| 模式 | 原理 | 场景 |
|---|---|---|
| **BGP** | 纯三层路由，无封装 | 数据中心、云厂商 VPC、支持 BGP 的网络 |
| **IPIP/VXLAN** | 跨子网时封装隧道 | 节点不在同一二层网络（如跨 VPC、公网） |

**Calico 的核心组件**：

| 组件 | 作用 |
|---|---|
| **Felix** | 每个节点上的 agent，负责配置路由、ACL、IPAM |
| **BIRD** | BGP 守护进程，负责路由分发 |
| **etcd / Typha** | 存储 Calico 配置和状态 |
| **calico-kube-controller** | 监听 K8s API，同步 Pod/Namespace/NetworkPolicy |

**Calico NetworkPolicy**（Flannel 没有的杀手级特性）：

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

> 💡 **面试金句**：Calico 的 NetworkPolicy 基于 iptables 规则实现。Felix 监听 K8s API，当 NetworkPolicy 变化时，动态生成对应的 iptables 规则，实现 Pod 级别的防火墙。

#### 3. Cilium（未来趋势，eBPF 革命）

Cilium 基于 **eBPF（extended Berkeley Packet Filter）**，直接在 Linux 内核中执行网络策略，绕过 iptables。

```
传统方案 vs Cilium:

iptables 方案:                    eBPF 方案:
┌─────────┐    ┌─────────┐        ┌─────────┐
│  Pod    │───→│ iptables│───→    │  Pod    │───→┌───────┐
└─────────┘    │ (遍历规则)│        └─────────┘    │ eBPF  │───→ 直接转发
               └─────────┘                         │ (JIT) │
                                                   └───────┘
                    O(n) 规则匹配                      O(1) 哈希查找
                    用户态配置                         内核态执行
```

**Cilium 的优势**：
- **性能**：绕过 iptables，无规则遍历开销
- **可观测性**：eBPF 可以追踪每个连接的 L3-L7 信息
- **安全策略**：支持基于 HTTP 方法、Kafka topic 的细粒度策略
- **Service Mesh**：可替代 Istio 的数据面（sidecar-less）

> 💡 **面试加分项**：提到 Cilium 和 eBPF 会给面试官留下"紧跟技术趋势"的印象。可以说"我们正在评估用 Cilium 替代 Calico，以获得更好的可观测性和性能"。

---

### 四、高频面试题速查

#### Q1: K8s 中 Pod 是怎么拿到 IP 的？

> 1. Kubelet 创建 Pod 时，调用 CNI 插件（`/opt/cni/bin/xxx`）
> 2. CNI 插件从 IPAM（IP Address Management）分配一个 IP
> 3. 创建 veth pair，一端放入 Pod 的 network namespace，另一端连接到宿主机的网桥
> 4. 配置路由，使 Pod 可以访问集群网络和外部网络
> 5. 返回 IP 配置给 Kubelet，Kubelet 将 IP 写入 Pod status
>
> IPAM 可以是 host-local（每个节点预分配一个 CIDR 段）或 etcd 集中管理（如 Calico）。

#### Q2: Service 的 ClusterIP 是怎么实现的？为什么能负载均衡？

> ClusterIP 是一个**虚拟 IP**，不绑定任何网络接口。
>
> 实现依赖 kube-proxy：
> - **iptables 模式**：kube-proxy 监听 Service/Endpoint 变化，生成 iptables DNAT 规则。访问 ClusterIP 时，内核随机选择一个后端 Pod IP 做 DNAT。
> - **ipvs 模式**：kube-proxy 配置 IPVS 虚拟服务器规则，IPVS 在内核中维护一个服务表，用哈希算法快速选择后端。
>
> 负载均衡是**随机**的（iptables）或基于算法（ipvs 支持 rr/wrr/lc 等），不保证会话亲和。

#### Q3: 为什么 Flannel 不适合大规模集群？

> 1. **VXLAN 封装开销**：每个数据包增加 50 字节头部，降低有效载荷比例
> 2. **广播域问题**：VXLAN 依赖 UDP 单播或组播，大规模下 ARP 表膨胀
> 3. **不支持 NetworkPolicy**：没有网络安全隔离能力
> 4. **集中式 etcd**：子网分配信息存在 etcd，规模大了成为瓶颈
>
> 大规模集群（500+ 节点）通常用 Calico BGP 模式或 Cilium。

#### Q4: Calico 的 BGP 模式和 IPIP 模式有什么区别？什么时候切换？

> **BGP 模式**：直接三层路由，无封装，性能最好。要求节点之间可以直接路由（同一二层网络或路由器支持 BGP）。
>
> **IPIP 模式**：跨子网时，Pod 数据包封装在 IP-in-IP 隧道中传输。有封装开销，但适应性强。
>
> Calico 默认配置是 **BGP + IPIP 混合**：同一子网内用 BGP，跨子网自动切换 IPIP。

#### Q5: 什么是 NetworkPolicy？它是怎么实现的？

> NetworkPolicy 是 K8s 的**Pod 级别防火墙**，定义哪些 Pod 可以互相通信。
>
> 实现依赖 CNI 插件：
> - **Calico**：Felix 组件监听 NetworkPolicy 资源，生成 iptables 规则
> - **Cilium**：用 eBPF 程序在内核中执行策略，性能更好
>
> 默认情况下，K8s 是**全通**的（allow-all）。创建 NetworkPolicy 后，只有策略允许的流量才能通过。

#### Q6: DNS 在 K8s 中是怎么工作的？

> 1. K8s 集群内运行 CoreDNS Pod（通常 2 个副本保证高可用）
> 2. CoreDNS 作为集群 DNS 服务器，监听 Service/Endpoint 变化
> 3. 每个 Pod 的 `/etc/resolv.conf` 配置为 CoreDNS 的 ClusterIP
> 4. Service 创建后，CoreDNS 自动生成 DNS 记录：
>    - `my-service.my-namespace.svc.cluster.local` → ClusterIP
>    - `my-pod.my-namespace.pod.cluster.local` → Pod IP（可选开启）
> 5. Pod 访问 Service 时，通过 DNS 解析到 ClusterIP，再由 kube-proxy 负载均衡到后端 Pod

#### Q7: 怎么排查一个 Pod 访问不了 Service 的问题？

> 排查 checklist：
> 1. `kubectl get svc,ep` — 确认 Service 和 Endpoints 存在且正确
> 2. `kubectl describe svc` — 检查 selector 是否匹配 Pod label
> 3. `kubectl get pods -l app=xxx` — 确认后端 Pod 是 Running 且 Ready
> 4. `kubectl exec -it pod -- nslookup service-name` — DNS 解析是否正常
> 5. `kubectl exec -it pod -- curl cluster-ip:port` — 直接访问 ClusterIP
> 6. `kubectl logs -n kube-system kube-proxy-xxx` — kube-proxy 是否有错误
> 7. 节点上 `iptables -t nat -L KUBE-SERVICES` — 检查 NAT 规则是否存在
> 8. 节点上 `ipvsadm -Ln` — 如果是 ipvs 模式，检查虚拟服务器配置

---

### 五、网络排工具体系

```bash
# 1. 查看 Pod IP 和所在节点
kubectl get pod -o wide

# 2. 进入 Pod 网络 namespace 排查
kubectl debug -it pod-name --image=nicolaka/netshoot --target=container-name
# 或使用 nsenter
nsenter -t <pod-pid> -n ip addr

# 3. 查看节点路由表
ip route show

# 4. 查看网桥和 veth
brctl show  # 或 ip link show type bridge
ip link show type veth

# 5. 抓包
tcpdump -i cni0 -n host 10.244.1.2

# 6. 查看 iptables 规则
iptables -t nat -L KUBE-SERVICES -n --line-numbers

# 7. 查看 IPVS 规则（ipvs 模式）
ipvsadm -Ln

# 8. 查看 CNI 配置
cat /etc/cni/net.d/10-calico.conflist
```

---

## 📝 今日速记卡

### 算法速记

```
重构字符串可行性判断：
  maxCount > (n + 1) / 2 → 不可能

贪心策略：
  最大堆版：每次取剩余最多的（但不能和上一个相同）
  O(N) 版：最频繁字符放偶数位，其余填充剩余位置

本质：和任务调度器是同一类贪心 —— 最频繁的优先安排，避免相邻
```

### K8s 网络速记

```
K8s 网络三大要求：Pod↔Pod 直通、Pod↔Node 通、Pod↔Service 通

Pod 通信四场景：
  同节点 → Linux Bridge（二层交换）
  跨节点 → Overlay(VXLAN/IPIP) 或 Underlay(BGP)
  访问 Service → kube-proxy (iptables/ipvs)
  访问外部 → iptables MASQUERADE (SNAT)

CNI 插件：
  Flannel → 简单，VXLAN，无 NetworkPolicy
  Calico → BGP 路由，NetworkPolicy，生产首选
  Cilium → eBPF，高性能，L7 策略，未来趋势

kube-proxy 三模式：
  userspace（废弃）→ iptables（默认）→ ipvs（生产推荐）
```

---

> 📌 **明日预告**: Day 82 — 最长快乐字符串 + CI/CD 流水线设计与 GitOps
>
> 🎯 **本周目标**: 掌握 Docker/Kubernetes 核心原理，能在面试中画出架构图并讲清楚各组件协作流程。
