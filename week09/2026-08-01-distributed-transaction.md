# Day 56 | 分布式事务：从 2PC 到 Saga 的工业级实践

> **日期**：2026-08-01  
> **Week 9 主题**：系统架构与工程实践 — 分布式事务、微服务拆分、监控告警、高可用设计  
> **今日核心**：理解分布式事务的 ACID 困境与 BASE 妥协，掌握 2PC / TCC / Saga / 本地消息表四种方案的核心思想与选型逻辑。

---

## 🎯 今日算法题

### 题目：设计一个分布式事务协调器（伪代码实现）

**题目描述**：

在微服务架构中，订单服务需要同时操作「订单库」和「库存库」。请设计一个基于 **TCC（Try-Confirm-Cancel）** 模式的分布式事务框架核心逻辑，要求：

1. 实现 `TransactionCoordinator` 协调器，管理多个参与者的生命周期；
2. 每个参与者需实现 `Try` / `Confirm` / `Cancel` 三阶段接口；
3. 处理幂等性（同一事务多次 Confirm/Cancel 只生效一次）；
4. 处理悬挂问题（Cancel 比 Try 先到时的处理）；
5. 用伪代码展示一个「下单扣库存」的完整流程。

**核心考点**：这不是一道 LeetCode 题，而是 **系统设计 + 工程思维** 的综合考察。面试官想看的是你对分布式事务本质的理解，以及如何在「一致性」和「可用性」之间做权衡。

---

### 💡 解题思路

#### 1. 为什么需要分布式事务？

单体架构 → 一个数据库 → ACID 天然保障。

微服务架构 → 多个数据库/服务 → **网络不可靠、节点会宕机、消息会丢失** → 传统 ACID 玩不转了。

核心矛盾：**CAP 定理** — 分区容错性(P)是必选项，一致性(C)和可用性(A)只能二选一。

#### 2. 四种主流方案对比

| 方案 | 一致性级别 | 性能 | 复杂度 | 适用场景 |
|------|-----------|------|--------|----------|
| **2PC** | 强一致性 | 低（阻塞） | 中 | 对一致性要求极高，短事务，传统金融系统 |
| **TCC** | 最终一致性 | 高 | 高 | 电商核心交易，需要自定义补偿逻辑 |
| **Saga** | 最终一致性 | 很高 | 中 | 长事务业务流程（旅游预订、物流） |
| **本地消息表** | 最终一致性 | 高 | 低 | 异步场景，容忍延迟，实现简单 |

#### 3. TCC 模式核心思想

```
Try    → 预留资源（冻结库存，订单状态=预创建）
Confirm → 真正执行业务（扣减库存，订单状态=已支付）
Cancel  → 释放预留资源（解冻库存，订单状态=已取消）
```

**关键设计点**：
- **幂等性**：每个参与者维护 `transaction_log` 表，记录 `(tx_id, action)` 状态，重复执行直接返回成功；
- **悬挂问题**：Cancel 先到、Try 后到。解决方案 — Try 阶段检查事务是否已 Cancel，若是则拒绝执行；
- **空回滚**：Try 未执行成功，Cancel 被调用。Cancel 需判断 Try 是否执行过，未执行则直接返回；
- **超时处理**：协调器定时扫描超时事务，根据当前状态驱动补偿。

#### 4. 伪代码实现

```python
class TransactionCoordinator:
    """TCC 事务协调器"""
    
    def __init__(self):
        self.tx_store = {}  # tx_id -> TransactionRecord
        self.participants = []
    
    def register(self, participant):
        self.participants.append(participant)
    
    def execute(self, tx_id, context) -> bool:
        """执行 TCC 事务"""
        record = TransactionRecord(tx_id=tx_id, status="TRYING")
        self.tx_store[tx_id] = record
        
        # Phase 1: Try
        try_results = []
        for p in self.participants:
            result = p.try_reserve(context)
            if not result.success:
                # Try 失败，进入 Cancel 补偿
                record.status = "CANCELLING"
                self._cancel_all(tx_id, context, try_results)
                return False
            try_results.append((p, result))
        
        record.status = "CONFIRMING"
        
        # Phase 2: Confirm
        for p, _ in try_results:
            if not self._confirm_with_idempotency(p, tx_id, context):
                # Confirm 失败？实际中需告警+人工介入，或进入异步重试
                record.status = "CONFIRM_FAILED"
                return False
        
        record.status = "CONFIRMED"
        return True
    
    def _confirm_with_idempotency(self, participant, tx_id, context) -> bool:
        """幂等 Confirm"""
        if self._is_action_recorded(tx_id, "CONFIRM"):
            return True  # 已执行过，直接返回成功
        
        success = participant.confirm(context)
        if success:
            self._record_action(tx_id, "CONFIRM")
        return success
    
    def _cancel_all(self, tx_id, context, executed_tries):
        """Cancel 所有已 Try 成功的参与者"""
        for p, result in executed_tries:
            # 空回滚检查：如果 Try 实际未成功，跳过
            if result.success:
                p.cancel(context)
        self.tx_store[tx_id].status = "CANCELLED"
    
    def _is_action_recorded(self, tx_id, action) -> bool:
        # 查 transaction_log 表
        pass
    
    def _record_action(self, tx_id, action):
        # 写入 transaction_log 表
        pass


class InventoryParticipant:
    """库存服务参与者"""
    
    def try_reserve(self, ctx):
        # 1. 检查事务状态，防止悬挂
        if self._is_cancelled(ctx.tx_id):
            return TryResult(success=False, reason="TX_ALREADY_CANCELLED")
        
        # 2. 冻结库存
        row = db.execute(
            "UPDATE inventory SET frozen = frozen + ?, available = available - ? "
            "WHERE product_id = ? AND available >= ?",
            ctx.quantity, ctx.quantity, ctx.product_id, ctx.quantity
        )
        if row == 0:
            return TryResult(success=False, reason="INSUFFICIENT_STOCK")
        
        # 3. 记录事务状态
        self._record_tx_state(ctx.tx_id, "TRIED")
        return TryResult(success=True)
    
    def confirm(self, ctx):
        # 真正扣减库存
        db.execute(
            "UPDATE inventory SET frozen = frozen - ?, sold = sold + ? "
            "WHERE product_id = ?",
            ctx.quantity, ctx.quantity, ctx.product_id
        )
        return True
    
    def cancel(self, ctx):
        # 解冻库存
        if not self._is_tried(ctx.tx_id):
            return True  # 空回滚：Try 没执行过，直接返回
        
        db.execute(
            "UPDATE inventory SET frozen = frozen - ?, available = available + ? "
            "WHERE product_id = ?",
            ctx.quantity, ctx.quantity, ctx.product_id
        )
        self._record_tx_state(ctx.tx_id, "CANCELLED")
        return True
```

