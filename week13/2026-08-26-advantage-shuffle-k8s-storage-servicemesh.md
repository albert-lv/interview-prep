# Day 83 · 优势洗牌 + K8s 持久化存储与 Service Mesh 核心原理

> 📅 2026-08-26 | Week 13 云原生与容器化基础设施 🐳

---

## 📋 今日概览

| 模块 | 内容 |
|---|---|
| **算法题** | 优势洗牌（Advantage Shuffle）— 田忌赛马贪心策略 |
| **系统设计** | K8s 持久化存储体系（PV/PVC/StorageClass/CSI） |
| **云原生进阶** | Service Mesh 架构与 Istio 核心原理 |

---

## 一、今日算法题：优势洗牌

### 题目描述

给定两个长度相等的数组 `nums1` 和 `nums2`，你需要重新排列 `nums1` 中元素的顺序，使得 `nums1` 相对于 `nums2` 的优势最大化。

定义：如果 `nums1[i] > nums2[i]`，则称 `nums1` 在索引 `i` 处具有优势。返回重排后的 `nums1`。

**示例：**
```
输入: nums1 = [2,7,11,15], nums2 = [1,10,4,11]
输出: [2,11,7,15]
解释: 
  - nums1[0]=2 > nums2[0]=1 ✓
  - nums1[1]=11 > nums2[1]=10 ✓
  - nums1[2]=7 > nums2[2]=4 ✓
  - nums1[3]=15 > nums2[3]=11 ✓
  全部 4 个位置都有优势
```

**约束：**
- `1 <= nums1.length <= 10^5`
- `nums2.length == nums1.length`
- `0 <= nums1[i], nums2[i] <= 10^9`

### 解题思路

**核心思想：田忌赛马**

这个策略大家都懂——
1. **用下等马对上等马**（必输，但不浪费好牌）
2. **用上等马对中等马**（能赢）
3. **用中等马对下等马**（能赢）

翻译成算法：
1. 将 `nums1` 排序，准备"出牌"
2. 对 `nums2` 的每个元素（从大到小），尝试用 `nums1` 中刚好比它大的最小元素去"赢"
3. 如果赢不了，就用 `nums1` 中最小的元素去"牺牲"

**为什么贪心有效？**
- 要最大化赢的次数，每局都应该"刚好赢"——用最小的代价换取胜利
- 如果当前最小的牌都能赢，那当然直接出
- 如果最大的牌都赢不了，那这张牌留着也没意义，拿去垫掉对面最大的

### 代码实现

```go
package main

import (
    "fmt"
    "sort"
)

// advantageShuffle 田忌赛马贪心
func advantageShuffle(nums1 []int, nums2 []int) []int {
    n := len(nums1)
    
    // 1. nums1 排序，准备出牌
    sort.Ints(nums1)
    
    // 2. 记录 nums2 的原始索引，因为我们要排序但不能打乱返回顺序
    type pair struct {
        val   int
        index int
    }
    nums2Sorted := make([]pair, n)
    for i, v := range nums2 {
        nums2Sorted[i] = pair{v, i}
    }
    // 从大到小排序 nums2——先处理对面的"上等马"
    sort.Slice(nums2Sorted, func(i, j int) bool {
        return nums2Sorted[i].val > nums2Sorted[j].val
    })
    
    // 3. 双指针：nums1[left] 是最小牌，nums1[right] 是最大牌
    left, right := 0, n-1
    res := make([]int, n)
    
    // 遍历 nums2 从大到小
    for _, p := range nums2Sorted {
        // 如果当前最大牌能赢，就出最大牌（刚好赢，不浪费）
        if nums1[right] > p.val {
            res[p.index] = nums1[right]
            right--
        } else {
            // 赢不了，拿最小牌去垫掉
            res[p.index] = nums1[left]
            left++
        }
    }
    
    return res
}

func main() {
    nums1 := []int{2, 7, 11, 15}
    nums2 := []int{1, 10, 4, 11}
    fmt.Println(advantageShuffle(nums1, nums2)) // [2, 11, 7, 15]
    
    nums1 = []int{12, 24, 8, 32}
    nums2 = []int{13, 25, 32, 11}
    fmt.Println(advantageShuffle(nums1, nums2)) // [24, 32, 8, 12]
}
```

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|---|---|---|
| **时间** | O(N log N) | 排序主导 |
| **空间** | O(N) | 存储索引对和结果 |

### 面试官连环追问

