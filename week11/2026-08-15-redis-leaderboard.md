# Day 72 — 设计排行榜 + Redis 核心原理

> **日期**: 2026-08-15  
> **主题**: Redis 数据结构 & 持久化机制  
> **Week 11 / Day 72**

---

## 1. 今日算法题：设计排行榜系统（Design Leaderboard）

### 题目描述

设计一个排行榜系统，支持以下操作：

- `addScore(playerId, score)`：为指定玩家增加分数（可为负数，即扣分）
- `top(K)`：返回前 K 名玩家的总分之和
- `reset(playerId)`：重置指定玩家的分数为 0

**约束条件**：
- `1 <= playerId, K <= 10000`
- 操作次数最多 `1000` 次
- 分数范围为 `[-1000, 1000]`

### 示例

```
Leaderboard leaderboard = new Leaderboard();
leaderboard.addScore(1, 73);   // player1 = 73
leaderboard.addScore(2, 56);   // player2 = 56
leaderboard.addScore(3, 39);   // player3 = 39
leaderboard.addScore(4, 51);   // player4 = 51
leaderboard.addScore(5, 4);    // player5 = 4
leaderboard.top(1);            // 返回 73 (player1 最高)
leaderboard.reset(1);          // player1 = 0
leaderboard.reset(2);          // player2 = 0
leaderboard.addScore(2, 51);   // player2 = 51
leaderboard.top(3);            // 返回 141 = 51 + 51 + 39
```

### 解题思路

这道题的核心是**维护一个动态有序集合**，支持：
1. 更新某个 key 的 score（增/减/重置）
2. 快速获取前 K 大的 score 之和

**数据结构选择**：

| 方案 | 数据结构 | 时间复杂度 | 备注 |
|---|---|---|---|
| 方案 A | HashMap + 排序数组 | O(n) 更新，O(K) 查询 | 每次更新后重排，TLE |
| 方案 B | HashMap + TreeSet/平衡树 | O(log n) 更新/删除，O(K) 查询 | ✅ 最优 |
| 方案 C | HashMap + 堆 | O(log n) 更新，O(K log n) 查询 | 适合只查 top K |

**最优方案：HashMap + TreeSet（或语言自带的有序集合）**

核心思想：
- `HashMap<Integer, Integer>`：记录 playerId → score 的映射
- `TreeSet<Player>`：按 score 降序、playerId 升序排列，支持 O(log n) 的增删改查

**注意坑点**：
1. **更新 score 时要先删后插**：TreeSet 中元素排序 key 变了，直接修改会破坏结构
2. **重载比较器的一致性**：如果 score 相同，按 playerId 区分，否则 TreeSet 会认为是同一个元素
3. **reset 操作**：从 TreeSet 中移除，HashMap 中标记为 0（或删除）

### 代码实现

```java
import java.util.*;

class Leaderboard {
    // playerId -> score
    private Map<Integer, Integer> scores;
    // 按 score 降序排列的有序集合
    private TreeSet<Player> ranking;
    
    public Leaderboard() {
        scores = new HashMap<>();
        // 按 score 降序，score 相同按 playerId 升序
        ranking = new TreeSet<>((a, b) -> {
            if (a.score != b.score) {
                return b.score - a.score; // 降序
            }
            return a.id - b.id; // playerId 升序，确保唯一性
        });
    }
    
    public void addScore(int playerId, int score) {
        int newScore = scores.getOrDefault(playerId, 0) + score;
        
        // 如果玩家已存在，先从 TreeSet 中移除旧记录
        if (scores.containsKey(playerId)) {
            ranking.remove(new Player(playerId, scores.get(playerId)));
        }
        
        // 更新 score
        scores.put(playerId, newScore);
        ranking.add(new Player(playerId, newScore));
    }
    
    public int top(int K) {
        int sum = 0;
        int count = 0;
        for (Player p : ranking) {
            if (count >= K) break;
            sum += p.score;
            count++;
        }
        return sum;
    }
    
    public void reset(int playerId) {
        if (scores.containsKey(playerId)) {
            ranking.remove(new Player(playerId, scores.get(playerId)));
            scores.put(playerId, 0);
            ranking.add(new Player(playerId, 0));
        }
    }
    
    // 辅助类
    private static class Player {
        int id, score;
        Player(int id, int score) {
            this.id = id;
            this.score = score;
        }
        
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Player)) return false;
            Player p = (Player) o;
            return id == p.id && score == p.score;
        }
        
        @Override
        public int hashCode() {
            return Objects.hash(id, score);
        }
    }
}
```

