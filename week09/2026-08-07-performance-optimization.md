# Day 62: 系统性能优化（System Performance Optimization）

> 日期：2026-08-07  
> 所属周：Week 09 — 系统架构与工程实践  
> 难度：🔴🔴🔴🔴🔴

---

## 今日算法题：无

本周为**系统设计 + 工程实践**专题，Day 62 为架构周收尾日，聚焦**性能优化方法论与实战**，无传统算法题，以系统设计深度题替代。

---

## 一、性能优化核心框架

### 1.1 性能指标体系

| 指标 | 定义 | 目标值（参考）|
|------|------|-------------|
| **RT (Response Time)** | 响应时间，请求发出到收到响应 | P99 < 200ms |
| **QPS/TPS** | 每秒查询/事务数 | 视业务而定 |
| **并发数** | 系统同时处理的请求数 | 与 QPS、RT 关系：QPS = 并发数 / RT |
| **吞吐量 (Throughput)** | 单位时间处理的请求/数据量 | 越高越好 |
| **资源利用率** | CPU / 内存 / 磁盘 / 网络使用率 | CPU < 70%，内存 < 80% |

> **Little's Law**: `L = λ × W`  
> 系统中平均对象数 = 到达率 × 平均停留时间

---

## 二、分层优化策略

### 2.1 客户端层
- **CDN 加速**：静态资源就近分发，减少回源
- **浏览器缓存**：Cache-Control / ETag / Last-Modified
- **资源压缩**：Gzip / Brotli 压缩，WebP/AVIF 图片格式
- **懒加载 / 预加载**：图片懒加载、路由懒加载、DNS 预解析

### 2.2 接入层 / 网关层
- **负载均衡**：Nginx / LVS / Envoy，加权轮询、最小连接、一致性哈希
- **SSL 卸载**：TLS 握手在网关层完成，减轻后端压力
- **限流熔断**：Sentinel / Hystrix，保护后端服务
- **请求合并**：批量接口聚合，减少网络往返

### 2.3 应用服务层
- **异步处理**：MQ 解耦，削峰填谷
- **连接池**：数据库连接池（Druid / HikariCP）、HTTP 连接池、线程池
- **本地缓存**：Caffeine / Guava Cache，热点数据内存缓存
- **并发模型**：Netty 事件驱动、Go goroutine、Java NIO
- **无状态设计**：服务无状态，水平扩展无瓶颈

### 2.4 数据层
- **Redis 缓存**：缓存穿透/击穿/雪崩防护策略
- **读写分离**：主从复制，写主读从
- **分库分表**：水平拆分（ShardingSphere / MyCat）
- **索引优化**：EXPLAIN 分析执行计划，覆盖索引、联合索引、最左前缀
- **SQL 优化**：避免 SELECT *、减少子查询、批量插入、分页优化（延迟关联 / 游标分页）

### 2.5 存储层
- **存储选型**：SSD vs HDD，顺序写 vs 随机写
- **日志优化**：WAL 机制，批量刷盘
- **压缩算法**：Snappy / LZ4 / Zstd，读写平衡

---

## 三、性能分析工具链

### 3.1 CPU 瓶颈分析
```bash
# Linux 性能分析四件套
top / htop          # 实时查看进程 CPU/内存
perf                # Linux 性能剖析，火焰图生成
vmstat / iostat     # 系统级 I/O 和 CPU 统计
strace / ltrace     # 系统调用跟踪
```

**火焰图（Flame Graph）解读**：
- 宽度 = 采样次数，越宽表示占用 CPU 越多
- 纵向 = 调用栈深度
- 找到最宽的"平顶山"，那就是性能瓶颈

### 3.2 JVM 性能分析（Java 技术栈）
```bash
jstack <pid>        # 线程 Dump，分析死锁/阻塞
jmap -histo <pid>   # 堆内存对象统计
jstat -gc <pid>     # GC 统计，判断是否需要调优
jprofiler / arthas  # 商业/开源性能剖析工具
```

### 3.3 数据库慢查询分析
```sql
-- MySQL 慢查询日志
SHOW VARIABLES LIKE 'slow_query_log%';
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;  -- 超过 1s 记录

-- 分析慢查询
EXPLAIN SELECT * FROM orders WHERE user_id = 123 ORDER BY created_at DESC;
-- 关注：type（ALL 全表扫描要警惕）、key、rows、Extra
```

---

## 四、经典优化案例分析

### 案例 1：缓存穿透 → 布隆过滤器
```python
# 问题：大量查询不存在的 key，直接打到 DB
# 解决：布隆过滤器前置拦截

import pybloom_live

bloom = pybloom_live.BloomFilter(capacity=1000000, error_rate=0.001)

# 预热：将所有存在的 key 加入布隆过滤器
for key in all_keys:
    bloom.add(key)

def get_user(user_id):
    if user_id not in bloom:
        return None  # 一定不存在，直接返回
    
    # 可能存在，查缓存 → 查 DB
    return cache.get(user_id) or db.query(user_id)
```