> 💬 **追问 1：如果不要求返回具体排列，只问最大优势次数？**
>
> 🎯 可以只计数，不构造结果。还是贪心，但省掉索引映射，空间 O(1)（不算输出）。

> 💬 **追问 2：如果 nums1 和 nums2 长度不等？**
>
> 🎯 题目假设等长。若不等，问题定义需要调整——比如"最长公共子序列"式的匹配？

> 💬 **追问 3：能不能用堆来做？**
>
> 🎯 可以。用最小堆维护 nums1，对每个 nums2[i] 找 upper_bound。但排序+双指针已经最优，堆的常数更大。

---

## 二、面试技巧：K8s 持久化存储体系

### 2.1 为什么 Pod 需要持久化存储？

Pod 是**临时**的——重建后容器内数据全丢。但现实中应用需要：
- 数据库要存数据文件
- 日志要持久化
- 配置文件要共享

**K8s 存储的核心理念：使用者（Pod）和提供者（存储后端）解耦。**

### 2.2 Volume 类型速查

| 类型 | 生命周期 | 适用场景 | 数据是否持久 |
|---|---|---|---|
| `emptyDir` | 随 Pod 创建/销毁 | 临时缓存、多容器共享数据 | ❌ |
| `hostPath` | 随 Node | 访问宿主机文件（日志采集等） | ⚠️ 节点绑定 |
| `nfs` | 独立于集群 | 共享存储 | ✅ |
| `PV/PVC` | 独立于 Pod | 生产环境标准方案 | ✅ |
| `ConfigMap/Secret` | 独立于 Pod | 配置注入 | ✅（只读） |

### 2.3 PV / PVC / StorageClass 三层模型

```
┌─────────────────────────────────────────────────────────────┐
│  User (Dev)                    Admin (Ops)                  │
│                                                             │
│  ┌──────────┐                ┌──────────────────┐          │
│  │   Pod    │─── mounts ────▶│      PVC         │          │
│  │  (应用)   │                │  (我要 10Gi SSD)  │          │
│  └──────────┘                └────────┬─────────┘          │
│                                       │ 绑定 (Binding)      │
│                                       ▼                   │
│                              ┌──────────────────┐          │
│                              │       PV         │          │
│                              │  (实际的 10Gi     │◀──── 由  │
│                              │   EBS/Ceph/NFS)  │     Admin │
│                              └──────────────────┘    预先创建│
│                                                             │
│  动态供给 (Dynamic Provisioning) ──────▶ StorageClass        │
│  "我来根据 PVC 的要求自动创建 PV"                             │
└─────────────────────────────────────────────────────────────┘
```

**一句话记忆：**
- **PV**（PersistentVolume）= 实际的存储资源（管理员准备）
- **PVC**（PersistentVolumeClaim）= 用户的存储需求声明（开发者使用）
- **StorageClass** = 存储的"模板"，支持动态自动创建 PV

### 2.4 动态供给：StorageClass 配置示例

```yaml
# StorageClass 定义 — 告诉 K8s "如何创建存储"
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs  # 或 ebs.csi.aws.com
parameters:
  type: gp3
  fsType: ext4
reclaimPolicy: Retain      # 删除 PVC 后 PV 怎么办？Retain/Delete
volumeBindingMode: WaitForFirstConsumer  # 等 Pod 调度后再绑定，避免跨可用区问题
---
# PVC 使用 — 开发者只需要声明需求
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-data
spec:
  accessModes:
    - ReadWriteOnce       # RWO = 单节点读写
  storageClassName: fast-ssd
  resources:
    requests:
      storage: 10Gi
```

**AccessModes 解析：**
| 模式 | 缩写 | 含义 | 典型场景 |
|---|---|---|---|
| ReadWriteOnce | RWO | 单节点读写 | 大多数数据库 |
| ReadOnlyMany | ROX | 多节点只读 | 静态资源分发 |
| ReadWriteMany | RWX | 多节点读写 | NFS 共享存储 |
| ReadWriteOncePod | RWOP | 单 Pod 独占 | K8s 1.27+，安全隔离 |

### 2.5 CSI（Container Storage Interface）架构

**为什么要有 CSI？**

K8s 早期存储插件（in-tree）直接编译在核心代码里，导致：
- 新增存储类型要改 K8s 核心代码
- 存储厂商的发布节奏被 K8s 版本绑定
- 安全性问题（第三方代码在主进程里跑）

**CSI 解耦方案：**