### 复杂度分析

| 操作 | 时间复杂度 | 空间复杂度 |
|---|---|---|
| `addScore` | O(log n) — TreeSet 删 + 插 | O(n) |
| `top(K)` | O(K) — 遍历前 K 个 | O(1) |
| `reset` | O(log n) — TreeSet 删 + 插 | O(1) |

> n 为当前玩家数量

### 延伸讨论（面试官爱问的）

**Q: 如果玩家数量是 10^9，查询 top 100，怎么优化？**
> 可以用**分桶 + 基数排序思想**：将分数范围分成若干桶，每个桶记录分数区间内的玩家数和分数总和。查询时从高分桶开始累加，直到凑够 K 个。更新时用**差分数组**或**树状数组**维护桶的计数。

**Q: 如果是分布式场景，怎么设计？**
> 参考 Redis Sorted Set（ZSET）：
> - 用 `跳表（Skiplist）` + `哈希表` 实现 O(log n) 的增删改查
> - 分布式时用 Redis Cluster 分片，按 playerId 哈希到不同节点
> - 全局 Top K 可以用「局部 Top K 合并」或「近似算法（Count-Min Sketch + Heap）」

---

## 2. 面试技巧：Redis 核心原理速查

### 2.1 五种基本数据结构 & 经典场景

| 数据结构 | 底层实现 | 经典场景 | 时间复杂度 |
|---|---|---|---|
| **String** | SDS（简单动态字符串） | 缓存对象、计数器、分布式锁、Session | O(1) |
| **Hash** | 压缩列表 / 哈希表 | 存储对象（用户资料）、购物车 | O(1) |
| **List** | 快速链表（quicklist） | 消息队列、时间线、栈 | O(1) 头尾插入 |
| **Set** | 整数集合 / 哈希表 | 标签系统、共同关注、抽奖去重 | O(1) |
| **ZSET** | 跳表 + 哈希表 | 排行榜、延时队列、范围查询 | O(log n) |

> 💡 **面试话术**：「ZSET 是 Redis 最核心的数据结构之一，底层用**跳表维护有序性** + **哈希表实现 O(1) 按 member 查 score**，两者结合实现了高效的范围查询和单点更新。」

### 2.2 跳表（Skiplist）为什么能代替平衡树？

Redis 选择跳表而非红黑树/AVL树的原因：

1. **实现简单**：跳表代码量不到平衡树的 1/3，维护成本低
2. **范围查询友好**：`ZRANGE key start stop` 直接按链表遍历，平衡树需要中序遍历
3. **并发友好**：跳表修改只需局部调整指针，锁粒度更细
4. **期望复杂度**：抛硬币升层，期望空间 O(n)，期望时间 O(log n)

```
Level 3:  1 ----------> 9
Level 2:  1 ----> 5 --> 9
Level 1:  1 -> 3 -> 5 -> 7 -> 9
Level 0:  1 -> 3 -> 5 -> 7 -> 9 -> 11 (原始链表)
```

### 2.3 持久化机制：RDB vs AOF

| 特性 | RDB | AOF |
|---|---|---|
| **原理** | 定时 fork 子进程，生成内存快照 | 记录每个写命令到日志文件 |
| **文件体积** | 紧凑，经压缩 | 较大，需定期重写（rewrite） |
| **恢复速度** | ⭐ 快，直接加载二进制 | 慢，需逐条重放命令 |
| **数据安全** | 可能丢最后一次快照后的数据 | 几乎不丢（always 策略） |
| **性能影响** | fork 时短暂阻塞 | 持续写盘，有 IO 开销 |
| **最佳实践** | **两者同时开启**，RDB 做基础备份，AOF 做增量保护 |

**混合持久化（Redis 4.0+）**：
```
# AOF 文件格式：
# [RDB 头部] + [AOF 尾部增量命令]
# 重写时先写 RDB 格式的全量数据，再追加 AOF 格式的增量命令
```

