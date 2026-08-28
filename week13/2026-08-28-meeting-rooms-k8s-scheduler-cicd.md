# Day 85 — 会议室 II + K8s 调度器高级特性与 CI/CD 流水线设计

> 今日主题：最小堆调度 + K8s 调度器高阶机制与持续交付流水线
> Week 13 · 2026-08-28

---

## 🧩 今日算法题：会议室 II（Meeting Rooms II）

**题目链接**：LeetCode 253 — [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)

### 题目描述

给定一个会议时间安排的数组 `intervals`，每个会议时间都会包括开始和结束的时间 `intervals[i] = [starti, endi]`。

返回**所需会议室的最小数量**。

**示例 1**：
```
输入: intervals = [[0,30],[5,10],[15,20]]
输出: 2
解释: 
  [0,30] 占用会议室 1
  [5,10] 占用会议室 2（5-10 期间会议室 1 被占用）
  [15,20] 结束后会议室 2 释放，15-20 可以复用会议室 2
  共需 2 间会议室
```

**示例 2**：
```
输入: intervals = [[7,10],[2,4]]
输出: 1
解释: 两个会议时间不重叠，可以复用同一间会议室
```

**约束条件**：
- `1 <= intervals.length <= 10^4`
- `0 <= starti < endi <= 10^6`

---

### 💡 解题思路

#### 核心问题转化

这题的本质是：**同一时刻最多有多少个会议在同时进行？**

换个角度想：如果把所有会议按时间轴展开，问的是时间轴上「重叠区间数」的最大值。

#### 方法一：最小堆（最直观）

思路：按开始时间排序，维护一个最小堆存放**当前占用会议室的结束时间**。

1. 按开始时间升序排序所有会议
2. 遍历每个会议：
   - 如果堆顶（最早结束的会议）的结束时间 ≤ 当前会议开始时间 → 该会议室可以复用，弹出堆顶
   - 把当前会议的结束时间加入堆
3. 堆的大小就是同时进行的会议数，即所需会议室数量

**为什么最小堆？** 我们想找「最早结束」的会议，看它能不能腾出来给当前会议用。最小堆 O(1) 查堆顶，O(log n) 插入删除。

#### 方法二：扫描线（更通用）

把所有时间点拆开，标记「开始+1」「结束-1」，按时间排序后扫描，维护当前并发数，取最大值。

适用于更复杂的变体（如每个会议室有容量限制、会议室有设备等）。

---

### 📝 代码实现

**Go 实现（最小堆）**：

```go
package main

import (
	"container/heap"
	"sort"
)

// 最小堆定义
type IntHeap []int

func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

func (h *IntHeap) Push(x interface{}) {
	*h = append(*h, x.(int))
}

func (h *IntHeap) Pop() interface{} {
	old := *h
	n := len(old)
	x := old[n-1]
	*h = old[0 : n-1]
	return x
}

func minMeetingRooms(intervals [][]int) int {
	if len(intervals) == 0 {
		return 0
	}

	// 按开始时间排序
	sort.Slice(intervals, func(i, j int) bool {
		return intervals[i][0] < intervals[j][0]
	})

	h := &IntHeap{}
	heap.Init(h)

	// 第一个会议占一间会议室
	heap.Push(h, intervals[0][1])

	for i := 1; i < len(intervals); i++ {
		start, end := intervals[i][0], intervals[i][1]

		// 如果最早结束的会议已经结束了，复用该会议室
		if (*h)[0] <= start {
			heap.Pop(h)
		}

		// 当前会议占用一间会议室（新的或复用的）
		heap.Push(h, end)
	}

	return h.Len()
}
```

**Python 实现**：

```python
import heapq

class Solution:
    def minMeetingRooms(self, intervals: list[list[int]]) -> int:
        if not intervals:
            return 0

        # 按开始时间排序
        intervals.sort(key=lambda x: x[0])

        # 最小堆：存放当前占用会议室的结束时间
        heap = []
        heapq.heappush(heap, intervals[0][1])

        for start, end in intervals[1:]:
            # 最早结束的会议已经结束了，复用会议室
            if heap[0] <= start:
                heapq.heappop(heap)
            # 当前会议需要一间会议室
            heapq.heappush(heap, end)

        return len(heap)
```

**扫描线版本（Python）**：

