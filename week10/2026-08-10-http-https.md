# Day 67：HTTP/HTTPS 核心原理

> Week 10：操作系统与网络底层  
> 日期：2026-08-10  
> 主题：HTTP 协议演进、HTTPS/TLS 握手、缓存机制、面试高频题

---

## 一、今日算法题：字符串解码（Decode String）

### 题目描述

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则：`k[encoded_string]`，表示其中方括号内部的 `encoded_string` 正好重复 `k` 次。注意 `k` 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

**示例：**
```
输入：s = "3[a]2[bc]"
输出："aaabcbc"

输入：s = "3[a2[c]]"
输出："accaccacc"

输入：s = "2[abc]3[cd]ef"
输出："abcabccdcdcdef"
```

### 解题思路

这道题的核心是处理**嵌套结构**，和 HTTP 协议解析、JSON 解析等场景非常相似——都需要用**栈**来处理成对出现的括号嵌套。

**关键观察：**
1. 遇到数字时，需要累积完整的数字（可能是多位数，如 `100[abc]`）
2. 遇到 `[` 时，将当前累积的**重复次数**和**已解码的前缀字符串**压栈，然后重置
3. 遇到 `]` 时，弹出栈顶的重复次数和前缀字符串，将当前解码结果重复后拼接
4. 遇到普通字母，直接累积到当前字符串

**为什么用栈？**
- 栈的 LIFO 特性完美匹配括号的嵌套关系
- 最近打开的 `[` 需要最先匹配闭合的 `]`
- 和 HTTP header 的解析、XML/JSON 解析一样，嵌套结构天然适合栈

### 代码实现

```python
def decodeString(s: str) -> str:
    stack = []
    current_num = 0
    current_str = ""
    
    for char in s:
        if char.isdigit():
            # 累积数字（处理多位数）
            current_num = current_num * 10 + int(char)
        elif char == '[':
            # 压栈：保存当前状态，开始新的子问题
            stack.append((current_str, current_num))
            current_str = ""
            current_num = 0
        elif char == ']':
            # 弹栈：合并子问题的解
            prev_str, num = stack.pop()
            current_str = prev_str + num * current_str
        else:
            # 普通字符，累积到当前字符串
            current_str += char
    
    return current_str
```

**递归版本（更贴近协议解析的写法）：**

```python
def decodeString(s: str) -> str:
    def helper(index):
        current_str = ""
        num = 0
        
        while index[0] < len(s):
            char = s[index[0]]
            
            if char.isdigit():
                num = num * 10 + int(char)
            elif char == '[':
                index[0] += 1  # 跳过 '['
                decoded, _ = helper(index)
                current_str += num * decoded
                num = 0
            elif char == ']':
                return current_str, index[0]
            else:
                current_str += char
            
            index[0] += 1
        
        return current_str, index[0]
    
    return helper([0])[0]
```

### 复杂度分析

| 维度 | 复杂度 | 说明 |
|------|--------|------|
| 时间 | O(n × max_k) | n 为字符串长度，max_k 为最大重复次数。最坏情况下输出字符串可能指数级增长 |
| 空间 | O(n) | 栈的最大深度由嵌套层数决定，最坏情况下 O(n) |

### 举一反三

- **JSON/XML 解析器**：本质上都是嵌套结构的递归/栈处理
- **HTTP 协议分块传输编码（Chunked Transfer Encoding）**：解析 chunk-size 和 chunk-data 的边界，也是状态机 + 累积处理
- **计算器问题（Basic Calculator）**：处理运算符优先级和括号，同样用栈保存中间状态

---

## 二、面试技巧：HTTP/HTTPS 高频考点

### 2.1 HTTP 版本演进（必考）

| 版本 | 核心特性 | 痛点 | 类比 |
|------|----------|------|------|
| HTTP/0.9 | 只有 GET，无 header，纯文本 | 功能极简 | "能用就行" |
| HTTP/1.0 | 引入 POST/HEAD、Header、状态码 | 每个请求新建 TCP 连接（短连接） | "每次聊天都要重新认识" |
| HTTP/1.1 | **持久连接**（Keep-Alive）、**管道化**、Host 头、缓存控制 | 队头阻塞（Head-of-Line Blocking） | "一条道排队，前面卡了都等着" |
| HTTP/2 | **二进制分帧**、**多路复用**、**头部压缩**（HPACK）、**服务器推送** | TCP 层队头阻塞仍在 | "多车道并行，但路面塌了全堵" |
| HTTP/3 | 基于 **QUIC（UDP）**、内置 TLS 1.3、**彻底解决队头阻塞** | 中间设备兼容性问题 | "换了一条新高速" |