> 💬 **面试官**：Redis 宕机了怎么恢复？  
> 🎯 **你**：「先加载 RDB 恢复大部分数据，再重放 AOF 增量日志，补全 RDB 快照后的数据。Redis 4.0 后支持混合持久化，重写后的 AOF 文件前半部分是 RDB 格式，后半部分是 AOF 命令，兼顾恢复速度和数据安全。」

### 2.4 面试高频题速答

**Q: Redis 单线程为什么还这么快？**
> 1. **纯内存操作**：所有数据在内存，无磁盘 IO
> 2. **IO 多路复用**：epoll/kqueue 处理海量连接，单线程无锁竞争
> 3. **避免上下文切换**：单线程无锁，无多线程切换开销
> 4. **高效数据结构**：SDS、跳表、整数集合等针对场景优化
>
> ⚠️ **注意**：Redis 6.0+ 引入**多线程 IO**，但命令执行仍是单线程，多线程只用于网络读写和协议解析。

**Q: 缓存穿透、缓存击穿、缓存雪崩的区别？**

| 问题 | 现象 | 解决方案 |
|---|---|---|
| **缓存穿透** | 查询不存在的数据，绕过缓存直达 DB | 布隆过滤器 / 空值缓存（短 TTL） |
| **缓存击穿** | 热点 key 过期瞬间，大量请求打到 DB | 互斥锁（Redis SETNX）/ 逻辑过期 |
| **缓存雪崩** | 大量 key 同时过期，DB 压力激增 | 随机 TTL / 多级缓存 / 熔断降级 |

**Q: Redis 的过期删除策略？**
> - **惰性删除**：访问时检查是否过期，过期则删除（CPU 友好，但可能堆积死数据）
> - **定期删除**：每 100ms 随机抽 20 个 key，删除其中过期的，如果超过 25% 则继续抽（平衡 CPU 和内存）
> - **内存淘汰**：当内存达到 maxmemory，按策略淘汰（LRU/LFU/随机/TTL）

### 2.5 Redis 分布式锁的正确姿势

```bash
# 错误写法（非原子操作）：
SETNX lock_key value
EXPIRE lock_key 30   # 如果这里挂了，锁永不过期

# 正确写法（Redis 2.6+）：
SET lock_key unique_value NX EX 30

# 释放锁（Lua 脚本保证原子性）：
if redis.call("get", KEYS[1]) == ARGV[1] then
    return redis.call("del", KEYS[1])
else
    return 0
end
```

**Redisson 看门狗机制**：
- 加锁后启动后台线程，每 10s 检查一次，如果还持有锁就续期到 30s
- 防止业务执行时间长于锁过期时间导致的误释放

---

## 3. 今日速记卡

### 🧠 算法速记

```
排行榜设计 = HashMap + TreeSet
├─ HashMap: playerId → score (O(1) 查当前分)
├─ TreeSet: 按 score 降序排列 (O(log n) 增删)
└─ 更新时：先删旧节点，再插新节点（排序 key 变了！）

延伸：Redis ZSET = Hash + Skiplist
├─ Hash: member → score (O(1) 查分)
└─ Skiplist: 按 score 排序 (O(log n) 范围查询)
```

### 🧠 Redis 速记

```
Redis 快 = 内存 + IO多路复用 + 单线程无锁 + 高效数据结构

持久化双保险：
├─ RDB: 快照，恢复快，可能丢数据
├─ AOF: 命令日志，数据安全，恢复慢
└─ 混合模式(4.0+): RDB头部 + AOF尾部

缓存三坑：
├─ 穿透 → 布隆过滤器 / 空值缓存
├─ 击穿 → 互斥锁 / 逻辑过期
└─ 雪崩 → 随机TTL / 多级缓存

分布式锁：SET key value NX EX + Lua释放 + Redisson看门狗
```

---

## 4. 推荐阅读

1. [Redis 设计与实现](http://redisbook.com/) — 黄健宏，Redis 源码级解析
2. [Redis 官方文档 - 持久化](https://redis.io/docs/management/persistence/)
3. LeetCode 1244: Design A Leaderboard（本题来源）

---

> 📝 **明日预告**：MySQL 主从复制与读写分离原理

*打卡 Day 72，已连续更新 72 天 🔥*
