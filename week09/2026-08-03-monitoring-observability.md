# Day 58：监控告警与可观测性（Monitoring, Alerting & Observability）

> **日期**: 2026-08-03  
> **主题**: 系统架构与工程实践 — 可观测性三大支柱 + 告警策略设计  
> **难度**: 🔥🔥🔥🔥（架构设计题高频考点）

---

## 📝 今日核心：可观测性三大支柱

面试里被问到"你们系统怎么监控的"，答不上来三大支柱，面试官直接扣印象分。

| 支柱 | 回答关键词 | 代表工具 |
|------|-----------|---------|
| **Metrics（指标）** | 时序数据、聚合、趋势、QPS/RT/ErrorRate | Prometheus + Grafana |
| **Logs（日志）** | 结构化日志、检索、关联 TraceID | ELK / Loki / Fluentd |
| **Traces（链路）** | 分布式追踪、Span、采样、依赖拓扑 | Jaeger / Zipkin / SkyWalking |

---

## 1. Metrics：Prometheus 监控体系

### 核心数据模型

```
metric_name{label1="value1", label2="value2"}  value  timestamp
```

**四种指标类型（面试必问）**：

| 类型 | 含义 | 经典场景 |
|------|------|---------|
| **Counter** | 单调递增计数器（只能加） | 请求总数、错误总数 |
| **Gauge** | 可增可减的瞬时值 | CPU 使用率、内存占用、连接数 |
| **Histogram** | 采样分布，分 bucket 统计 | 请求延迟 P50/P99、响应大小 |
| **Summary** | 预计算分位数（客户端算） | 和 Histogram 类似，但计算在 client 端 |

### PromQL 高频查询

```promql
# 1. 计算 QPS（rate = 每秒变化率）
rate(http_requests_total[5m])

# 2. 计算错误率
rate(http_requests_total{status=~"5.."}[5m]) 
  / rate(http_requests_total[5m])

# 3. P99 延迟（histogram_quantile 必考）
histogram_quantile(0.99, 
  rate(http_request_duration_seconds_bucket[5m]))

# 4. 环比变化（和 1h 前比）
(avg(cpu_usage) - avg(cpu_usage offset 1h)) 
  / avg(cpu_usage offset 1h)
```

### Prometheus 架构要点

```
┌─────────────┐     pull      ┌─────────────┐
│  Application │ ────────────> │  Prometheus │
│  (/metrics)  │  15s间隔     │  Server     │
└─────────────┘              └──────┬──────┘
                                    │
                              ┌─────▼─────┐
                              │  Grafana  │
                              │ (Dashboard)│
                              └───────────┘
```

**面试常挖坑的点**：
- **Pull vs Push**：Prometheus 是 pull 模式，Pushgateway 用于短生命周期任务
- **HA 方案**：Thanos / Cortex / VictoriaMetrics 解决单机存储瓶颈
- **Cardinality 爆炸**：label 值无限制增长会导致内存爆炸，需要控制基数

---

## 2. Logs：日志系统设计与 ELK/Loki

### 日志系统设计三要素

1. **采集** → Filebeat / Fluentd / Promtail
2. **存储** → Elasticsearch / Loki / ClickHouse
3. **查询** → Kibana / Grafana Explore

### 结构化日志（必推实践）

```json
{
  "timestamp": "2026-08-03T14:30:00Z",
  "level": "ERROR",
  "trace_id": "abc123def456",
  "span_id": "span789",
  "service": "order-service",
  "message": "支付回调处理失败",
  "error": "timeout after 30s",
  "user_id": 10086,
  "order_id": "ORD-20260803-001"
}
```

**为什么必须结构化？**
- 模糊搜索 → 字段精确过滤
- 日志关联 → 通过 `trace_id` 串起整条链路
- 告警触发 → 对 `level: ERROR` 做聚合告警

### ELK vs Loki 选型对比

