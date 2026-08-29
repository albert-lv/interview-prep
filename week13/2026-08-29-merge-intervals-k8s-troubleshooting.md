# Day 86 — 合并区间 + K8s 生产环境排障体系与 SRE 实践

> 📅 2026-08-29 · Week 13 Day 7 · 云原生与容器化基础设施 🐳

---

## 今日算法题：合并区间（Merge Intervals）

### 题目描述

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [start_i, end_i]`。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

**示例 1：**
```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠，将它们合并为 [1,6]。
```

**示例 2：**
```
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间（边界接触也算重叠）。
```

**约束：**
- `1 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= start_i <= end_i <= 10^4`

---

### 解题思路

**核心观察**：两个区间能合并，当且仅当它们有重叠（包括边界接触）。

**贪心策略**：
1. 先按区间左端点 **升序排序**
2. 维护一个当前合并中的区间 `current`
3. 遍历每个区间：
   - 如果当前区间左端点 ≤ `current` 右端点 → **可以合并**，扩展 `current` 的右端点
   - 否则 → **无法合并**，将 `current` 加入结果，开启新区间

**为什么排序后贪心有效？**

排序后，任何可能与当前区间重叠的区间，必定紧跟在当前区间之后（左端点有序）。不存在"跳过"的情况，所以一次线性扫描即可。

---

### 代码实现

```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    if not intervals:
        return []
    
    # 按左端点升序排序 —— O(n log n)
    intervals.sort(key=lambda x: x[0])
    
    result = []
    current_start, current_end = intervals[0]
    
    for start, end in intervals[1:]:
        if start <= current_end:  # 重叠或接触，合并
            current_end = max(current_end, end)
        else:  # 不重叠，结算当前区间
            result.append([current_start, current_end])
            current_start, current_end = start, end
    
    # 别漏了最后一个
    result.append([current_start, current_end])
    return result
```

**Go 版本：**

```go
func merge(intervals [][]int) [][]int {
    if len(intervals) == 0 {
        return nil
    }
    
    // 按左端点排序
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })
    
    var res [][]int
    curStart, curEnd := intervals[0][0], intervals[0][1]
    
    for i := 1; i < len(intervals); i++ {
        start, end := intervals[i][0], intervals[i][1]
        if start <= curEnd { // 重叠，合并
            if end > curEnd {
                curEnd = end
            }
        } else { // 不重叠，结算
            res = append(res, []int{curStart, curEnd})
            curStart, curEnd = start, end
        }
    }
    res = append(res, []int{curStart, curEnd})
    return res
}
```

---

### 复杂度分析

| 维度 | 复杂度 | 说明 |
|---|---|---|
| **时间** | O(n log n) | 排序主导，扫描 O(n) |
| **空间** | O(log n) ~ O(n) | 排序栈空间 / 结果存储 |

> 如果输入已经有序，可以做到 O(n) 时间 —— 面试时可以提这个优化点。

---

### 面试官会怎么问？

**Q1：如果区间数量很大（10^8 级别），内存放不下怎么办？**

> 采用**外部排序**先对左端点排序，然后用**多路归并**的思路一次加载部分区间到内存处理。或者如果数据已经在磁盘上有序，可以直接流式处理 O(n)。

**Q2：合并区间和 K8s 的 Pod 调度有什么关系？**

> 有意思的联系：K8s 调度器在分配资源时，可以把节点上的可用资源看作"区间"，多个 Pod 的资源请求需要"填入"这些区间。合并连续可用资源区间是调度优化的子问题之一。

**Q3：能否用并查集做？**

> 可以，将重叠的区间归到同一集合，最后每个集合取最小左端点和最大右端点。时间 O(n² α(n)) 或优化到 O(n log n)，但代码更复杂，不如排序贪心直观。

**Q4：边界接触 `[1,4]` 和 `[4,5]` 算重叠吗？**

> 题目要求算。如果面试官说"不算"，把 `<=` 改成 `<` 即可。问清楚需求是面试加分项。

---

## 面试技巧：K8s 生产环境排障体系与 SRE 实践

> Week 13 收官篇 —— 前面学了 Docker/K8s 原理、网络、存储、调度、CI/CD，今天讲**出事了怎么办**。

---

### 一、K8s 故障分层诊断模型

生产排障和算法题一样，**先分类，再定位**。

```
┌─────────────────────────────────────────────────────────┐
│                    用户层 (现象层)                        │
│         服务 502 / 响应慢 / 功能异常 / 数据不一致            │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│                    应用层 (Pod/Container)                 │
│    CrashLoopBackOff / OOMKilled / 探针失败 / 资源耗尽      │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│                    调度层 (K8s 控制面)                     │
│    Pending / Unschedulable / 节点 NotReady / 网络不通      │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│                    基础设施层 (Node/网络/存储)              │
│    磁盘满 / 内存压力 / 网络分区 / CNI 故障 / 存储脱连      │
└─────────────────────────────────────────────────────────┘
```

**面试话术**："我排障习惯从现象出发逐层下钻，先确认是应用问题还是平台问题，避免在错误层面浪费時間。"

---

### 二、Pod 异常状态速查表

| 状态 | 含义 | 常见原因 | 排查命令 |
|---|---|---|---|
| `CrashLoopBackOff` | 容器反复崩溃重启 | 启动命令错误、依赖服务未就绪、配置缺失 | `kubectl logs --previous` |
| `OOMKilled` | 内存超限被杀死 | 内存 limit 设太低、内存泄漏、突发流量 | `kubectl describe pod` 看 Events |
| `ImagePullBackOff` | 镜像拉取失败 | 镜像不存在、权限不足、网络不通 | `kubectl describe pod` |
| `Pending` | 调度失败 | 资源不足、节点污点、亲和性限制、PV 未绑定 | `kubectl describe pod` 看 Events |
| `Evicted` | 被驱逐 | 节点磁盘/内存压力、污点驱逐 | `kubectl get pod -o wide` |
| `Terminating` | 卡在终止中 | Finalizer 未完成、Volume 未卸载、PreStop 挂死 | `kubectl get pod -w` |

**面试金句**：`kubectl describe` 是排障的第一把刀，Events 里藏着 80% 的答案。

---

### 三、排障标准 SOP（五步法）

#### Step 1：快速止血（1 分钟内）

```bash
# 1. 查看 Pod 状态快照
kubectl get pods -A -o wide | grep -E "Error|Crash|Pending|Evicted"

