# Day 82 · 根据身高重建队列 + Helm 与 GitOps 实践

> 📅 2026-08-25 | Week 13 — 云原生与容器化基础设施 🐳

---

## 1) 今日算法题：根据身高重建队列

**LeetCode 406. Queue Reconstruction by Height**

### 题目

假设有打乱顺序的一群人站成一个队列，数组 `people` 表示队列中一些人的属性（不一定按顺序）。每个 `people[i] = [hi, ki]` 表示第 `i` 个人的身高为 `hi`，前面**正好**有 `ki` 个身高大于或等于 `hi` 的人。

请你重新构造并返回输入数组 `people` 所表示的队列。返回的队列应该格式化为数组 `queue`，其中 `queue[j] = [hj, kj]` 是队列中第 `j` 个人的属性（`queue[0]` 是排在队列前面的人）。

**示例 1：**
```
输入：people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
输出：[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
```

**示例 2：**
```
输入：people = [[6,0],[5,0],[4,0],[3,2],[2,2],[1,4]]
输出：[[4,0],[5,0],[2,2],[3,2],[1,4],[6,0]]
```

**约束：**
- `1 <= people.length <= 2000`
- `0 <= hi <= 10^6`
- `0 <= ki < people.length`
- 题目数据保证队列可以被重建

---

### 思路与解法

这道题是贪心算法的经典应用，核心洞察在于：**先处理高的人，再处理矮的人**。

**为什么先处理高的？**

假设我们先排好了所有身高 > `h` 的人。此时再插入一个身高为 `h` 的人，由于所有已排好的人都比他高，他的 `k` 值就**完全等价于**他应该插入的位置索引。矮的人插在前面不会影响高的人的 `k` 值（因为矮的不算在"前面有 `k` 个更高的人"里）。

**策略：**
1. 按身高 **降序** 排序，身高相同的按 `k` 值 **升序** 排序
2. 依次将每个人插入到结果数组的索引 `k` 位置

**排序规则拆解：**
```
people = [[7,0], [7,1], [6,1], [5,0], [5,2], [4,4]]

排序后：
[7,0]  → 身高最高，k=0，插在索引 0
[7,1]  → 身高最高，k=1，插在索引 1
[6,1]  → 此时 [7,0],[7,1] 已排好，都比 6 高，k=1 插在索引 1
[5,0]  → 前面有 3 个更高的，k=0 插在索引 0
[5,2]  → 前面有 3 个更高的，k=2 插在索引 2
[4,4]  → 前面有 5 个更高的，k=4 插在索引 4

结果：[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
```

---

### 代码实现

#### Go 实现

```go
package main

import "sort"

func reconstructQueue(people [][]int) [][]int {
	// 1. 按身高降序，身高相同则按 k 升序
	sort.Slice(people, func(i, j int) bool {
		if people[i][0] == people[j][0] {
			return people[i][1] < people[j][1] // k 升序
		}
		return people[i][0] > people[j][0] // 身高降序
	})

	// 2. 依次插入到 k 位置
	result := make([][]int, 0, len(people))
	for _, p := range people {
		k := p[1]
		// 在索引 k 处插入
		result = append(result, nil)
		copy(result[k+1:], result[k:])
		result[k] = p
	}
	return result
}
```

#### 链表优化版（O(N log N) + O(N) 插入）

对于数据量大的情况，数组中间插入是 O(N)，可以用链表优化到均摊 O(1) 插入：

```go
func reconstructQueueLinked(people [][]int) [][]int {
	sort.Slice(people, func(i, j int) bool {
		if people[i][0] == people[j][0] {
			return people[i][1] < people[j][1]
		}
		return people[i][0] > people[j][0]
	})

	// 用 list 实现 O(1) 插入
	list := make([][]int, 0, len(people))
	for _, p := range people {
		k := p[1]
		list = append(list, nil)
		copy(list[k+1:], list[k:])
		list[k] = p
	}
	return list
}
```

#### Python 实现（最简洁）

```python
class Solution:
    def reconstructQueue(self, people: List[List[int]]) -> List[List[int]]:
        # 按身高降序，k 升序
        people.sort(key=lambda x: (-x[0], x[1]))
        result = []
        for p in people:
            result.insert(p[1], p)
        return result
```

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|---|---|---|
| **时间** | O(N log N) | 排序 O(N log N) + 插入 O(N²) 最坏（Go/Python 列表插入是 O(N)） |
| **空间** | O(N) | 结果数组 |

> 💡 **面试优化点**：如果面试官追问，可以提到用**链表**或**树状数组**将插入优化到 O(log N)，总复杂度可降至 O(N log N)。

---

### 面试官追问

| 追问 | 回答要点 |
|---|---|
| "能优化插入的复杂度吗？" | 可以用链表 O(1) 插入，或者用平衡 BST / 树状数组维护空位，查询第 k 个空位位置，总复杂度 O(N log N) |
| "为什么身高相同要按 k 升序？" | 如果按 k 降序，先插入 k 大的会导致后面 k 小的插入位置偏移，破坏正确性 |
| "证明一下这个贪心策略的正确性？" | 数学归纳法：假设已排好的所有人身高都 ≥ 当前人，则他们的相对顺序不受当前人影响；当前人的 k 就是他在这些人中的目标位置 |
| "如果数据流动态插入怎么办？" | 用 Order Statistic Tree（顺序统计树）维护，支持按 rank 插入，O(log N) |

