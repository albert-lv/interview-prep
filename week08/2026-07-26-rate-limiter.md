# Day 50: 设计限流器（Rate Limiter）

> 面试高频系统设计题，保护服务不被流量冲垮的核心组件。

---

## 1. 今日设计题

### 题目描述
设计一个限流器（Rate Limiter），用于控制 API 的请求频率。要求：
- 支持 **令牌桶（Token Bucket）** 和 **漏桶（Leaky Bucket）** 两种经典算法
- 支持多用户 / 多 API 维度的独立限流（per-user, per-IP, per-API）
- 支持单机版和分布式版（基于 Redis）
- 在限流触发时返回明确的拒绝信号（如 HTTP 429）

### 核心场景
- 用户 A 的 API 调用限制为 **1000次/分钟**
- 某热点接口限制为 **100次/秒**（全局限流）
- 防止突发流量打垮后端数据库

---

## 2. 算法/设计思路

### 2.1 令牌桶（Token Bucket）⭐ 最常用

**核心思想**：桶以固定速率产生令牌，请求来时需消耗令牌，有令牌则通过，无令牌则拒绝。

- **桶容量** `capacity`：桶里最多能装多少令牌（决定突发能力）
- **产生速率** `rate`：每秒产生多少令牌（决定长期平均速率）
- **当前令牌数** `tokens`：惰性计算，不用定时器

**为什么选它？** 允许一定程度的突发流量（桶里有存货就能burst），更符合真实业务场景。

#### 伪代码实现

```python
import time

class TokenBucket:
    def __init__(self, capacity: int, rate: float):
        """
        capacity: 桶容量（最大突发数）
        rate: 每秒产生令牌数
        """
        self.capacity = capacity
        self.rate = rate
        self.tokens = capacity      # 初始满桶
        self.last_time = time.time()
    
    def allow(self, tokens: int = 1) -> bool:
        now = time.time()
        # 时间差内新产生的令牌
        delta = (now - self.last_time) * self.rate
        self.tokens = min(self.capacity, self.tokens + delta)
        self.last_time = now
        
        if self.tokens >= tokens:
            self.tokens -= tokens
            return True        # ✅ 通过
        return False           # ❌ 拒绝（触发限流）
```

**关键点**：
- **惰性计算**：不用定时器，每次请求时才更新令牌数
- **时间精度**：用浮点秒计算，不用整数秒，避免边界毛刺
- **线程安全**：单机版加锁；分布式版用 Redis + Lua 保证原子性

---

### 2.2 漏桶（Leaky Bucket）

**核心思想**：请求像水一样流入桶，桶以固定速率流出，桶满则溢出拒绝。

- **桶容量** `capacity`：队列最大长度
- **漏出速率** `rate`：固定处理速率

**特点**：
- 强制平滑流量，不允许突发（适合需要严格匀速的场景，如视频流）
- 实现通常用队列 + 定时器，或令牌桶的变体

**为什么面试问得少？** 令牌桶更灵活，漏桶的严格平滑很多时候反而是缺点。

---

### 2.3 滑动窗口计数（Sliding Window Counter）

**简单版**：固定窗口，按秒/分钟计数，到点就清零。—— 有临界突变问题（最后一秒+下一秒 = 2倍流量）。

**滑动窗口**：维护一个时间戳队列，每次请求来时，踢掉窗口外的老请求，统计剩余数量。

```python
def allow(self, key: str, now: float) -> bool:
    window = self.window  # 时间窗口长度（秒）
    limit = self.limit    # 窗口内最大请求数
    
    # 清理过期请求
    while self.requests[key] and self.requests[key][0] <= now - window:
        self.requests[key].popleft()
    
    if len(self.requests[key]) < limit:
        self.requests[key].append(now)
        return True
    return False
```

**面试常考点**：
- 固定窗口 vs 滑动窗口的精度差异
- 滑动窗口内存开销大（存时间戳），如何用 "滑动窗口日志" 或 "近似滑动窗口" 优化

---

### 2.4 分布式限流：Redis + Lua

单机限流不够，多实例部署时需要集中计数。Redis 是标准答案。

**核心问题**：`读取计数 → 判断 → 更新计数` 这三步必须原子，否则并发下会超卖。

**解决方案**：Redis + Lua 脚本（原子执行）。

