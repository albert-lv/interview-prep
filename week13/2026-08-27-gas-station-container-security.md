# Day 84 — 加油站 + 容器安全与云原生可观测性

> 今日主题：贪心绕环判定 + 云原生安全与可观测性三支柱
> Week 13 · 2026-08-27

---

## 🧩 今日算法题：加油站（Gas Station）

**题目链接**：LeetCode 134 — [Gas Station](https://leetcode.com/problems/gas-station/)

### 题目描述

在一条环路上有 `n` 个加油站，其中第 `i` 个加油站有汽油 `gas[i]` 升。

你有一辆油箱容量无限的车，从第 `i` 个加油站开往第 `i+1` 个加油站需要消耗汽油 `cost[i]` 升。你从其中的一个加油站出发，开始时油箱为空。

给定两个整数数组 `gas` 和 `cost`，如果你可以按顺序绕环路行驶一周，则返回出发时加油站的编号，否则返回 `-1`。如果存在解，则**它是唯一的**。

**示例 1**：
```
输入: gas = [1,2,3,4,5], cost = [3,4,5,1,2]
输出: 3
解释:
  从 3 号加油站(索引 3)出发，可获得 4 升汽油。油箱 = 0 + 4 = 4
  开往 4 号站，消耗 1 升。油箱 = 4 - 1 = 3
  开往 0 号站，消耗 2 升。油箱 = 3 - 2 = 1
  开往 1 号站，消耗 3 升。油箱 = 1 - 3 = -2 → 等等，不对，重新算：
  
  实际上从 3 出发：
  油箱 = 0 + gas[3] = 4
  3→4: 油箱 = 4 - cost[3] + gas[4] = 4 - 1 + 5 = 8
  4→0: 油箱 = 8 - cost[4] + gas[0] = 8 - 2 + 1 = 7
  0→1: 油箱 = 7 - cost[0] + gas[1] = 7 - 3 + 2 = 6
  1→2: 油箱 = 6 - cost[1] + gas[2] = 6 - 4 + 3 = 5
  2→3: 油箱 = 5 - cost[2] + gas[3]... 不，2→3 消耗 cost[2]=5，油箱 = 5 - 5 = 0，到达终点。
  能绕一圈，返回 3。
```

**示例 2**：
```
输入: gas = [2,3,4], cost = [3,4,3]
输出: -1
解释: 总油 2+3+4=9，总消耗 3+4+3=10，无法完成。
```

**约束条件**：
- `n == gas.length == cost.length`
- `1 <= n <= 10^5`
- `0 <= gas[i], cost[i] <= 10^4`

---

### 💡 解题思路

#### 第一步：全局可行性判断（必要条件）

如果所有加油站的油量之和 < 总消耗量，那么无论从哪里出发都不可能完成一圈。这是**必要条件**。

```
if sum(gas) < sum(cost): return -1
```

#### 第二步：贪心找起点

这个题的核心观察非常巧妙：

> 如果从站点 `i` 出发，走到站点 `j` 时油箱变负了，那么 **i 到 j 之间的任何一个站点都不可能作为起点**。

**为什么？** 因为从 `i` 出发时油箱是 0（最小），如果到 `j` 时不够了，说明从 `i` 到 `j-1` 这段路积累的"净油量"（gas[k] - cost[k]）都是正的或零，把这些净油量加到任何一个中间站点上，只会让该站点出发时的初始油量更少，不可能撑得更远。

所以一旦在 `j` 失败，直接把起点跳到 `j+1`，重新开始累积。

#### 算法流程

1. 遍历每个站点，维护 `current_tank`（当前油箱油量）
2. 如果 `current_tank + gas[i] - cost[i] < 0`，说明从当前起点到 `i` 都不行，起点设为 `i+1`，`current_tank` 归零
3. 同时维护 `total_tank`，判断是否全局可行
4. 最后如果 `total_tank >= 0`，返回记录的起点

---

### 📝 代码实现

**Go 实现**：

```go
package main

func canCompleteCircuit(gas []int, cost []int) int {
    n := len(gas)
    totalTank := 0  // 总油量 - 总消耗，用于判断全局可行性
    currTank := 0   // 当前油箱，用于找起点
    start := 0      // 候选起点
    
    for i := 0; i < n; i++ {
        diff := gas[i] - cost[i]
        totalTank += diff
        currTank += diff
        
        // 如果当前油箱不够到达下一站
        if currTank < 0 {
            // i 及之前的所有站点都不能作为起点
            start = i + 1
            currTank = 0
        }
    }
    
    if totalTank < 0 {
        return -1
    }
    return start
}
```

**Python 实现**：

```python
class Solution:
    def canCompleteCircuit(self, gas: list[int], cost: list[int]) -> int:
        total_tank = curr_tank = 0
        start = 0
        
        for i in range(len(gas)):
            diff = gas[i] - cost[i]
            total_tank += diff
            curr_tank += diff
            
            if curr_tank < 0:
                start = i + 1
                curr_tank = 0
        
        return start if total_tank >= 0 else -1
```

---

### ⏱ 复杂度分析

| 维度 | 复杂度 | 说明 |
|---|---|---|
| **时间** | O(n) | 一次线性遍历 |
| **空间** | O(1) | 只使用常数额外空间 |

---

### 🎯 面试官追问

**Q1：为什么起点唯一？**
> 如果 `sum(gas) >= sum(cost)`，说明全局可行。假设有两个可行起点 `a` 和 `b`，从 `a` 能走到 `b`，说明 `a` 到 `b` 的净油量 ≥ 0。但从 `b` 也能走回 `a`，说明 `b` 到 `a` 的净油量也 ≥ 0。两段的净油量之和 = 总净油量 ≥ 0。如果两段都 ≥ 0 且总和 ≥ 0，那么从 `a` 出发时经过 `b` 点的油量 ≥ 从 `b` 出发时的初始油量（0），所以 `a` 经过 `b` 后不会失败，这意味着 `b` 不可能是独立的新起点——矛盾。所以唯一。

**Q2：如果油箱容量有限呢？**
> 增加一个容量限制 `C`。在遍历时，除了判断 `currTank < 0`，还要判断 `currTank > C` 时截断为 `C`。如果某段路径上需要的油量峰值超过 `C`，则该起点不可行。这时 greedy 不再适用，可能需要 O(n²) 枚举 + 滑动窗口优化。

**Q3：如果允许多次加油（不是环，是线段，中途可以停任意站）？**
> 那就变成经典 DP 或贪心问题：每一步选净收益最大的下一站，或者直接用 dijkstra 最短路。但如果是线段且必须经过所有站，那就是原题的变体了。

---

## 🛡️ 面试技巧：容器安全与云原生可观测性

### 一、容器安全四维度

云原生安全不是"把防火墙搬到容器里"，而是**分层防御**。

#### 1. 镜像安全（最源头）

| 风险 | 防护手段 |
|---|---|
| 基础镜像有 CVE 漏洞 | 用 `trivy`/`clair`/`snyk` 扫描镜像，CI 阶段阻断高危漏洞 |
| 镜像过大，攻击面大 | 用 distroless / alpine / scratch 最小化镜像 |
| 敏感信息硬编码在镜像里 | **绝对不要**！用 Secret / Vault / 环境变量注入 |
| 使用 `latest` tag | 固定版本号，可追踪可回滚 |

**面试金句**：
> "镜像安全是供应链安全的第一环。我们在 CI 里做了三层检查：Dockerfile 合规检查（不用 root、不装无用包）→ 镜像构建 → 漏洞扫描，高危阻断，中危告警。"

#### 2. 运行时安全（Pod / 容器）

```yaml
# Pod Security Context 示例
apiVersion: v1
kind: Pod
spec:
  securityContext:
    runAsNonRoot: true        # 禁止 root 运行
    runAsUser: 1000           # 指定 UID
    fsGroup: 2000             # 卷挂载的组权限
    seccompProfile:
      type: RuntimeDefault    # 系统调用过滤
  containers:
  - name: app
    securityContext:
      allowPrivilegeEscalation: false   # 禁止提权
      readOnlyRootFilesystem: true      # 根文件系统只读
      capabilities:
        drop: ["ALL"]                   # 丢弃所有 capabilities
```

**关键配置记忆法**：
- `runAsNonRoot` — 别用 root
- `readOnlyRootFilesystem` — 根盘只读，写数据用 volume
- `allowPrivilegeEscalation: false` — 禁止 sudo 那一套
- `capabilities: drop: [ALL]` — 最小权限原则

#### 3. 网络安全（东西向流量）

```yaml
# NetworkPolicy：只允许 frontend 访问 backend 的 8080 端口
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
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

**面试常问**：
> "K8s 默认网络是全部互通的，这在生产环境极其危险。NetworkPolicy 相当于给 Pod 之间加了防火墙，但只有装了 CNI 插件（如 Calico、Cilium）才真正实现隔离。Flannel 不支持 NetworkPolicy。"

#### 4. 访问控制（RBAC）

```yaml
# 最小权限 RBAC 示例
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: deploy-reader
rules:
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list"]    # 只能看，不能改
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: deploy-reader-binding
subjects:
- kind: User
  name: "developer@company.com"
roleRef:
  kind: Role
  name: deploy-reader
```

**RBAC 核心原则**：
- 不给 `cluster-admin`，给 `Role` 而非 `ClusterRole`
- ServiceAccount 一个应用一个，不共享
- 定期审计：`kubectl auth can-i --list`

---

### 二、云原生可观测性三支柱

> **Metrics + Logs + Traces = 可观测性**

不是三个都装了就叫可观测，而是**能从外部信号推断内部状态**。

#### 1. Metrics（指标）— Prometheus 体系

```yaml
# ServiceMonitor：让 Prometheus 自动发现监控目标
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: app-metrics
spec:
  selector:
    matchLabels:
      app: my-app
  endpoints:
  - port: metrics
    interval: 15s
    path: /metrics          # 应用暴露的指标端点
```

**Metrics 类型速记**：

| 类型 | 含义 | 示例 |
|---|---|---|
| Counter | 只增不减的累计值 | HTTP 请求总数、错误总数 |
| Gauge | 可增可减的瞬时值 | 当前内存用量、队列长度 |
| Histogram | 采样分布 | 请求延迟分布（P50/P95/P99） |
| Summary | 预计算分位数 | 客户端计算好的 P99 延迟 |

**SRE 黄金信号**（面试必背）：
1. **Latency** — 延迟（不是平均延迟，是 P99）
2. **Traffic** — 流量（QPS / 并发数）
3. **Errors** — 错误率（4xx / 5xx 比例）
4. **Saturation** — 饱和度（CPU / 内存 / 连接数利用率）

#### 2. Logs（日志）— Loki / ELK 体系

**日志收集架构**：
```
App stdout/stderr → Fluent Bit (DaemonSet 采集) → Loki / Kafka → Grafana 查询
```

**面试要点**：
- **结构化日志**（JSON）比文本好，可索引、可过滤
- **日志分级**：ERROR 级别以上自动告警，WARN 聚合看趋势
- **日志采样**：高 QPS 服务做尾部采样（Tail-based Sampling），别全量存
- **敏感信息脱敏**：手机号、身份证号打码后再入库

#### 3. Traces（链路追踪）— Jaeger / Tempo / SkyWalking

```go
// OpenTelemetry 埋点示例（伪代码）
ctx, span := tracer.Start(ctx, "process-order")
defer span.End()

span.SetAttributes(
    attribute.String("order.id", orderID),
    attribute.Int64("order.amount", amount),
)

// 下游调用时把 trace context 传递下去
resp, err := httpClient.Do(req.WithContext(ctx))
```

**Trace 核心概念**：
- **Trace** = 一次完整请求的树形链路
- **Span** = 链路中的一个节点（一个服务调用）
- **Baggage** = 跨服务传递的上下文（如 userID、tenantID）

**面试常问**：
> "Trace 不是越多越好，采样率要调。开发环境 100%，生产环境 1%-10% 头部采样或尾部采样。Jaeger 的 Adaptive Sampling 可以根据 QPS 自动调整。"

---

### 三、可观测性落地 Checklist

```
□ Metrics：应用暴露 /metrics，Prometheus 抓取，Grafana  dashboard
□ Logs：结构化 JSON，分级（INFO/WARN/ERROR），Loki 收集
□ Traces：OpenTelemetry 埋点，跨服务传递 traceparent header
□ Alerts：P99 延迟 > 500ms、错误率 > 1%、CPU > 80% 持续 5min
□ Dashboard：SLA 看板、黄金信号看板、业务指标看板
□ 日志和指标关联：同一个请求ID在日志和trace里都能查到
```

---

### 四、面试高频 Q&A

**Q：容器逃逸是什么？怎么防？**
> 容器逃逸是指进程突破容器边界访问宿主机资源。防护：1) 不用 `--privileged`；2) 限制 capabilities；3) AppArmor / SELinux 强制访问控制；4) 只读 rootfs；5) 非 root 运行。

**Q：Prometheus 单点怎么解决？**
> 1) Prometheus Federation 分层抓取；2) Thanos / Cortex 做全局查询和长期存储；3) 告警用 Alertmanager 集群模式；4) 本地 SSD + remote write 到对象存储。

**Q：Loki 和 ELK 怎么选？**
> Loki 轻量、只索引标签不索引日志内容、Grafana 原生集成好，适合 Kubernetes 云原生场景。ELK 功能全、索引能力强，但资源消耗大。我们选 Loki + Grafana，因为日志量太大，全文索引成本扛不住。

**Q：什么是 eBPF？跟可观测性有什么关系？**
> eBPF 是 Linux 内核的可编程钩子，可以无侵入地采集内核事件（网络包、系统调用、文件操作）。Cilium 用 eBPF 做网络策略和可观测性，比 iptables 性能高很多。Pixie / Falco 也基于 eBPF 做安全监控。

---

## 📝 今日小结

| 项目 | 要点 |
|---|---|
| **算法** | 加油站贪心：全局可行性 + 局部失败直接跳到下一站，O(n) O(1) |
| **安全** | 镜像扫描 → 运行时安全上下文 → NetworkPolicy → RBAC 四层防御 |
| **可观测性** | Metrics(Prometheus) + Logs(Loki) + Traces(Jaeger) 三支柱 |
| **面试话术** | "我们在 CI 里做了镜像漏洞扫描，高危阻断；运行时用了 SecurityContext 最小权限；监控用了 SRE 黄金信号做告警。" |

---

> 📅 明日预告：Week 14 开启 —— 云原生收尾 + 进阶主题 🚀