| 维度 | ELK Stack | Grafana Loki |
|------|-----------|--------------|
| 存储 | Elasticsearch（倒排索引，资源重） | 对象存储 + 轻量索引（成本低） |
| 查询 | 极强，支持复杂聚合 | 够用，LogQL 类似 PromQL |
| 资源占用 | 高（内存大户） | 低（省 90% 存储成本不是吹的） |
| 生态 | 成熟，历史悠久 | 云原生，和 Prometheus 深度整合 |

**面试话术**："中小规模用 ELK 没问题，但日志量大、成本敏感的场景，Loki + S3 是更云原生的选择。"

---

## 3. Traces：分布式链路追踪

### OpenTelemetry 标准（当下主流）

```
Trace（一次完整请求）
  └── Span A（service: gateway, 耗时 120ms）
        └── Span B（service: user-service, 耗时 80ms）
              └── Span C（service: mysql, 耗时 50ms）
        └── Span D（service: order-service, 耗时 30ms）
```

**Trace 核心概念**：
- **TraceID**：全局唯一，串起所有 Span
- **SpanID**：单个操作单元
- **ParentSpanID**：构建树形关系
- **Baggage**：跨服务传递的上下文（如 user_id）

### 采样策略（大厂必问）

| 策略 | 说明 | 适用 |
|------|------|------|
| **头部采样** | 请求入口处决定是否采样 | 简单，但可能漏掉关键下游 |
| **尾部采样** | 等整条链路完成后，按条件（如耗时>1s / 有错误）决定是否保留 | 精准保留异常链路，推荐 |
| **概率采样** | 固定比例（如 1%） | 流量极大时的兜底方案 |

### 链路追踪的实际价值

不只是"看调用链"，而是：
- **定位慢请求**：一眼看出哪个服务拖后腿
- **依赖拓扑**：自动生成服务调用图，发现循环依赖
- **性能回归**：对比发布前后的链路耗时

---

## 4. SRE 黄金信号（Golden Signals）

Google SRE 定义的四个核心监控维度，**系统设计题答这个 = 加分项**：

| 信号 | 含义 | 典型指标 |
|------|------|---------|
| **Latency（延迟）** | 服务处理请求的时间 | P50 / P99 响应时间 |
| **Traffic（流量）** | 系统的需求量 | QPS / 并发连接数 |
| **Errors（错误）** | 失败请求比例 | HTTP 5xx 率 / 业务错误率 |
| **Saturation（饱和度）** | 资源接近满载的程度 | CPU / 内存 / 磁盘 / 连接池使用率 |

**面试延伸**：
- RED 方法（Rate, Errors, Duration）→ 面向微服务
- USE 方法（Utilization, Saturation, Errors）→ 面向资源

---

## 5. 告警策略设计（Alarm Design）

### 告警设计的核心原则

```
好的告警 =  actionable（可执行）+  timely（及时）+  relevant（相关）
```

**反模式（面试可以吐槽）**：
- ❌ "CPU 超过 80% 就告警" → 凌晨 3 点吵醒人，但可能不需要立刻处理
- ❌ 同样的故障触发 50 条告警 → 告警风暴，没人看了
- ❌ 只有 ERROR 日志告警 → 没有趋势预判，总是事后诸葛亮

### 告警分级（P0/P1/P2）

| 级别 | 触发条件 | 响应要求 | 通知方式 |
|------|---------|---------|---------|
| **P0（紧急）** | 核心服务不可用 / 错误率飙高 / 数据丢失 | 5 分钟内响应 | 电话 + 短信 + 群 |
| **P1（高优）** | 非核心功能降级 / 性能显著下降 | 30 分钟内响应 | 短信 + 群 |
| **P2（一般）** | 资源预警 / 容量即将触顶 | 工作时间内处理 | 群通知 |
| **P3（低优）** | 趋势性异常 / 待优化项 | 排期处理 | 邮件 / 周报 |

### 告警收敛策略

1. **去重**：同样的告警在 5 分钟内只发一次
2. **聚合**：同一服务的多个相关告警合并成一条
3. **抑制**：高优先级告警自动屏蔽低优告警
4. **升级**：15 分钟无人认领，自动升级给上级

### 异常检测（进阶）