### 案例 2：接口响应慢 → 并行化查询
```python
import asyncio
import aiohttp

async def fetch_user(user_id):
    async with aiohttp.ClientSession() as session:
        async with session.get(f"/api/users/{user_id}") as resp:
            return await resp.json()

async def get_dashboard(user_id):
    # 并行查询用户信息、订单、通知
    user, orders, notices = await asyncio.gather(
        fetch_user(user_id),
        fetch_orders(user_id),
        fetch_notices(user_id)
    )
    return {"user": user, "orders": orders, "notices": notices}

# 串行：300ms + 200ms + 100ms = 600ms
# 并行：max(300, 200, 100) = 300ms
```

### 案例 3：数据库大表分页慢 → 游标分页
```sql
-- 传统 OFFSET 分页，数据量大时越来越慢
SELECT * FROM orders ORDER BY id LIMIT 1000000, 10;  -- 慢！

-- 游标分页（Keyset Pagination），O(1) 性能
SELECT * FROM orders 
WHERE id > :last_seen_id 
ORDER BY id 
LIMIT 10;
```

---

## 五、性能优化面试题

### 高频题 1：如何设计一个高性能的秒杀系统？
**答题框架**：
1. **前端**：静态化 + CDN + 验证码防刷 + 按钮置灰
2. **网关**：Nginx 限流（漏桶/令牌桶）+ 黑名单过滤
3. **应用层**：Redis 原子扣库存（Lua 脚本）+ MQ 异步下单
4. **数据库**：库存扣减优化（乐观锁 CAS）+ 异步写 DB
5. **兜底**：熔断降级 + 排队页面 + 库存预热

### 高频题 2：Redis 变慢了，怎么排查？
**答题框架**：
1. 查看 INFO 命令：`info stats`、`info commandstats`
2. 检查慢查询：`SLOWLOG GET 10`
3. 检查大 key：`redis-cli --bigkeys` 或 `MEMORY DOCTOR`
4. 检查持久化：AOF fsync 策略、RDB fork 阻塞
5. 检查内存：maxmemory 策略，是否频繁淘汰
6. 检查网络：连接数、带宽

### 高频题 3：MySQL 慢查询优化思路？
**答题框架**：
1. 开启慢查询日志，定位慢 SQL
2. EXPLAIN 分析执行计划
3. 索引优化：是否有合适索引、索引是否命中、是否需要覆盖索引
4. SQL 改写：减少子查询、避免函数操作列、分页优化
5. 架构优化：读写分离、分库分表、引入缓存
6. 参数调优：innodb_buffer_pool_size、连接数等

---

## 六、面试技巧

### 💡 技巧 1：性能优化面试的"分层思维"
面试官问"系统慢了怎么优化"，**不要直接给方案**。先分层分析：

> "我会从客户端 → CDN → 网关 → 应用 → 缓存 → 数据库 → 存储逐层排查。先通过监控定位瓶颈层，再针对性优化，避免盲目优化。"

这种回答展现**结构化思维**，比直接说"加缓存"高级十倍。

### 💡 技巧 2：量化你的优化成果
不要说"优化后快了"，要说：
- ❌ "优化后接口变快了"
- ✅ "通过 Redis 缓存 + 异步化改造，接口 P99 从 800ms 降至 120ms，QPS 从 2000 提升到 15000"

**数字是最有力的证明**。

### 💡 技巧 3：性能优化的"金三角"
面试官常问："优化要考虑什么？"  
记住三个维度：
1. **速度（Latency）**：响应快
2. **容量（Throughput）**：扛得住
3. **资源（Resource）**：成本低

> "性能优化是这三者的平衡，不能为了速度无限堆机器，也不能为了省钱让用户等待。"

### 💡 技巧 4：面试官挖坑："先优化哪里？"
标准回答：
> "先 profiling 找瓶颈，做**数据驱动的优化**。没有 profiling 的优化是瞎猜。常用工具：火焰图看 CPU、慢查询日志看 DB、jstack 看线程。"

### 💡 技巧 5：架构周收尾总结
Week 9 六天覆盖了系统设计的完整链路：
- **Day 56**：分布式事务 → 数据一致性
- **Day 57**：微服务拆分 → 服务治理
- **Day 58**：监控告警 → 可观测性
- **Day 59**：消息队列 → 异步通信
- **Day 60**：高可用架构 → 容错设计
- **Day 61**：容灾设计 → 灾难恢复
- **Day 62**：性能优化 → 调优方法论

> 面试时遇到系统设计题，可以从这六个维度展开：**一致性、治理、可观测、异步、容错、性能**。

---

## 七、今日学习检查清单

- [ ] 理解 RT / QPS / 并发数 / 吞吐量的关系
- [ ] 掌握 Little's Law 并能在面试中应用
- [ ] 熟悉至少一种性能分析工具（perf / arthas / EXPLAIN）
- [ ] 能说出至少 3 个数据库优化手段
- [ ] 能完整讲解一个性能优化案例（有数据）

---

## 八、延伸阅读

1. 《Systems Performance: Enterprise and the Cloud》— Brendan Gregg
2. 《高性能 MySQL》（High Performance MySQL）
3. Brendan Gregg 博客：https://www.brendangregg.com/
4. Linux 性能工具图谱：https://www.brendangregg.com/linuxperf.html

---

> **Week 9 完结撒花 🎉**  
> 六天系统架构与工程实践专题已完结。下周将进入新的主题，请查看 memory/interview-prep.md 获取最新进度。

> "性能优化没有银弹，但有方法论。先测量，再优化，最后验证。"