```lua
-- 令牌桶分布式实现（Redis Lua）
local key = KEYS[1]          -- 限流key，如 "rate_limit:user:123"
local capacity = tonumber(ARGV[1])
local rate = tonumber(ARGV[2])     -- 每秒产生令牌数
local requested = tonumber(ARGV[3]) -- 本次请求需要令牌数
local now = tonumber(ARGV[4])      -- 当前时间戳（秒.毫秒）

local bucket = redis.call('HMGET', key, 'tokens', 'last_time')
local tokens = tonumber(bucket[1]) or capacity
local last_time = tonumber(bucket[2]) or now

-- 计算新增令牌
local delta = (now - last_time) * rate
tokens = math.min(capacity, tokens + delta)

local allowed = 0
if tokens >= requested then
    tokens = tokens - requested
    allowed = 1
end

-- 写回
redis.call('HMSET', key, 'tokens', tokens, 'last_time', now)
redis.call('EXPIRE', key, 60)  -- 自动过期清理

return allowed
```

**执行**：
```python
allowed = redis.eval(lua_script, 1, "rate_limit:user:123", 100, 10, 1, time.time())
```

---

## 3. 复杂度分析

| 维度 | 单机令牌桶 | 分布式 Redis+Lua |
|------|-----------|----------------|
| 时间复杂度 | O(1) | O(1)（Lua 在 Redis 单线程执行） |
| 空间复杂度 | O(用户维度数) | O(用户维度数)，依赖 Redis 内存 |
| 精度 | 纳秒级 | 毫秒级（受网络延迟影响） |
| 突发支持 | ✅ 允许 | ✅ 允许（桶容量控制） |

---

## 4. 面试技巧

### 4.1 系统设计面试万能沟通框架（限流器版）

面试官问 "设计一个限流器" 时，不要直接甩代码。按这个节奏：

**Step 1: 对齐需求（30秒）**
> "我先确认一下：是单机还是分布式？限流维度是什么（用户级、IP级、还是全局API级）？触发限流后是直接拒绝还是降级处理？"

**Step 2: 量化指标（30秒）**
> "假设我们需要支持 10万 QPS，限流粒度是用户级，大概百万级活跃用户。"

**Step 3: 讲算法选择（1分钟）**
> "我先讲令牌桶，因为它允许合理的突发流量，比漏桶更灵活。核心是两个参数：容量和产生速率..."

**Step 4: 讲单机实现（1-2分钟）**
> 画/写 `allow()` 的核心逻辑，强调 **惰性计算** 和 **线程安全**。

**Step 5: 扩展到分布式（1-2分钟）**
> "单机版用 `dict` 存状态，多机必须用 Redis 集中存储。关键是读取-判断-更新要原子，所以用 Lua 脚本。"

**Step 6: 讲边界和 trade-off（1分钟）**
> "令牌桶的缺点是时钟回拨会导致瞬间大量令牌（跟 Snowflake 一样），需要兜底。另外 Redis 挂了怎么办？可以本地降级为单机限流，保证不完全失控。"

---

### 4.2 高频追问 & 标准答法

| 追问 | 答法要点 |
|------|---------|
| "令牌桶和漏桶的区别？" | 令牌桶允许突发（桶里有存货就能burst），漏桶强制匀速。令牌桶更适合API限流，漏桶适合流量整形（如网络QoS）。 |
| "Redis挂了怎么办？" | 降级策略：本地缓存最后知道的令牌状态，降级为单机限流；或者快速失败（fail-open/fail-closed看业务）。 |
| "怎么支持多维度组合限流？" | key设计：`rate_limit:{维度1}:{维度2}`，如 `rate_limit:api:/order:create:user:123`。分层限流：先全局、再API级、再用户级。 |
| "百万用户，Redis内存会不会爆？" | 用自动过期（EXPIRE），冷用户key自动清理；或者换用 Redis Cell 模块（原生支持令牌桶）。 |
| "滑动窗口和令牌桶选哪个？" | 要精确计数选滑动窗口（如"每分钟最多5次"），要平滑流量选令牌桶。可以结合：外层令牌桶防突发，内层滑动窗口控精确频次。 |

---

### 4.3 一个加分点：提到 Redis Cell

> "实际生产中，Redis 有一个官方模块叫 **Redis Cell**，原生实现了令牌桶限流，用 `CL.THROTTLE` 命令，比 Lua 脚本性能更好，而且省得自己写原子逻辑。"

```bash
CL.THROTTLE user:123 100 10 60 1
# 容量100，每60秒产生10个令牌，本次请求消耗1个
# 返回 [0, 剩余令牌, 重试秒数, ...]  0=通过 1=拒绝
```

---

## 5. 今日核心记忆点

1. **令牌桶 = 容量 + 速率 + 惰性计算** —— 面试写代码就这三个要素
2. **分布式 = Redis + Lua 原子脚本** —— 不要说 "先读再判断再写"，那是错的
3. **Trade-off 要张口就来**：突发能力 vs 平滑度、精度 vs 内存开销、单机 vs 分布式延迟

---

> 📝 **面试金句**："限流不是让系统变慢，而是让系统在不崩的前提下，把资源给最值得的请求。"