#### 5. 复杂度分析

| 维度 | 分析 |
|------|------|
| **时间复杂度** | Try/Confirm/Cancel 各 O(1) 数据库操作；协调器 O(n)，n 为参与者数量 |
| **空间复杂度** | 每个参与者需维护 `transaction_log` 表，O(活跃事务数) |
| **网络开销** | 2 轮 RPC（Try + Confirm/Cancel），比 2PC 少 1 轮 |
| **核心代价** | 业务侵入性强 — 每个操作都要拆成 Try/Confirm/Cancel 三个接口 |

---

## 🗣️ 面试技巧

### 1. "从不会聊到会"的话术模板

**如果面试官问"讲讲分布式事务"，你可以这样展开：**

> "分布式事务的核心是 **在不可靠的网络上追求可靠的一致性**。我先从最简单的场景说起 —— 单体应用用本地事务就行，但拆成微服务后，一个业务操作可能涉及多个数据库，这时候就要引入分布式事务协议。
> 
> 业界主流有四种方案，我按 **一致性强度从强到弱** 排序：2PC → TCC → Saga → 本地消息表。选型时我通常会问三个问题：
> 1. 业务对一致性的容忍度？（能不能接受短暂不一致）
> 2. 事务执行时间长短？（短事务用 2PC/TCC，长事务用 Saga）
> 3. 团队工程能力？（TCC 业务侵入性强，落地成本高）
> 
> 如果是电商下单场景，我倾向于 **TCC**，因为库存扣减需要实时性，而且可以在 Try 阶段做库存预占，用户体验比较好。但如果是旅游预订（订机票+酒店+租车）这种长流程，Saga 更合适。"

### 2. 高频追问与应对

| 追问 | 回答要点 |
|------|----------|
| "2PC 和 TCC 有什么区别？" | 2PC 是数据库层面协议（XA），锁定资源直到事务结束；TCC 是业务层面，Try 阶段预留资源不锁定，Confirm/Cancel 由业务实现，性能更好但侵入性强 |
| "TCC 的悬挂问题怎么解决？" | Cancel 比 Try 先到时，Try 阶段先查事务日志，若已 Cancel 则拒绝执行，防止资源被错误预留 |
| "如果 Confirm 阶段失败了怎么办？" | 设计为 **尽最大努力交付**：异步重试 + 告警 + 人工兜底。因为 Try 已成功，Confirm 理论上不应该失败，若失败大概率是 Bug 或极端故障 |
| "Seata 的 AT 模式了解吗？" | AT = Automatic Transaction，基于 SQL 解析自动生成反向补偿 SQL（Undo Log），对业务零侵入。缺点是隔离性弱（全局锁），高并发下可能冲突 |
| "什么时候用消息队列最终一致性？" | 对实时性要求不高、允许秒级延迟的场景，比如订单完成后发积分、同步物流信息。实现简单，性能最好 |

### 3. 加分项：提到工业级实践

- **阿里 Seata**：开源分布式事务框架，支持 AT（自动补偿）、TCC、Saga、XA 四种模式；
- **蚂蚁 SOFA/DTP**：金融级分布式事务，对一致性要求极高；
- **RocketMQ 事务消息**：Half Message + 本地事务回查，实现最终一致性，性能极好；
- **数据库实现**：MySQL XA、PostgreSQL 2PC，了解底层 commit/rollback 的原子性保证。

### 4. 避坑指南

- ❌ 不要说"分布式事务就是 2PC" —— 暴露知识单一；
- ❌ 不要追求"强一致性" —— 绝大多数互联网场景 BASE 就够了；
- ✅ 要强调 **幂等性设计** —— 这是分布式系统的生命线；
- ✅ 要提到 **监控与对账** —— 再完美的协议也需要兜底机制。

---

## 📝 课后巩固

1. **手撕代码**：用你熟悉的语言实现一个简化版 TCC 协调器，支持注册参与者、Try/Confirm/Cancel 三阶段调用；
2. **对比分析**：画一张表对比 2PC / TCC / Saga / 本地消息表在「一致性、性能、复杂度、适用场景」四个维度的差异；
3. **阅读拓展**：Seata 官方文档中 AT 模式的 Undo Log 生成机制，理解"业务零侵入"背后的技术原理。

---

> **明日预告**：Day 57 — 微服务拆分原则与领域驱动设计（DDD），从「业务边界」到「服务边界」的实战经验。