---

### 举一反三

- **LeetCode 135. 分发糖果** — 双趟贪心，先满足一边再满足另一边
- **LeetCode 57. 插入区间** — 排序后插入的逻辑技巧
- **LeetCode 1846. 减小和重新排列数组后的最大元素** — 类似的"排序后逐个插入调整"思路

---

## 2) 面试技巧：Helm 包管理与 GitOps 实践

### 2.1 Helm 是什么？为什么需要它？

K8s 的原生 YAML 配置存在几个痛点：
- **重复配置**：每个环境（dev/staging/prod）都要维护一套几乎一样的 YAML
- **版本管理困难**：没有版本概念，回滚靠 `kubectl apply` 历史文件
- **依赖管理复杂**：一个应用可能依赖多个 K8s 资源，手动管理容易遗漏

**Helm = K8s 的包管理器（yum/apt for Kubernetes）**

核心概念：
```
Chart（包） → 一组 K8s 资源的模板集合
  ├── Chart.yaml      # 包的元数据（名称、版本、依赖）
  ├── values.yaml     # 默认值（环境相关配置）
  ├── templates/      # K8s 资源模板（用 Go template 语法）
  │   ├── deployment.yaml
  │   ├── service.yaml
  │   └── ingress.yaml
  └── charts/         # 依赖的子 Chart

Release（实例） → Chart 在集群中的一次部署
  ├── my-app-v1.2.0  (namespace: production)
  └── my-app-v1.3.0-rc1 (namespace: staging)
```

---

### 2.2 Helm Values 覆盖机制（面试高频）

Helm 支持多层 values 覆盖，优先级**从低到高**：

```
1. Chart 内置 values.yaml（默认值）
2. 父 Chart 的 values（如果是子 Chart）
3. 用户指定的 values 文件 (-f custom.yaml)
4. --set 命令行参数（最高优先级）
```

**实战示例：**
```bash
# 安装时覆盖镜像标签和副本数
helm install my-app ./my-chart \
  -f values-production.yaml \
  --set image.tag=v1.2.3 \
  --set replicaCount=5
```

**面试金句：**
> "Helm 的 values 覆盖遵循从通用到特定的原则，保证了环境配置的灵活性。生产中我们通常维护 `values-base.yaml` + `values-{env}.yaml` 两层结构，环境差异一目了然。"

---

### 2.3 Helm 升级与回滚

```bash
# 查看 release 历史
helm history my-app

# 升级（会自动做 3-way merge）
helm upgrade my-app ./my-chart --set image.tag=v1.2.4

# 回滚到版本 2
helm rollback my-app 2
```

**Helm 3 的重大改进：**
| 特性 | Helm 2 | Helm 3 |
|---|---|---|
| Tiller | 有（Server 端组件，需要集群权限） | ❌ 移除，纯客户端 |
| 权限模型 | Tiller 权限 = 所有 release 权限 | 用户本人权限，更安全 |
| 3-way Merge | ❌ | ✅ 升级时对比 live / last applied / current |
| 发布 Secret 存储 | ConfigMap | Secret（base64 编码，更安全） |

**面试追问 "为什么 Helm 3 去掉 Tiller？"**
> "Tiller 是 Helm 2 的安全瓶颈——它有集群 admin 权限，所有用户通过 Tiller 操作等于共享一个超级账号。Helm 3 去掉 Tiller 后，权限模型与 kubectl 一致，遵循 K8s RBAC，每个用户只用自己的权限操作，实现了真正的最小权限原则。"

---

### 2.4 GitOps 核心原则

**GitOps = 用 Git 作为基础设施和应用部署的唯一事实来源（Single Source of Truth）**

四大原则：
1. **声明式** — 用 YAML 描述期望状态，不是命令式脚本
2. **版本化 & 不可变** — Git 中的配置版本可追溯、可回滚
3. **自动拉取（Pull）** — 集群中的 Agent 自动检测 Git 变化并同步
4. **持续协调（Reconciliation）** — Agent 持续对比 Git 期望状态与集群实际状态，漂移自动修复

```
┌─────────────┐     push     ┌─────────────┐     pull      ┌─────────────┐
│  开发者      │ ───────────→ │  Git 仓库    │ ───────────→ │  K8s 集群    │
│ (修改 YAML)  │              │ (GitOps)     │   ArgoCD     │  (自动同步)   │
└─────────────┘              └─────────────┘              └─────────────┘
                                    ↑                          │
                                    └──────── 状态回写 ─────────┘
```

---

### 2.5 ArgoCD 工作原理

ArgoCD 是目前最流行的 GitOps 工具，核心架构：