# 2. 节点健康检查
kubectl get nodes -o wide

# 3. 核心组件状态
kubectl get componentstatuses  # 或检查 kube-system Pod
```

**原则**：先恢复服务，再根因分析。能回滚就回滚，能扩容就扩容。

#### Step 2：日志定位（2 分钟内）

```bash
# 当前日志
kubectl logs <pod> -f

# 上一次崩溃日志（CrashLoopBackOff 必用）
kubectl logs <pod> --previous

# 多副本聚合日志（配合 loki/fluentd 等）
kubectl logs -l app=frontend --tail=100 | grep ERROR
```

#### Step 3：事件分析（3 分钟内）

```bash
# Pod 生命周期事件（调度/拉镜像/启动/健康检查）
kubectl describe pod <pod>

# 节点事件（压力驱逐、磁盘满等）
kubectl describe node <node>

# 全集群事件按时间排序
kubectl get events --sort-by='.lastTimestamp' | tail -50
```

**关键 Event 关键词**：`FailedScheduling`、`FailedMount`、`Unhealthy`、`Killing`、`Evicted`。

#### Step 4：资源与性能

```bash
# Pod 资源使用
kubectl top pod -A

# 节点资源
kubectl top node

# 进入容器内部排查
kubectl exec -it <pod> -- /bin/sh