- **静态阈值**：简单但容易误报（周末流量天然低）
- **同环比**：和昨天/上周同时段比，> 30% 波动触发
- **动态基线**：机器学习拟合正常曲线（如 3σ 原则、Prophet 时序预测）

---

## 6. 面试真题：设计一个监控系统

### 题目描述

> 设计一个监控告警系统，支持采集多个服务的 Metrics，提供 Dashboard 展示，并能在异常时触发告警通知。

### 回答框架（分层展开）

**Step 1：数据采集层**
- 服务通过 SDK / Agent 暴露 `/metrics` 端点（Prometheus 格式）
- 支持 Pull（Prometheus 定期拉取）和 Push（Gateway 用于批处理任务）
- 指标类型：Counter、Gauge、Histogram、Summary

**Step 2：存储层**
- 时序数据库（TSDB）存储指标，按时间 + 标签索引
- 日志存储用 Loki / S3，降低存储成本
- Trace 数据量极大，必须采样存储（尾部采样保留异常链路）

**Step 3：计算与查询层**
- PromQL / LogQL 提供灵活查询
- 预聚合常用指标（如 1 分钟粒度的 QPS）加速查询
- 分冷热数据：热数据保留 7 天，冷数据归档到对象存储

**Step 4：可视化层**
- Grafana 绑定 Prometheus / Loki / Jaeger 数据源
- Dashboard 按服务、环境、集群分层组织
- 关键页面：服务总览、错误分析、链路拓扑

**Step 5：告警引擎**
- 规则配置：阈值、同环比、动态基线
- 告警收敛：去重、聚合、抑制、升级
- 通知渠道：PagerDuty / 钉钉 / 企业微信 / 电话

**Step 6：高可用设计**
- 监控自身必须高可用 → Prometheus 多副本 + Thanos 联邦
- 告警通道不能单点 → 多通道备份（钉钉挂了走短信）
- 存储水平扩展 → 分片 + 对象存储后端

### 加分回答点

- "**监控的监控**"：如果监控系统自己挂了，谁来告警？→ 交叉监控、外部探活
- "**可观测性不是可监控性**"：Monitoring 告诉你系统是否工作，Observability 帮你回答"为什么"
- **eBPF 无侵入采集**：新兴技术，内核层面采集指标，无需改代码

---

## 💡 面试技巧

### 技巧 1：答"你们怎么监控的"时，先丢框架再展开

❌ 烂回答："我们用 Prometheus 加 Grafana"

✅ 好回答：
> "我们的可观测性体系基于三大支柱：
> 1. **Metrics** 用 Prometheus 采集，Grafana 做大盘，关注 SRE 黄金信号；
> 2. **Logs** 用 Loki 做结构化日志收集，通过 TraceID 关联链路；
> 3. **Traces** 用 Jaeger 做分布式追踪，尾部采样保留异常链路。
> 告警按 P0/P1/P2 分级，有去重聚合机制，避免告警风暴。"

### 技巧 2：被问"告警太多怎么办"

标准三板斧：
1. **先收敛**：去重、聚合、抑制，减少噪音
2. **再分级**：P0 必须立刻处理，P2 可以排期
3. **最后优化阈值**：静态阈值改动态基线，减少误报

### 技巧 3：链路追踪的"坑"

- **开销问题**：100% 采样性能下降 20%+，必须采样
- **异步调用**：MQ / 线程池里的 Trace 容易断，需要手动透传 Context
- **第三方服务**：调用外部 API 无法插码，只能看到"黑盒"

---

## 📌 一句话总结

> 可观测性 = Metrics 看趋势 + Logs 查细节 + Traces 找链路。面试答出三大支柱 + SRE 黄金信号 + 告警分级收敛，这题就稳了。

---

## 🔗 拓展阅读

- [Google SRE Book - Monitoring](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Prometheus 官方文档](https://prometheus.io/docs/introduction/overview/)
- [OpenTelemetry 官方文档](https://opentelemetry.io/docs/)
- Grafana Labs Blog: "Loki: Prometheus-inspired open source logging"