```python
class Solution:
    def minMeetingRooms(self, intervals: list[list[int]]) -> int:
        events = []
        for start, end in intervals:
            events.append((start, 1))   # 开始：+1
            events.append((end, -1))    # 结束：-1

        # 按时间排序；时间相同则结束优先（先释放再占用）
        events.sort(key=lambda x: (x[0], x[1]))

        res = curr = 0
        for _, delta in events:
            curr += delta
            res = max(res, curr)

        return res
```

---

### ⏱ 复杂度分析

| 维度 | 复杂度 | 说明 |
|---|---|---|
| **时间** | O(n log n) | 排序 O(n log n) + 堆操作 O(n log n) |
| **空间** | O(n) | 堆最多存 n 个元素 |

---

### 🎯 面试官追问

**Q1：如果每个会议室有容量限制（最多坐 k 人），怎么改？**
> 会议有参会人数属性，堆元素要存 `(结束时间, 剩余容量)`。复用时检查剩余容量够不够，不够就开新会议室。时间复杂度不变。

**Q2：如果要输出具体的会议室分配方案？**
> 堆里存 `(结束时间, 会议室编号)`。每次复用时取出编号复用，否则分配新编号。最后按编号分组输出即可。

**Q3：扫描线和最小堆哪个更好？**
> 最小堆更直观，适合「找最早结束的资源复用」这类场景。扫描线更通用，适合变体（如加权、多维度约束）。面试里推荐先写最小堆，再提扫描线作为拓展。

**Q4：如果数据流是无限来的（online），怎么办？**
> 在线场景用最小堆直接处理，来一个会议处理一个，均摊 O(log n)。不需要预排序，维护一个按开始时间的最小堆和一个按结束时间的最小堆即可。

---

## ⚙️ 面试技巧：K8s 调度器高级特性与 CI/CD 流水线设计

### 一、K8s 调度器高阶机制

默认调度器的两阶段（Predicates + Priorities）只是基础。生产环境里，你绕不开这些高级特性。

#### 1. 节点亲和性与反亲和性（Node / Pod Affinity & Anti-Affinity）

```yaml
# Pod 亲和性：把这个 Pod 和带 "app=cache" 标签的 Pod 放一起
apiVersion: v1
kind: Pod
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values: ["cache"]
        topologyKey: kubernetes.io/hostname   # 同一台机器
---
# Pod 反亲和性：同一个 Deployment 的 Pod 分散到不同可用区
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app: web
              topologyKey: topology.kubernetes.io/zone
```

**面试速记表**：

| 类型 | 语义 | 典型场景 |
|---|---|---|
| `nodeAffinity` | Pod 想去哪些节点 | GPU 任务去 GPU 节点 |
| `podAffinity` | Pod 想挨着谁 | 缓存服务和业务服务同节点，减少网络延迟 |
| `podAntiAffinity` | Pod 不想和谁挨着 | 高可用：同一个服务的 Pod 分散到不同机柜/可用区 |

**金句**：
> "反亲和性是高可用的关键配置。我们给核心服务配了 `podAntiAffinity`，确保同一个 Deployment 的 Pod 不会调度到同一个可用区，单 AZ 故障时服务不中断。"

#### 2. 污点与容忍（Taints & Tolerations）

Taint 是节点「赶人」，Toleration 是 Pod「忍一下」。

```yaml
# 给节点打污点：只接受特定负载
kubectl taint nodes node1 dedicated=gpu:NoSchedule

# Pod 声明能容忍这个污点
apiVersion: v1
kind: Pod
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

**三种 Effect**：
- `NoSchedule` — 不能容忍的 Pod 不会被调度过来（已有 Pod 不驱逐）
- `PreferNoSchedule` — 尽量不调，不是硬性要求
- `NoExecute` — 不能容忍的 Pod **立即被驱逐**（已有 Pod 也赶走）

**典型场景**：
- 专用节点：GPU 节点、大数据节点，打污点防止普通 Pod 占上去
- 节点维护：维护前给节点打 `NoExecute` 污点，优雅驱逐 Pod
- 控制平面隔离：Master 节点默认有 `node-role.kubernetes.io/control-plane:NoSchedule`

#### 3. Pod 优先级与抢占（Priority & Preemption）

```yaml
# 定义优先级类
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
preemptionPolicy: PreemptLowerPriority    # 允许抢占低优先级 Pod
---
# Pod 声明优先级
apiVersion: v1
kind: Pod
spec:
  priorityClassName: high-priority