**高频追问：**
> "HTTP/2 多路复用为什么还会队头阻塞？"

答：HTTP/2 的多路复用只是在**应用层**把多个请求/响应拆成帧交错发送，但底层还是一条 TCP 连接。TCP 要求按序交付，如果某个包丢失，后续所有流都必须等待重传——这就是 **TCP 层队头阻塞**。HTTP/3 用 QUIC（基于 UDP）解决了这个问题，因为 QUIC 在传输层就实现了流级别的独立可靠传输。

### 2.2 HTTPS = HTTP + TLS（面试必问，必须讲清楚握手）

#### TLS 1.2 握手（2-RTT）

```
客户端                              服务器
  | -------- Client Hello ---------> |  （支持的加密套件、客户端随机数）
  | <------- Server Hello ---------- |  （选定的加密套件、服务端随机数）
  | <------- Certificate ----------- |  （服务器证书，含公钥）
  | <------- Server Hello Done ----- |
  | -------- Client Key Exchange ---> |  （用公钥加密预主密钥）
  | -------- Change Cipher Spec ----> |
  | -------- Finished --------------> |  （握手完成，后续加密通信）
  | <------- Change Cipher Spec ---- |
  | <------- Finished -------------- |
```

**关键点：**
1. **两个随机数**（Client Random + Server Random）+ **预主密钥**（Pre-Master Secret）→ 三者共同生成**会话密钥**
2. 为什么需要三个随机数？**防止重放攻击**，确保每次会话密钥都是唯一的
3. 证书验证：客户端用内置 CA 公钥验证服务器证书链

#### TLS 1.3 握手（1-RTT，甚至 0-RTT）

```
客户端                              服务器
  | -------- Client Hello ---------> |  （带密钥 share）
  | <------- Server Hello ---------- |  （选定密钥 share）
  | <------- {EncryptedExtensions}- |  （加密传输）
  | <------- {Certificate} --------- |
  | <------- {Finished} ------------ |
  | -------- {Finished} -----------> |
```

**TLS 1.3 改进：**
- 握手从 2-RTT 降到 **1-RTT**
- 支持 **0-RTT 恢复**（基于之前会话的 PSK，但有重放风险）
- 废弃 RSA 密钥交换，只支持 **ECDHE**，具备**前向安全性**（Forward Secrecy）
- 握手消息加密（除了 Client Hello），防止中间盒窥探

**面试金句：**
> "TLS 1.2 的 RSA 密钥交换，如果服务器私钥泄露，所有历史流量都能被解密。TLS 1.3 强制使用 ECDHE，每次握手生成临时密钥对，即使私钥泄露也无法解密历史流量——这就是前向安全。"

### 2.3 HTTP 缓存机制（高频，容易讲乱）

**缓存决策树（面试官最爱让你画的）：**

```
请求资源
  │
  ▼
是否有 Cache-Control: no-store? ──Yes──► 直接请求，不缓存
  │ No
  ▼
是否有 Cache-Control: no-cache 或 Pragma: no-cache?
  │ Yes                          │ No
  ▼                              ▼
带 If-None-Match/If-Modified-Since    检查是否过期
去服务端验证（协商缓存）                （强缓存）
  │                                    │
  ▼                                    ▼
返回 304 Not Modified                在有效期内？
（用本地缓存）                 Yes ──► 直接用缓存
                                 │ No
                                 ▼
                          协商缓存验证
```

**核心 Header 对比：**

| Header | 作用 | 优先级 |
|--------|------|--------|
| `Cache-Control: max-age=3600` | 强缓存，相对时间（秒） | 最高 |
| `Expires: Wed, 21 Oct 2026 07:28:00 GMT` | 强缓存，绝对时间 | 次高 |
| `ETag: "33a64df5"` | 协商缓存，资源唯一标识 | 优先于 Last-Modified |
| `Last-Modified: Wed, 21 Oct 2026 07:28:00 GMT` | 协商缓存，最后修改时间 | 兜底 |