```
┌──────────────────────────────────────────────┐
│                 ArgoCD Server                 │
│  ┌──────────┐  ┌──────────┐  ┌─────────────┐ │
│  │ API Server│  │ Repository│  │  Application│ │
│  │ (UI/CLI) │  │  Service  │  │  Controller │ │
│  └──────────┘  └──────────┘  └─────────────┘ │
└──────────────────────────────────────────────┘
         │                                    │
         ▼                                    ▼
   Git 仓库（期望状态）                K8s API Server（实际状态）
```

**核心流程：**
1. **Application** 定义：指向 Git 仓库路径 + 目标集群/Namespace
2. **Controller 轮询**（默认 3 分钟）或 **Webhook 触发**
3. 对比 Git 期望状态 vs K8s 实际状态（Diff）
4. **自动同步（Auto-Sync）**或手动触发 Sync
5. 同步完成后更新 Application 状态（回写到 Git 可选）

**ArgoCD 的三种同步策略：**

| 策略 | 行为 | 适用场景 |
|---|---|---|
| `Auto` | Git 变更后自动同步到集群 | 开发环境 |
| `Manual` | 需要人工在 UI/CLI 点击 Sync | 生产环境（需要审批） |
| `Prune` | 删除 Git 中已不存在的资源 | 配合 Auto 使用，防止孤儿资源 |

**面试高频：**
> "ArgoCD 怎么处理 Secret？" → 用 Sealed Secrets 或 External Secrets Operator，Git 中只存加密后的 SealedSecret，集群中自动解密为 K8s Secret。

---

### 2.6 部署策略对比

| 策略 | 原理 | 优点 | 缺点 | 工具 |
|---|---|---|---|---|
| **滚动更新** | 逐步替换旧 Pod，保持服务可用 | 简单、资源占用低 | 回滚慢、不能精确控制流量 | K8s 原生 |
| **蓝绿部署** | 同时部署两套环境，切流量 | 零停机、快速回滚 | 资源翻倍 | Argo Rollouts |
| **金丝雀** | 小比例流量到新版本，逐步放量 | 风险可控、可监控指标回滚 | 复杂、需要流量控制 | Argo Rollouts / Flagger |
| **A/B 测试** | 按用户维度分流（地域/设备/UID） | 精确灰度 | 最复杂 | Istio + Flagger |

**Argo Rollouts 金丝雀示例：**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
spec:
  strategy:
    canary:
      steps:
        - setWeight: 20          # 20% 流量到新版本
        - pause: {duration: 10m} # 观察 10 分钟
        - setWeight: 50
        - pause: {duration: 10m}
        - setWeight: 100         # 全量
      analysis:                  # 自动指标分析
        templates:
          - templateName: success-rate
        args:
          - name: service-name
            value: my-service
```

---

### 2.7 面试高频题速查

**Q1: Helm 和 Kustomize 怎么选？**
> "Helm 适合有发布、版本、依赖管理需求的应用（如中间件 chart），Kustomize 适合同一套 base YAML 多环境 patch 的场景。Kustomize 是 kubectl 原生支持的（`kubectl apply -k`），不需要额外工具，学习曲线更低。两者可以结合使用——用 Helm 管理第三方依赖，Kustomize 管理环境差异。"

**Q2: GitOps 和传统的 CI/CD（Jenkins/GitLab CI）有什么区别？**
> "传统 CI/CD 是 Push 模型——CI 服务器有集群凭据，主动 `kubectl apply` 到集群，存在权限集中和安全风险。GitOps 是 Pull 模型——集群内的 Agent 只读 Git，用自己的权限拉取并应用，权限分散更安全。而且 GitOps 天然具备状态回写和漂移检测能力，传统 CI/CD 很难做到。"

**Q3: 怎么保证 GitOps 的安全性？**
> "第一，Git 仓库的写权限严格限制，启用分支保护和代码审查；第二，Secret 不在 Git 中明文存储，用 Sealed Secrets 或 External Secrets；第三，ArgoCD 本身使用最小 RBAC 权限；第四，生产环境关闭 Auto-Sync，使用手动审批或 PR 审批流。"

**Q4: Helm 3-way merge 是什么？解决了什么问题？**
> "3-way merge 对比三个状态：Git 中的期望配置、上次部署记录、集群中的实际状态。解决的是'谁改了集群'的问题——如果运维用 kubectl patch 手动改了集群，Helm 2 的升级会覆盖掉这个改动；Helm 3 的 3-way merge 能检测到 live 状态的变化，避免意外覆盖。"

---

### 2.8 速记口诀

```
Helm 三板斧：chart install upgrade rollback
GitOps 四原则：声明式、版本化、自动拉、持续协调
部署四策略：滚动、蓝绿、金丝雀、A/B
安全三防线：Git 权限、Secret 加密、Auto-Sync 关闭
```

---

## 3) 今日打卡

✅ **算法**：根据身高重建队列 — 贪心排序+插入，经典"先处理高的"思路  
✅ **面试**：Helm 包管理、GitOps 四原则、ArgoCD 工作原理、四种部署策略对比  

---

> 📅 **明日预告**：Day 83 — K8s 存储体系（PV/PVC/StorageClass）+ 算法题  
> 🐳 **本周主题**：云原生与容器化基础设施