```

**调度流程**：
1. 高优先级 Pod 进来，找不到足够资源的节点
2. 调度器在该节点上选一批「低优先级 Pod」驱逐
3. 被驱逐的 Pod 进入 Pending，等待重新调度
4. 高优先级 Pod 被调度到腾出的节点上

**面试注意**：
- 抢占是**破坏性操作**，被驱逐的 Pod 可能正在处理请求
- 设置 `preemptionPolicy: Never` 可以只排队不抢占
- 生产环境建议给核心服务（支付、订单）设高优先级，给批处理任务设低优先级

#### 4. Pod 拓扑分布约束（Pod Topology Spread Constraints）

比 Anti-Affinity 更精细的分布控制：

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 6
  template:
    spec:
      topologySpreadConstraints:
      - maxSkew: 1              # 最多差 1 个 Pod
        topologyKey: topology.kubernetes.io/zone
        whenUnsatisfiable: ScheduleAnyway
        labelSelector:
          matchLabels:
            app: web
```

**语义**：6 个 Pod 在 3 个可用区里尽量均匀分布（2-2-2），如果某个区资源不够，`maxSkew: 1` 允许暂时 3-2-1，但调度器会努力平衡。

#### 5. 资源 QoS 分级

K8s 根据 `requests` 和 `limits` 自动给 Pod 分 QoS 等级：

| QoS 等级 | 条件 | 驱逐优先级 |
|---|---|---|
| **Guaranteed** | 所有容器都设置了 `limits=requests`，且只限 CPU/内存 | 最后驱逐 |
| **Burstable** | 至少一个容器有 `requests`，但不满足 Guaranteed | 中间 |
| **BestEffort** | 没有设置 `requests` 和 `limits` | **最先驱逐** |

**面试要点**：
> "核心服务（如支付网关）配 Guaranteed，确保节点内存不足时最后被杀。测试环境可以用 BestEffort 提高资源利用率。"

#### 6. ResourceQuota & LimitRange

```yaml
# 命名空间级别资源配额
apiVersion: v1
kind: ResourceQuota
metadata:
  name: ns-quota
  namespace: production
spec:
  hard:
    requests.cpu: "20"
    requests.memory: 100Gi
    limits.cpu: "40"
    limits.memory: 200Gi
    pods: "50"
---
# 默认资源限制（Pod 没配 requests/limits 时的默认值）
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
spec:
  limits:
  - default:
      cpu: "500m"
      memory: "512Mi"
    defaultRequest:
      cpu: "100m"
      memory: "128Mi"
    type: Container
```

---

### 二、CI/CD 流水线设计

#### 1. 主流工具对比

| 工具 | 定位 | 优点 | 缺点 |
|---|---|---|---|
| **Jenkins** | 老牌 CI/CD | 插件生态极丰富、灵活 | 单点、配置复杂、插件兼容性噩梦 |
| **GitLab CI** | Git 托管 + CI 一体 | `.gitlab-ci.yml` 即代码、Runner 可扩展 | 与 GitLab 强绑定 |
| **GitHub Actions** | 云原生托管 | 市场丰富、与 GitHub 深度集成 | vendor lock-in、私有 Runner 管理 |
| **ArgoCD** | GitOps 部署 | 声明式、自动同步、回滚方便 | 只负责 CD，CI 需要配合 |
| **Tekton** | K8s 原生 CI/CD | CRD 定义流水线、云原生 | 学习曲线陡、生态较新 |

**面试回答框架**：
> "我们选型分两层：CI 用 GitLab CI（团队已经在用 GitLab），CD 用 ArgoCD 做 GitOps 部署。Jenkins 太重了，维护成本高；GitHub Actions 也不错，但公司代码在 GitLab 上。"

#### 2. Pipeline as Code

```yaml
# .gitlab-ci.yml 示例
stages:
  - build
  - test
  - security
  - deploy

build:
  stage: build
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

test:
  stage: test
  script:
    - go test ./... -race -coverprofile=coverage.out
  coverage: '/coverage: \d+\.\d+%/'

security:
  stage: security
  script:
    - trivy image --exit-code 1 --severity HIGH,CRITICAL $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  allow_failure: false    # 高危漏洞阻断流水线

deploy-staging:
  stage: deploy
  script:
    - kubectl set image deployment/app app=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA -n staging
  environment:
    name: staging
  only:
    - main
```

#### 3. CI/CD 安全最佳实践