```
┌──────────────────────────────────────────────────────┐
│                    K8s 控制平面                       │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐ │
│  │   API Server  │  │   kubelet    │  │  external-  │ │
│  │              │  │              │  │ provisioner │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬──────┘ │
│         │                 │                  │        │
│         │  Watch PVC      │  gRPC            │        │
│         │  创建 Volume    │  CSI 调用        │        │
│         ▼                 ▼                  ▼        │
│  ┌──────────────────────────────────────────────────┐ │
│  │         CSI Driver（独立进程，容器化部署）          │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌───────────┐ │ │
│  │  │ CSI Controller│  │   CSI Node  │  │  存储后端  │ │ │
│  │  │  (Provision/  │  │ (Mount/     │  │ (EBS/Ceph/│ │ │
│  │  │   Attach/     │  │  Unmount)   │  │  NFS...)  │ │ │
│  │  │   Snapshot)   │  │             │  │           │ │ │
│  │  └─────────────┘  └─────────────┘  └───────────┘ │ │
│  └──────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
```

**CSI 三个核心组件：**
1. **CSI Controller**：处理卷的创建、删除、快照、扩容（运行在任意节点）
2. **CSI Node**：处理卷的挂载/卸载（运行在每个节点，kubelet 通过 gRPC 调用）
3. **外部 Sidecar**：`external-provisioner`、`external-attacher`、`external-resizer`、`external-snapshotter`

**面试金句：**
> "CSI 让 K8s 存储生态从'绑定发布'变成'插件化'——存储厂商自己维护 CSI Driver，和 K8s 版本解耦，故障域也更清晰。"

### 2.6 StatefulSet + PVC 的工作模式

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: "postgres"
  replicas: 3
  volumeClaimTemplates:      # 关键：每个 Pod 有自己的 PVC
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 10Gi
```

**StatefulSet 的存储特性：**
- Pod-0、Pod-1、Pod-2 各自拥有**独立的 PVC**
- Pod 重建后，通过 `volumeClaimTemplates` 重新绑定到**同一个 PV**
- 配合 Headless Service，实现稳定的网络标识 + 稳定存储 = 有状态服务

**对比 Deployment（无状态）：**
| 特性 | Deployment | StatefulSet |
|---|---|---|
| Pod 命名 | 随机 hash | 有序序号（-0, -1, -2） |
| 存储 | 共享或临时 | 每个 Pod 独立 PVC |
| 网络 | 任意 IP | 稳定 DNS（通过 Headless Service） |
| 伸缩 | 并行 | 有序（先扩后缩） |
| 典型应用 | Web 服务 | 数据库、消息队列 |

### 2.7 高频面试题速查

> **Q：PV 的 reclaimPolicy 有哪些？**
>
> 🎯 Retain（保留，需手动清理）、Delete（删除 PVC 时自动删除 PV 和底层存储）、Recycle（已废弃，擦除数据后复用）

> **Q：为什么推荐 `WaitForFirstConsumer`？**
>
> 🎯 避免 PVC 先绑定到可用区 A 的 PV，结果 Pod 被调度到可用区 B 导致无法挂载。等 Pod 调度确定后再绑定，保证同可用区。

> **Q：CSI 和 in-tree 插件的区别？**
>
> 🎯 CSI 是外部标准接口，存储厂商独立维护 Driver；in-tree 是 K8s 核心代码的一部分，需要跟随 K8s 版本发布。

---

## 三、Service Mesh 核心原理

### 3.1 什么是 Service Mesh？

**一句话定义：** 把服务间通信的复杂度（流量控制、安全、可观测性）从应用代码里**抽出来**，下沉到一个独立的基础设施层。

**为什么需要它？**

微服务拆分后，服务间调用变得复杂：
- A 调用 B，B 调用 C，C 调用 D——调用链变长
- 要加熔断、重试、超时——每个服务都得写一遍
- 要 mTLS 加密——证书管理头大
- 要监控调用延迟——埋点侵入业务代码

**Service Mesh 的解法：Sidecar 模式**

```
Before（传统微服务）:
┌─────────┐  HTTP/gRPC  ┌─────────┐  HTTP/gRPC  ┌─────────┐
│ Service │◄───────────▶│ Service │◄───────────▶│ Service │
│   A     │  (自己处理   │   B     │  重试/熔断/  │   C     │
│ (+重试  │   超时/熔断) │ (+重试  │   超时...)   │ (+重试  │
│  +超时  │              │  +超时  │              │  +超时  │
│  +mTLS) │              │  +mTLS) │              │  +mTLS) │
└─────────┘              └─────────┘              └─────────┘