**高频坑点：**
- `no-cache` 不是不缓存，而是**使用前必须去服务端验证**！真正不缓存是 `no-store`
- `max-age=0` 等价于 `no-cache`
- `s-maxage` 只针对共享缓存（CDN/代理），优先级高于 `max-age`
- `private` vs `public`：private 只允许浏览器缓存，CDN 不能缓存；public 都可以缓存

### 2.4 Cookie vs Session vs Token

| 维度 | Cookie/Session | JWT Token |
|------|---------------|-----------|
| 存储位置 | 服务端存 Session，客户端存 Session ID（Cookie） | 服务端无状态，Token 存客户端 |
| 扩展性 | 需要 Session 共享（Redis/DB） | 天然分布式友好 |
| 安全性 | Session ID 泄露即可冒充 | Token 泄露后无法主动吊销（除非黑名单） |
| 性能 | 每次查 Session 表/缓存 | 验签即可，无 IO |
| 适用场景 | 传统 Web 应用 | 移动端/API/微服务 |

**Cookie 安全属性（面试常问）：**
- `HttpOnly`：禁止 JavaScript 读取，防 XSS 窃取
- `Secure`：只允许 HTTPS 传输
- `SameSite`：控制跨站请求是否携带 Cookie（Strict/Lax/None）
- `SameSite=None` 必须配合 `Secure` 使用

### 2.5 HTTP 状态码（快速复习）

| 状态码 | 含义 | 面试高频场景 |
|--------|------|-------------|
| 200 | OK | 正常响应 |
| 204 | No Content | 成功但无返回体（如删除成功） |
| 301 | Moved Permanently | 永久重定向，SEO 权重转移 |
| 302 | Found | 临时重定向 |
| 304 | Not Modified | 协商缓存命中 |
| 400 | Bad Request | 请求参数错误 |
| 401 | Unauthorized | 未认证（需要登录） |
| 403 | Forbidden | 无权限（已登录但无权访问） |
| 404 | Not Found | 资源不存在 |
| 429 | Too Many Requests | 限流触发 |
| 500 | Internal Server Error | 服务端内部错误 |
| 502 | Bad Gateway | 网关/代理从上游收到无效响应 |
| 503 | Service Unavailable | 服务过载或维护 |
| 504 | Gateway Timeout | 网关/代理等待上游超时 |

**高频考点：401 vs 403**
> 401 是"你是谁？"（未认证），403 是"我知道你是谁，但你不准进"（无权限）。

### 2.6 面试答题框架（STAR 法则变种）

被问到"从浏览器输入 URL 到页面显示发生了什么"时，用这个分层框架回答：

1. **解析层**：DNS 解析（缓存层级 → 递归查询 → 迭代查询）
2. **传输层**：TCP 三次握手（或者 QUIC 连接建立）
3. **安全层**：TLS 握手（1-RTT / 0-RTT）
4. **应用层**：HTTP 请求 → 服务器处理 → HTTP 响应
5. **渲染层**：HTML 解析 → CSSOM + DOM → Render Tree → Layout → Paint → Composite

**时间分配建议：**
- 简单问法（5 分钟）：重点讲 DNS + TCP + TLS + HTTP，每层一句话带过
- 深入问法（15 分钟）：挑一层深入，比如详细讲 TLS 1.3 握手流程、HTTP/2 帧结构、QUIC 的可靠性如何实现

### 2.7 今日速记卡

```
□ HTTP/2 多路复用：应用层并行，TCP 层仍队头阻塞
□ HTTP/3 用 QUIC(UDP)，彻底解决队头阻塞
□ TLS 1.2 = 2-RTT，TLS 1.3 = 1-RTT，支持 0-RTT 恢复
□ 前向安全：ECDHE 临时密钥，私钥泄露不暴露历史流量
□ no-cache ≠ 不缓存，是用前必须验证；no-store 才是真正不缓存
□ ETag 优先级高于 Last-Modified，精确度更高
□ Cookie: HttpOnly 防 XSS，Secure 防明文传输，SameSite 防 CSRF
□ 401 未认证，403 无权限，502 网关坏，504 网关超时
```

---

## 三、今日一句话

> HTTP 的演进史，本质上是一部"人类对队头阻塞的斗争史"——从串行到并行，从一条 TCP 到多条流，从 TCP 到 UDP，每一次升级都是在用更复杂的协议设计，换取更低的延迟和更高的吞吐。

---

*明日预告：Day 68 — 网络安全基础（HTTPS 攻击面、中间人攻击、CSRF/XSS/SQL 注入防御）*