```
代码提交 → 依赖扫描（Snyk）→ 静态扫描（SonarQube）→ 镜像构建 → 漏洞扫描（Trivy）
    → 密钥检测（GitLeaks）→ 单元测试 → 集成测试 → 部署到 Staging → 人工审批 → 生产部署
```

**关键安全实践**：
- **不用 Docker socket 挂载**：用 Kaniko / Buildah 构建镜像，避免容器逃逸风险
- **RBAC 最小权限**：CI/CD ServiceAccount 只给目标命名空间的 `patch deployment` 权限
- **不可变镜像标签**：生产只用 SHA256 摘要，不用 `latest`
- **流水线密钥管理**：用 Vault / Sealed Secrets / External Secrets，不硬编码

#### 4. GitOps 工作流

```
Git Repo (单一可信源)
    └── k8s manifests / Helm charts
            ↓
    ArgoCD (watch git repo)
            ↓
    自动同步到 K8s 集群 (Staging)
            ↓
    人工审批 (Production)
            ↓
    ArgoCD 同步到生产集群
```

**ArgoCD 核心概念**：
- **Application**：一个 Git 仓库 + 一个目标集群/命名空间的映射
- **App of Apps**：用一个 ArgoCD Application 管理多个 Application
- **Sync Policy**：自动同步 / 手动同步 / 自动同步+自动修复（self-heal）

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/org/gitops-repo.git
    targetRevision: HEAD
    path: overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true        # 删除 Git 中不存在的资源
      selfHeal: true     # 集群里被手动改了的，自动改回 Git 定义
    syncOptions:
    - CreateNamespace=true
```

---

### 三、面试高频 Q&A

**Q：K8s 调度器 Predicates 和 Priorities 分别做什么？**
> Predicates 是「硬性过滤」：节点资源够不够、污点能不能容忍、亲和性满不满足——不满足直接筛掉。Priorities 是「软性打分」：在剩下的节点里按规则打分，选分数最高的。比如节点上同服务 Pod 越少分数越高（分散调度）。

**Q：Pod 调度失败怎么排查？**
> 1. `kubectl describe pod <name>` 看 Events；2. 常见原因：资源不够（Insufficient cpu/memory）、污点不匹配、亲和性不满足、PVC 绑定失败；3. 调度器日志：`kubectl logs -n kube-system kube-scheduler-*`。

**Q：ArgoCD 和传统 CI/CD 有什么区别？**
> 传统是 Push 模型：Jenkins 有集群凭据，主动 `kubectl apply`。GitOps 是 Pull 模型：ArgoCD 在集群里跑，监听 Git 变化，自动同步。好处是集群不需要暴露 API 给外部，安全性更高；Git 是单一可信源，回滚就是 `git revert`。

**Q：镜像构建为什么推荐 Kaniko 而不是 Docker？**
> Docker build 需要 Docker daemon（通常是挂载 `/var/run/docker.sock`），这等于给容器开了特权，有逃逸风险。Kaniko 在容器里直接构建，不需要 daemon，更安全，也适合在 K8s 里跑。

**Q：蓝绿部署和金丝雀部署在 K8s 里怎么实现？**
> 蓝绿：两个 Deployment（blue/green），Service selector 切流量，瞬间切换，零停机。金丝雀：两个版本同时跑，用 Ingress/Nginx/Service Mesh 按权重分流量（如 5% 到新版本），观察指标，逐步放大。Argo Rollouts 提供了更自动化的金丝雀分析（自动回滚/晋升）。

---

## 📝 今日小结

| 项目 | 要点 |
|---|---|
| **算法** | 会议室 II：最小堆维护最早结束时间，能复用就复用，O(n log n)。扫描线变体适用于更复杂的调度约束 |
| **K8s 调度** | 亲和/反亲和性、污点容忍、优先级抢占、拓扑分布约束、QoS 分级——生产调度必备 |
| **CI/CD** | Pipeline as Code、安全扫描左移（Trivy/SonarQube/GitLeaks）、GitOps Pull 模型、不可变镜像 |
| **面试话术** | "核心服务配 Guaranteed + PodAntiAffinity 跨 AZ 分布；CI 用 GitLab CI，CD 用 ArgoCD GitOps，高危漏洞阻断流水线。" |

---

> 📅 明日预告：Week 13 收官 —— 云原生架构模式与生产排错实战 🐳🔧