After（Service Mesh）:
┌─────────┐  localhost  ┌─────────┐  localhost  ┌─────────┐
│ Service │◄───────────▶│ Service │◄───────────▶│ Service │
│   A     │   (代理接管)  │   B     │   (代理接管)  │   C     │
└─────────┘              └─────────┘              └─────────┘
     ▲                       ▲                       ▲
     │  iptables 透明拦截     │  iptables 透明拦截     │
     ▼                       ▼                       ▼
┌─────────┐              ┌─────────┐              ┌─────────┐
│ Sidecar │◄────────────▶│ Sidecar │◄────────────▶│ Sidecar │
│ (Envoy) │   mTLS +     │ (Envoy) │   mTLS +     │ (Envoy) │
│         │  流量控制     │         │  流量控制     │         │
└─────────┘  + 可观测     └─────────┘  + 可观测     └─────────┘

应用无感知：业务代码还是 `curl http://service-b`，
但流量被 iptables 透明劫持到 Sidecar 处理。
```

### 3.2 Istio 架构解析

Istio 是当前最流行的 Service Mesh 实现，架构分为两层：

```
┌─────────────────────────────────────────────────────────────┐
│                     控制平面（Control Plane）                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐ │
│  │   istiod     │  │  Istio API   │  │   Certificate        │ │
│  │  (单体组件)   │  │   Server     │  │   Authority (CA)     │ │
│  │              │  │              │  │                      │ │
│  │ • Pilot:     │  │              │  │ • 签发/轮转工作负载    │ │
│  │   服务发现    │  │ • Gateway    │  │   证书               │ │
│  │   流量配置    │  │ • VirtualS.  │  │ • 自动 mTLS          │ │
│  │   xDS 下发    │  │ • DestRule   │  │                      │ │
│  │              │  │ • ServiceE.  │  │                      │ │
│  │ • Citadel:   │  │              │  │                      │ │
│  │   证书管理    │  │              │  │                      │ │
│  │              │  │              │  │                      │ │
│  │ • Galley:    │  │              │  │                      │ │
│  │   配置验证    │  │              │  │                      │ │
│  └──────┬───────┘  └──────────────┘  └──────────────────────┘ │
│         │                                                    │
│         │  xDS API (gRPC)                                     │
│         ▼                                                    │
├─────────────────────────────────────────────────────────────┤
│                      数据平面（Data Plane）                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐ │
│  │   Envoy      │  │   Envoy      │  │       Envoy          │ │
│  │  (Sidecar)   │  │  (Sidecar)   │  │      (Sidecar)       │ │
│  │              │  │              │  │                      │ │
│  │ • L7 代理     │  │ • 负载均衡    │  │ • 熔断/重试/超时      │ │
│  │ • mTLS 终止   │  │ • 健康检查    │  │ • 流量镜像/金丝雀     │ │
│  │ • 指标收集    │  │ • 百分比分发  │  │ • 故障注入（混沌）    │ │
│  └──────────────┘  └──────────────┘  └──────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

**关键设计：** Istio 1.5+ 将控制平面合并为单个 `istiod` 二进制，降低复杂度和资源占用。

### 3.3 流量管理核心 CRD

```yaml
# VirtualService：定义路由规则
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews-route
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2           # Jason 的流量去 v2
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 90             # 90% 去 v1
    - destination:
        host: reviews
        subset: v3
      weight: 10             # 10% 去 v3（金丝雀）

---
# DestinationRule：定义服务子集和策略
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: reviews-dest
spec:
  host: reviews
  trafficPolicy:
    connectionPool:
      tcp:
        maxConnections: 100
      http:
        http1MaxPendingRequests: 50
    outlierDetection:          # 熔断配置
      consecutiveErrors: 5
      interval: 30s
      baseEjectionTime: 30s
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v3
    labels:
      version: v3
```

### 3.4 mTLS 自动加密

```
┌─────────┐                    ┌─────────┐
│  Pod A  │◄────── mTLS ──────▶│  Pod B  │
│         │   (Istio 自动管理)   │         │
└────┬────┘                    └────┬────┘
     │                              │
     │  1. Citadel 为每个 Service    │  2. Sidecar 自动交换证书
     │     Account 签发证书          │     完成双向认证
     │  3. 证书自动轮转（默认24h）    │  4. 业务代码无感知
```

**mTLS 模式：**
| 模式 | 说明 |
|---|---|
| `PERMISSIVE` | 同时接受明文和 mTLS（迁移过渡期） |
| `STRICT` | 强制 mTLS，拒绝明文 |
| `DISABLE` | 关闭 mTLS |

