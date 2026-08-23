# Day 80 — 任务调度器 + Docker 与 Kubernetes 核心原理

> **日期**: 2026-08-23  
> **周主题**: Week 13 — 云原生与容器化基础设施 🐳  
> **难度**: 🟡 Medium  
> **标签**: `贪心` `哈希表` `数学` `调度算法`

---

## 📋 今日算法题：任务调度器 (Task Scheduler)

**LeetCode 621**: [Task Scheduler](https://leetcode.com/problems/task-scheduler/)

### 题目描述

给定一个用字符数组表示的任务 CPU 需要执行的任务列表，其中包含使用大写的 A - Z 字母表示的不同的任务。任务可以以任意顺序执行，并且每个任务都可以在 1 个单位时间内执行完。CPU 在任何一个单位时间内都可以执行一个任务，或者在待命状态。

然而，两个**相同种类**的任务之间必须有长度为 `n` 的冷却时间，因此至少有连续 `n` 个单位时间内 CPU 在执行不同的任务，或者在待命状态。

你需要计算完成所有任务所需要的**最短时间**。

**示例 1**：
```
输入: tasks = ["A","A","A","B","B","B"], n = 2
输出: 8
解释: A -> B -> (待命) -> A -> B -> (待命) -> A -> B
     在本例中，两个相同类型任务之间必须间隔长度为 n = 2 的冷却时间，
     而执行一个任务只需要一个单位时间，所以中间出现了待命状态。
```

**示例 2**：
```
输入: tasks = ["A","A","A","B","B","B"], n = 0
输出: 6
解释: 在这种情况下，任何大小为 6 的排列都可以，因为 n = 0 意味着无冷却限制
```

**示例 3**：
```
输入: tasks = ["A","A","A","A","A","A","B","C","D","E","F","G"], n = 2
输出: 16
解释: 一种可能的解决方案是:
     A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> (待命) -> (待命) -> A -> (待命) -> (待命) -> A
```

---

### 🧠 解题思路

这道题的本质是**贪心调度问题**，和操作系统中的进程调度、Kubernetes 中的 Pod 调度都有相似的贪心思想。

#### 核心洞察

出现次数最多的任务决定了调度的"骨架"。设出现次数最多的任务出现了 `maxCount` 次：

```
A _ _ A _ _ A _ _ A ...  (maxCount 个 A，中间至少 n 个间隔)
```

这个骨架至少需要 `(maxCount - 1) * (n + 1) + 1` 个时间单位。

#### 为什么是这个公式？

- 最频繁的任务有 `maxCount` 个，它们之间需要 `n` 个冷却间隔
- 所以这 `maxCount` 个任务形成 `maxCount - 1` 个"间隔段"
- 每个间隔段长度为 `n + 1`（1 个任务 + n 个冷却）
- 最后一个任务不需要后面的冷却，所以 `+1`

```
A _ _ _ A _ _ _ A
↑       ↑       ↑
└─ n+1 ─┘└─ n+1 ─┘

总长度 = (maxCount - 1) × (n + 1) + 最后一组的任务数
```

#### 两个约束条件

1. **冷却约束**：`(maxCount - 1) * (n + 1) + maxCountTasks`（最后一组可能有多个出现次数相同的任务）
2. **任务总量约束**：`tasks.length`（如果没有冷却限制，就是简单按顺序执行）

**最终答案 = max(冷却约束, 任务总量)**

#### 为什么需要取 max？

- 当冷却时间 `n` 很大时，冷却约束起主导作用，会出现待命状态
- 当任务种类很多时，即使不待命，不同的任务也足够填充冷却间隔，此时任务总量就是答案

---

### 💻 代码实现

#### Go 实现（推荐面试写）

```go
package main

import "fmt"

func leastInterval(tasks []byte, n int) int {
    // 统计每个任务的出现次数
    count := make([]int, 26)
    maxCount := 0
    for _, task := range tasks {
        idx := task - 'A'
        count[idx]++
        if count[idx] > maxCount {
            maxCount = count[idx]
        }
    }
    
    // 统计有多少个任务出现了 maxCount 次
    maxCountTasks := 0
    for _, c := range count {
        if c == maxCount {
            maxCountTasks++
        }
    }
    
    // 计算冷却约束下的最小时间
    // (maxCount - 1) 个完整间隔段，每段长度 (n + 1)
    // 最后一段有 maxCountTasks 个任务（并列最频繁的）
    partCount := maxCount - 1
    partLength := n + 1
    lastPartLength := maxCountTasks
    
    coolingConstraint := partCount*partLength + lastPartLength
    
    // 取冷却约束和任务总量的较大值
    if len(tasks) > coolingConstraint {
        return len(tasks)
    }
    return coolingConstraint
}

func main() {
    tasks := []byte{'A', 'A', 'A', 'B', 'B', 'B'}
    fmt.Println(leastInterval(tasks, 2)) // 输出: 8
    
    tasks2 := []byte{'A', 'A', 'A', 'B', 'B', 'B'}
    fmt.Println(leastInterval(tasks2, 0)) // 输出: 6
}
```

#### Python 实现（简洁版）

```python
from collections import Counter

class Solution:
    def leastInterval(self, tasks: list[str], n: int) -> int:
        count = Counter(tasks)
        max_count = max(count.values())
        max_count_tasks = sum(1 for v in count.values() if v == max_count)
        
        cooling = (max_count - 1) * (n + 1) + max_count_tasks
        return max(len(tasks), cooling)
```

#### Java 实现

```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] count = new int[26];
        int maxCount = 0;
        for (char task : tasks) {
            count[task - 'A']++;
            maxCount = Math.max(maxCount, count[task - 'A']);
        }
        
        int maxCountTasks = 0;
        for (int c : count) {
            if (c == maxCount) maxCountTasks++;
        }
        
        int cooling = (maxCount - 1) * (n + 1) + maxCountTasks;
        return Math.max(tasks.length, cooling);
    }
}
```

---

### ⏱️ 复杂度分析

| 维度 | 复杂度 | 说明 |
|---|---|---|
| **时间** | O(N) | N 为任务总数，只需遍历统计频率 |
| **空间** | O(1) | 固定 26 个字母的计数数组，常数空间 |

> 💡 如果任务种类不是固定的 26 个大写字母，而是 K 种任务，空间复杂度为 O(K)。

---

### 🎯 面试官追问（高频）

**Q1: 如果没有待命状态，只有冷却限制，公式还适用吗？**  
> 适用。公式本质是"最频繁任务决定骨架，其他任务填充间隙"。如果没有待命状态，说明任务种类足够多，此时 `len(tasks)` 会大于冷却约束，取 max 后就是任务总量。

**Q2: 如果每个任务的执行时间不同呢？**  
> 这是一个变体问题。需要改用优先队列（最大堆）贪心：每次选执行时间最长的任务，执行后放回队列并标记冷却结束时间。时间复杂度变为 O(N log K)。

**Q3: 这道题和操作系统调度有什么联系？**  
> 核心思想相似：最频繁任务优先调度（类似最高响应比优先），避免同类任务集中执行导致资源争抢。OS 中更复杂的调度器（如 CFS）还要考虑优先级、时间片、I/O 等待等因素。

**Q4: 如何用优先队列（最大堆）实现？**  
> 模拟过程：统计频率 → 入最大堆 → 每次取堆顶任务执行 → 执行后频率减 1，若仍大于 0 放入冷却队列 → 检查冷却队列中是否有任务可以恢复。时间 O(N log K)，空间 O(K)。

---

### 🔗 相关题目

| 题目 | 难度 | 关联点 |
|---|---|---|
| [621. 任务调度器](https://leetcode.com/problems/task-scheduler/) | Medium | 本题 |
| [358. 重排字符串](https://leetcode.com/problems/rearrange-string-k-distance-apart/) | Medium | 相似冷却间隔问题 |
| [767. 重构字符串](https://leetcode.com/problems/reorganize-string/) | Medium | 贪心 + 最大堆，字符不相邻 |
| [1405. 最长快乐字符串](https://leetcode.com/problems/longest-happy-string/) | Medium | 贪心选剩余最多的字符 |

---

## 🎤 面试技巧：Docker 与 Kubernetes 核心原理

### 一、Docker 核心原理（必考）

#### 1. 容器 vs 虚拟机

```
┌─────────────────────────────────────┐
│           虚拟机 (VM)                │
├─────────────────────────────────────┤
│  App A │ App B │ App C              │
├────────┴───────┴────────────────────┤
│  Guest OS │ Guest OS │ Guest OS     │
├───────────┴──────────┴──────────────┤
│           Hypervisor                │
├─────────────────────────────────────┤
│           Host OS                   │
├─────────────────────────────────────┤
│           Hardware                  │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│           容器 (Container)           │
├─────────────────────────────────────┤
│  App A │ App B │ App C              │
├────────┴───────┴────────────────────┤
│  Container Runtime (Docker Engine)  │
├─────────────────────────────────────┤
│           Host OS (Shared Kernel)   │
├─────────────────────────────────────┤
│           Hardware                  │
└─────────────────────────────────────┘
```

**核心区别**：
- VM：硬件级虚拟化，每个 VM 有独立内核，**重量级**，启动分钟级
- 容器：OS 级虚拟化，共享宿主机内核，**轻量级**，启动秒级
- 容器镜像比 VM 镜像小得多（MB vs GB）

#### 2. Docker 三大核心技术

| 技术 | 作用 | 面试一句话 |
|---|---|---|
| **Namespace** | 隔离（视图隔离） | PID/NET/IPC/MOUNT/UTS/USER 六类命名空间，让容器觉得自己在独立系统中 |
| **Cgroups** | 限制（资源管控） | 控制 CPU/内存/磁盘/网络的使用量，防止一个容器拖垮整台机器 |
| **UnionFS** | 存储（分层镜像） | 分层文件系统，只读层共享 + 可写容器层，镜像复用率高 |

**Namespace 详解**：
```bash
# 查看容器的 Namespace
ls -la /proc/<pid>/ns/

# 六大 Namespace
PID  - 进程 ID 隔离（容器内 PID 1 是独立 init）
NET  - 网络隔离（每个容器有自己的 eth0, IP, 路由表）
IPC  - 进程间通信隔离（容器间无法直接共享内存）
MOUNT - 挂载点隔离（容器的 /proc, /sys 是独立的）
UTS  - 主机名隔离（容器可以有自己的 hostname）
USER - 用户权限隔离（容器内 root 映射到宿主机普通用户）
```

**Cgroups 详解**：
```bash
# 查看容器的 Cgroups 限制
cat /sys/fs/cgroup/cpu/docker/<container_id>/cpu.cfs_quota_us
cat /sys/fs/cgroup/memory/docker/<container_id>/memory.limit_in_bytes

# 面试高频：如果容器 OOM 了，是 Cgroups 的 memory 限制触发的
# 不是容器内进程自己申请的，是宿主机内核杀的
```

**UnionFS（联合文件系统）**：
```
镜像层（只读，共享）:
  Layer 3: apt-get install nginx
  Layer 2: apt-get install python
  Layer 1: FROM ubuntu:20.04  (基础镜像)

容器层（读写，私有）:
  Container Layer: 运行时的所有写入
```

> 💡 **面试金句**：Docker 镜像是分层的，多个容器共享只读层，只有容器层是私有的。这就是为什么 `docker pull` 时已经存在的层不会重复下载。

#### 3. Dockerfile 优化要点

```dockerfile
# ❌ 不好的写法 — 每层都产生新镜像层
FROM ubuntu
RUN apt-get update
RUN apt-get install -y python
RUN apt-get install -y nginx
COPY . /app

# ✅ 好的写法 — 合并 RUN 指令，减少层数
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y \
    python3 \
    nginx \
    && rm -rf /var/lib/apt/lists/*
COPY . /app
WORKDIR /app
CMD ["python3", "app.py"]
```

**Dockerfile 最佳实践（面试常问）**：
1. **用多阶段构建**（Multi-stage）：编译阶段和运行阶段分离，最终镜像只包含二进制文件
2. **选择小的基础镜像**：`alpine` 或 `distroless` 代替 `ubuntu`
3. **合并 RUN 指令**：减少镜像层数
4. **.dockerignore**：避免把不需要的文件（如 .git, node_modules）打进镜像
5. **指定具体版本**：`FROM node:18-alpine` 而不是 `FROM node:latest`

---

### 二、Kubernetes 核心架构（超高频）

#### 1. K8s 整体架构

```
┌─────────────────────────────────────────────────────────────┐
│                      Master (Control Plane)                  │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
│  │  API Server │ │   etcd      │ │  Controller Manager │   │
│  │  (统一入口)  │ │ (状态存储)   │ │   (控制循环)         │   │
│  └─────────────┘ └─────────────┘ └─────────────────────┘   │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              Scheduler (调度器)                      │   │
│  │         "把 Pod 放到最合适的 Node 上"                 │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                        Node (Worker)                         │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
│  │   kubelet   │ │ kube-proxy  │ │  Container Runtime  │   │
│  │(Pod 生命周期)│ │ (网络代理)   │ │   (docker/containerd)│   │
│  └─────────────┘ └─────────────┘ └─────────────────────┘   │
│                                                              │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐                        │
│  │  Pod    │ │  Pod    │ │  Pod    │  ...                   │
│  │ [nginx] │ │ [app]   │ │ [redis] │                        │
│  └─────────┘ └─────────┘ └─────────┘                        │
└─────────────────────────────────────────────────────────────┘
```

**Master 组件（控制平面）**：

| 组件 | 作用 | 挂了会怎样 |
|---|---|---|
| **API Server** | 所有操作的统一入口，REST API | 整个集群无法操作 |
| **etcd** | 分布式键值存储，保存集群所有状态 | 集群状态丢失，无法恢复 |
| **Controller Manager** | 运行各种控制器（Deployment/ReplicaSet/Node 等），维持期望状态 | Pod 不会自动恢复、扩缩容失效 |
| **Scheduler** | 监听未调度的 Pod，选择最优 Node | 新 Pod 无法创建，已有 Pod 不受影响 |

**Node 组件（工作节点）**：

| 组件 | 作用 |
|---|---|
| **kubelet** | 接收 API Server 指令，管理 Pod 生命周期（创建/销毁/健康检查） |
| **kube-proxy** | 维护节点网络规则，实现 Service 的负载均衡（iptables/IPVS） |
| **Container Runtime** | 真正运行容器，Docker/containerd/CRI-O |

> 💡 **面试金句**：kubelet 是 Node 上的"管家"，它不负责调度，只负责"执行"——API Server 告诉它做什么，它就做什么。

#### 2. Pod 生命周期与状态

```
Pending → Running → Succeeded / Failed / Unknown
   ↓
ContainerCreating（拉取镜像中）
   ↓
CrashLoopBackOff（启动失败，正在重试）
   ↓
ImagePullBackOff（镜像拉取失败）
   ↓
Terminating（正在删除）
```

**Pod 的 restartPolicy**：
- `Always`：默认，容器退出总是重启（适合长服务）
- `OnFailure`：失败时重启，成功不重启（适合 Job）
- `Never`：不重启（适合一次性任务）

#### 3. 核心工作负载资源对比

| 资源 | 适用场景 | 特点 |
|---|---|---|
| **Pod** | 直接运行容器 | 最小调度单位，但一般不直接用 |
| **Deployment** | 无状态应用（Web/API） | 滚动更新、回滚、自动扩缩容 |
| **StatefulSet** | 有状态应用（DB/Kafka） | 稳定的网络标识、有序部署、持久化存储 |
| **DaemonSet** | 每个 Node 跑一个（日志/监控） | 自动随节点扩缩 |
| **Job** | 一次性任务 | 完成即停，可设置重试次数 |
| **CronJob** | 定时任务 | 基于 Job 的定时调度 |

> 💡 **面试高频**：Deployment vs StatefulSet 的区别？  
> **Deployment**：Pod 之间无区别，随机调度，适合无状态服务。  
> **StatefulSet**：每个 Pod 有唯一序号（pod-0, pod-1），有稳定的 PVC 和 DNS，适合需要数据持久化的有状态服务。

#### 4. Service 与网络

**Service 类型**：

| 类型 | 访问方式 | 面试考点 |
|---|---|---|
| **ClusterIP** | 集群内部访问 | 默认类型，kube-proxy 通过 iptables/IPVS 做负载均衡 |
| **NodePort** | 节点IP:端口 | 每个节点开放端口，范围 30000-32767 |
| **LoadBalancer** | 云厂商 LB | 自动创建云负载均衡器，适合生产 |
| **ExternalName** | DNS CNAME | 映射到外部域名 |

**Service 原理（面试高频）**：
```
Pod IP 是不稳定的（重启会变），Service 通过 Label Selector 动态关联 Pod

Service IP 是虚拟 IP（ClusterIP），由 kube-proxy 维护 iptables 规则：
- 访问 ClusterIP 时，iptables 随机 DNAT 到某个 Pod IP
- IPVS 模式性能更好（生产推荐）

DNS 解析：
- my-service.my-namespace.svc.cluster.local → ClusterIP
```

**Ingress**：
```
┌─────────┐     ┌───────────┐     ┌─────────────┐
│   用户   │────→│  Ingress  │────→│  Service    │
│         │     │ Controller│     │  (ClusterIP)│
└─────────┘     │ (Nginx/   │     └──────┬──────┘
                │  Traefik) │            │
                └───────────┘     ┌──────┴──────┐
                                  │ Pod A Pod B │
                                  └─────────────┘

Ingress 提供：
- 七层路由（按域名/路径）
- SSL/TLS 终止
- 限流、重写等
```

#### 5. 存储（PV / PVC / StorageClass）

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│     Pod     │────→│    PVC      │────→│     PV      │
│  (消费存储)  │     │ (声明需求)   │     │ (实际存储)   │
└─────────────┘     └─────────────┘     └──────┬──────┘
                                               │
                                        ┌──────┴──────┐
                                        │ NFS/EBS/    │
                                        │ Ceph/本地盘  │
                                        └─────────────┘

StorageClass: 动态 provisioner，按需自动创建 PV
```

#### 6. 调度器 Scheduler 原理

```
1. 过滤阶段 (Predicates) — "哪些 Node 能跑？"
   - 资源足够吗？（CPU/内存）
   - 节点亲和性/反亲和性匹配吗？
   - Pod 亲和性/反亲和性满足吗？
   - 端口冲突吗？
   - Taint 能容忍吗？

2. 评分阶段 (Priorities) — "哪个 Node 最合适？"
   - 资源利用率均衡（LeastRequestedPriority）
   - 亲和性权重
   - 镜像本地性（Node 上已有镜像得分更高）
```

> 💡 **面试金句**：K8s 调度是"先过滤再评分"的两阶段过程。过滤是硬约束（必须满足），评分是软约束（选最优）。

---

### 三、高频面试题速查

#### Q1: Docker 和虚拟机有什么区别？

> **三层回答模板**：
> 1. **虚拟化层级**：VM 是硬件级虚拟化（Hypervisor），容器是 OS 级虚拟化（共享内核）
> 2. **资源效率**：容器轻量（MB 级镜像，秒级启动），VM 重量（GB 级镜像，分钟级启动）
> 3. **隔离程度**：VM 隔离更强（独立内核），容器隔离较弱（共享内核，需要 Namespace + Cgroups）
>
> 适用场景：VM 适合强隔离多租户，容器适合微服务快速部署扩缩容。

#### Q2: 容器里面 PID 1 是什么？为什么重要？

> PID 1 是容器内第一个进程，承担**信号转发**和**僵尸进程回收**的责任。
> 
> 常见问题：如果 PID 1 不能正确处理 SIGTERM，kubectl delete pod 时容器会 graceful shutdown 失败，被强制 SIGKILL。
> 
> 解决方案：使用 tini/dumb-init 作为 entrypoint，或确保主进程能正确处理信号。

#### Q3: K8s 中 Pod 一直 Pending 是什么原因？

> 排查思路：
> 1. `kubectl describe pod <name>` 看 Events
> 2. **资源不足**：节点 CPU/内存不够 → 扩容节点或调整 requests
> 3. **镜像拉取失败**：ImagePullBackOff → 检查镜像名/Secret/网络
> 4. **调度失败**：taints/tolerations 不匹配、亲和性规则太严格、没有可用节点
> 5. **PVC 未绑定**：StorageClass 问题或 PV 不足
> 6. **Init Container 卡住**：初始化脚本死锁或失败

#### Q4: K8s 滚动更新（Rolling Update）是怎么实现的？

> Deployment 的滚动更新策略：
> ```yaml
> strategy:
>   type: RollingUpdate
>   rollingUpdate:
>     maxUnavailable: 25%   # 更新时最多允许多少 Pod 不可用
>     maxSurge: 25%         # 最多可以比期望多创建多少 Pod
> ```
> 
> 过程：
> 1. 创建新 ReplicaSet，逐步扩容新 Pod
> 2. 同时缩容旧 ReplicaSet 的 Pod
> 3. 保证服务不中断（通过 maxUnavailable 控制）
> 4. 如果新 Pod 健康检查失败，自动回滚
>
> 底层：Deployment 控制器通过 API Server 操作 ReplicaSet，ReplicaSet 确保指定数量的 Pod 副本运行。

#### Q5: K8s 如何实现服务发现？

> 两种方式：
> 1. **环境变量**：Pod 创建时，K8s 自动注入同 namespace 下 Service 的 IP 和端口为环境变量（不太灵活）
> 2. **DNS（CoreDNS）**：推荐方式，Service 创建后自动注册 DNS 记录
>    - `my-service.my-namespace.svc.cluster.local`
>    - 同 namespace 内可直接用 `my-service`
>
> DNS 解析由 CoreDNS 处理，kube-dns 是早期实现（已废弃）。

#### Q6: 什么是 Helm？它和 K8s 的关系是什么？

> Helm 是 K8s 的包管理工具（类似 apt/yum）。
> 
> - **Chart**：Helm 包，包含一组 K8s 资源模板（Deployment/Service/ConfigMap 等）
> - **Release**：Chart 的运行实例，可以安装、升级、回滚
> - **Values**：自定义配置，渲染到模板中
> 
> 作用：避免手写大量 YAML，实现配置的版本管理和复用。

#### Q7: K8s 的探针（Probe）有哪些类型？

> 三种探针：
> | 探针 | 作用 | 失败时的行为 |
> |---|---|---|
> | **Liveness** | 容器是否还活着 | 重启容器 |
> | **Readiness** | 容器是否准备好接收流量 | 从 Service Endpoints 移除 |
> | **Startup** | 容器是否启动完成 | 禁用其他探针，防止慢启动被误杀 |
>
> 配置方式：HTTP GET / TCP Socket / 执行命令

---

### 四、CI/CD 与 DevOps 基础

#### 典型流水线

```
Code Commit → Build → Test → Security Scan → Push Image → Deploy to Staging → Integration Test → Deploy to Production
     ↓           ↓       ↓          ↓              ↓               ↓                  ↓                  ↓
   GitHub    Docker   Unit    Trivy/Snyk    Harbor/ECR    Helm/Kustomize     Cypress/        Canary /
   Actions   Build    Test                      Argo CD        Postman         Blue-Green
```

**GitOps（Argo CD / Flux）**：
> 以 Git 为唯一事实来源，Argo CD 监控 Git 仓库，自动同步 K8s 集群状态。
> 
> 优势：版本控制、回滚简单、审计可追溯、权限控制（通过 Git）。

---

## 📝 今日速记卡

### 算法速记

```
任务调度器公式：
  答案 = max(任务总数, (maxCount - 1) * (n + 1) + maxCountTasks)

maxCount      = 最频繁任务的出现次数
maxCountTasks = 出现次数等于 maxCount 的任务种类数
n             = 冷却时间

本质：最频繁任务决定骨架长度，其他任务填充间隙
```

### Docker 速记

```
Namespace → 隔离（视图）
Cgroups   → 限制（资源）
UnionFS   → 存储（分层）

容器 vs VM：共享内核，轻量，启动快，隔离弱
```

### Kubernetes 速记

```
Master: API Server (入口) + etcd (状态) + Controller Manager (控制) + Scheduler (调度)
Node:   kubelet (执行) + kube-proxy (网络) + Container Runtime (运行)

Pod → 最小调度单位
Deployment  → 无状态应用
StatefulSet → 有状态应用
DaemonSet   → 每个节点一个
Service     → ClusterIP/NodePort/LoadBalancer
Ingress     → 七层路由 + SSL

调度: Predicates(过滤) → Priorities(评分)
```

---

> 📌 **明日预告**: Day 81 — 重构字符串 + K8s 网络模型与 CNI 插件深入
>
> 🎯 **本周目标**: 掌握 Docker/Kubernetes 核心原理，能在面试中画出架构图并讲清楚各组件协作流程。