# 查看资源配额限制
kubectl get resourcequota -n <namespace>
```

#### Step 5：根因归档

```bash
# 导出 Pod 完整信息归档
kubectl get pod <pod> -o yaml > pod-debug-$(date +%s).yaml
kubectl describe pod <pod> > pod-describe-$(date +%s).txt
```

> 面试加分项：提到用 `kubectl debug`（ephemeral containers）在不重启 Pod 的情况下排障，K8s 1.18+ 特性。

---

### 四、高频面试连环问

**Q：Pod 一直处于 Pending 状态，怎么排查？**

> 三层检查：
> 1. `kubectl describe pod` 看 Events，调度器会说明为什么 Pending（资源不足/污点/亲和性）
> 2. `kubectl get nodes -o yaml` 看节点 allocatable 资源和已有 Pod 占用
> 3. 检查 PVC 是否已绑定（`kubectl get pvc`），StorageClass 动态供给是否正常

**Q：服务间歇性 502，怎么定位？**

> 排查路径：Ingress → Service → Pod → 应用
> 1. `kubectl get endpoints` 确认后端 Pod IP 是否正确注册
> 2. 检查 readinessProbe 配置，Pod 未就绪就被加入 endpoints 是常见原因
> 3. 看 Ingress Controller 日志（nginx/traefik），可能是 upstream 超时
> 4. 应用层抓包或链路追踪（Jaeger/Zipkin）确认具体请求卡在哪

**Q：节点变成 NotReady 了怎么办？**

> 1. SSH 上节点，`systemctl status kubelet`
> 2. 检查节点资源：`df -h`（磁盘）、`free -h`（内存）、`systemctl status docker/containerd`
> 3. 看 kubelet 日志：`journalctl -u kubelet -f`
> 4. 常见原因：磁盘压力（Pod 日志写满）、内存压力（OOM 频繁）、PLEG 超时（容器运行时卡死）

**Q：怎么防止同类故障再次发生？**

> 从"止血→定位→预防"闭环回答：
> - 监控告警：Prometheus + Alertmanager，核心指标（Pod 重启率、节点 NotReady、Ingress 5xx 率）
> - 资源保障：设置合理的 request/limit，配置 HPA/VPA
> - 健康检查：完善的 liveness/readiness/startup probe
> - 混沌工程：Chaos Mesh 定期注入故障验证恢复能力
> - 预案演练：定期 runbook 演练，保证 on-call 工程师熟悉 SOP

---

### 五、SRE 黄金实践速查

| 实践 | 具体做法 | 工具/机制 |
|---|---|---|
| **可观测性三支柱** | Metrics + Logs + Traces 缺一不可 | Prometheus + Loki + Jaeger |
| **告警分级** | P0（立即处理）/ P1（30min）/ P2（2h）/ P3（次日） | Alertmanager 路由 |
| **错误预算** | 月度可用性目标（如 99.9%）→ 允许 43.8min 停机 | SLO + Burn Rate 告警 |
| **on-call 轮值** | 明确 primary/secondary，交接班记录 | PagerDuty / OpsGenie |
| **Runbook 文化** | 每个告警配一份排查手册，不依赖个人经验 | 内部 Wiki / GitBook |
| **事后复盘（Post-mortem）** | 无责备文化，聚焦系统改进，产出 action items | Google SRE 模板 |

---

### 六、一句话速答模板

| 问题 | 一句话 |
|---|---|
| K8s 排障第一步？ | `kubectl get pods` + `kubectl describe` 看 Events |
| CrashLoopBackOff 怎么查？ | `--previous` 日志 + 检查启动命令和依赖就绪 |
| OOMKilled 怎么解决？ | 调大 limit 或排查内存泄漏，不是简单加内存 |
| 节点 NotReady 最常见原因？ | 磁盘满、kubelet 挂、PLEG 超时 |
| 怎么保证故障快速恢复？ | 健康探针 + HPA + 预案演练 + 混沌工程 |

---

## 今日小结

| 项目 | 内容 |
|---|---|
| **算法题** | 合并区间 —— 排序+贪心，O(n log n)，注意边界接触也算重叠 |
| **核心技巧** | 区间问题先排序，维护当前合并区间，一次扫描出结果 |
| **面试技巧** | K8s 排障五步法（止血→日志→事件→资源→归档），Pod 异常状态速查 |
| **SRE 要点** | 可观测性三支柱、错误预算、无责备复盘文化 |

---

> 🐳 Week 13 云原生与容器化基础设施 已完结。从 Docker 核心原理到 K8s 网络/存储/调度/安全/可观测性/CI/GitOps，再到今天的生产排障，覆盖了一套完整的云原生技术栈。
>
> 明天开始 **Week 14** —— 敬请期待新主题 🔥