### 3.5 Service Mesh vs API Gateway

| 维度 | API Gateway | Service Mesh |
|---|---|---|
| **定位** | 南北向流量（外部→集群） | 东西向流量（服务间） |
| **功能** | 认证鉴权、限流、路由、API 版本管理 | 服务发现、负载均衡、熔断、mTLS、可观测 |
| **部署** | 集群边缘（Ingress） | 每个 Pod 的 Sidecar |
| **代表** | Kong、APISIX、Nginx、AWS API Gateway | Istio、Linkerd、Consul Connect |
| **关系** | **互补**——Gateway 处理入口，Mesh 处理内部 |

### 3.6 Service Mesh 的代价

**不是所有场景都适合：**

| 问题 | 说明 |
|---|---|
| **延迟增加** | Sidecar 引入额外一跳，通常 2-5ms，对延迟敏感场景需评估 |
| **资源开销** | 每个 Pod 额外一个 Envoy，内存占用 50-100MB+ |
| **复杂度** | 新增一层抽象，排错更困难（"到底是业务问题还是 Sidecar 问题？"） |
| **学习成本** | CRD 多、概念多、调试难 |

**适用场景：**
- ✅ 大规模微服务（>50 个服务）
- ✅ 多语言技术栈（Java/Go/Python 混用，无法统一 SDK）
- ✅ 强安全合规要求（强制 mTLS、审计）
- ❌ 单体应用或只有几个服务——杀鸡用牛刀

### 3.7 高频面试题速查

> **Q：Sidecar 是怎么劫持流量的？**
>
> 🎯 Istio 使用 `istio-init` initContainer 设置 iptables 规则，将 Pod 的出站/入站流量透明重定向到 Envoy Sidecar 的 15001/15006 端口。应用无感知。

> **Q：Envoy 的 xDS API 是什么？**
>
> 🎯 xDS 是一组发现服务 API（LDS/ RDS/ CDS/ EDS/ SDS），istiod 通过这些 gRPC 接口将配置动态推送给 Envoy，实现无需重启的热更新。

> **Q：Istio 和 Linkerd 怎么选？**
>
> 🎯 Istio 功能最全（流量管理丰富、多集群支持），但复杂度高；Linkerd 轻量简单（Rust 写的代理，资源占用低），适合中小规模。选型看团队规模和需求复杂度。

> **Q：Sidecarless Mesh（如 Cilium Service Mesh）是什么？**
>
> 🎯 用 eBPF 在内核层实现流量管理，不需要每个 Pod 部署 Sidecar，降低资源开销和延迟。Cilium 的 Service Mesh 就是基于 eBPF 的 Sidecarless 方案。

---

## 四、今日速记卡

### 算法速记
```
优势洗牌 = 田忌赛马 = 贪心双指针
- nums1 排序，准备出牌
- nums2 从大到小遍历
- 能赢就出最大牌，赢不了就拿最小牌垫
- 时间 O(N log N)，空间 O(N)
```

### K8s 存储速记
```
三层模型：PVC（我要）→ PV（我有）→ StorageClass（自动造）
CSI 架构：Controller（创建卷）+ Node（挂载卷）+ Sidecar（辅助）
AccessMode：RWO（单节点读写）/ ROX（多节点只读）/ RWX（多节点读写）
动态供给：StorageClass + WaitForFirstConsumer（避免跨可用区）
```

### Service Mesh 速记
```
本质：服务间通信下沉到基础设施层
核心：Sidecar 代理（Envoy）+ 控制平面（istiod）
三大能力：流量管理（VirtualService/DestinationRule）
         安全（自动 mTLS）
         可观测（Metrics/Tracing）
代价：延迟+2~5ms，每个 Pod 额外 50-100MB 内存
适用：大规模微服务、多语言栈、强安全要求
```

---

## 五、延伸阅读

- [K8s 官方文档 - 存储](https://kubernetes.io/docs/concepts/storage/)
- [CSI 规范](https://github.com/container-storage-interface/spec)
- [Istio 架构文档](https://istio.io/latest/docs/ops/deployment/architecture/)
- [Service Mesh 比较：Istio vs Linkerd](https://linkerd.io/2020/12/03/why-linkerd-doesnt-use-istio/)

---

> 🎯 **明日预告**：Day 84 — 贪心/设计题 + K8s 调度器深度原理（亲和性/污点容忍/优先级抢占）
