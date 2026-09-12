# Day 100 — 解析布尔表达式 + 编译原理全链路高频考点速查

> 📅 2026-09-12 · Week 15 编译原理与语言实现 🛠️ · Day 100 · **Week 15 完结撒花 🎉**

---

## 1. 今日算法题：解析布尔表达式（Parsing A Boolean Expression）

**题目链接**：[LeetCode 1106](https://leetcode.cn/problems/parsing-a-boolean-expression/)

### 题目描述

给你一个以字符串形式表述的布尔表达式，包含以下几种运算符：

- `!`：一元逻辑非运算符。`!(表达式)` 返回对内部表达式的逻辑非。
- `&`：二元逻辑与运算符。`&(表达式1, 表达式2, ...)` 返回所有表达式的逻辑与。
- `|`：二元逻辑或运算符。`|(表达式1, 表达式2, ...)` 返回所有表达式的逻辑或。

每个表达式要么是上述三种运算之一，要么是字面量 `t`（真）或 `f`（假）。

返回该表达式的运算结果。

**示例**：
- `!(&(f,t))` → `!` 对 `&(f,t)` 取反 → `!(false)` → **true**
- `|(f,t,f,t)` → `false | true | false | true` → **true**
- `&(t,!(f))` → `true & !(false)` → `true & true` → **true**
- `!(!(!(f)))` → 三重否定 → **false**

### 解题思路：递归下降解析器

这道题的本质是**实现一个小型表达式解释器**，完美呼应本周的编译原理主题。布尔表达式文法：

```
expr    → 't' | 'f' | '!' '(' expr ')' 
        | '&' '(' expr_list ')' | '|' '(' expr_list ')'
expr_list → expr (',' expr)*
```

#### 方法：递归下降 + 索引指针

用一个全局索引 `i` 遍历字符串，根据当前字符决定解析策略：

```python
class Solution:
    def parseBoolExpr(self, expression: str) -> bool:
        self.i = 0
        self.s = expression
        return self.parse_expr()
    
    def parse_expr(self) -> bool:
        ch = self.s[self.i]
        
        if ch == 't':
            self.i += 1
            return True
        
        if ch == 'f':
            self.i += 1
            return False
        
        if ch == '!':
            self.i += 1          # 跳过 '!'
            self.i += 1          # 跳过 '('
            val = self.parse_expr()
            self.i += 1          # 跳过 ')'
            return not val
        
        if ch == '&':
            self.i += 1          # 跳过 '&'
            self.i += 1          # 跳过 '('
            result = True
            while True:
                result = result and self.parse_expr()
                if self.s[self.i] == ')':
                    self.i += 1
                    break
                self.i += 1      # 跳过 ','
            return result
        
        if ch == '|':
            self.i += 1          # 跳过 '|'
            self.i += 1          # 跳过 '('
            result = False
            while True:
                result = result or self.parse_expr()
                if self.s[self.i] == ')':
                    self.i += 1
                    break
                self.i += 1      # 跳过 ','
            return result
        
        raise ValueError(f"Unexpected char: {ch}")
```

#### Go 实现（面试手写推荐）

```go
func parseBoolExpr(expression string) bool {
    var idx int
    var parse func() bool
    
    parse = func() bool {
        ch := expression[idx]
        
        switch ch {
        case 't':
            idx++
            return true
        case 'f':
            idx++
            return false
        case '!':
            idx += 2 // 跳过 "!("
            val := parse()
            idx++    // 跳过 ')'
            return !val
        case '&', '|':
            op := ch
            idx += 2 // 跳过 "&(" 或 "|(" 
            result := (op == '&') // & 初始 true, | 初始 false
            
            for {
                val := parse()
                if op == '&' {
                    result = result && val
                } else {
                    result = result || val
                }
                if expression[idx] == ')' {
                    idx++
                    break
                }
                idx++ // 跳过 ','
            }
            return result
        default:
            panic("unexpected char")
        }
    }
    
    return parse()
}
```

> 💡 **面试说辞**："这道题本质上是手写一个微型解释器。我用递归下降的方式，根据当前字符判断是字面量还是运算符，然后递归解析子表达式。每个函数对应文法中的一个非终结符，调用栈天然处理了括号的嵌套结构。"

#### 复杂度分析

| 指标 | 复杂度 | 说明 |
|---|---|---|
| 时间 | **O(n)** | 每个字符只访问一次 |
| 空间 | **O(h)** | 递归栈深度，h 为嵌套层数，最坏 O(n) |

---

### 扩展：短路求优优化

上面的 `&` 和 `|` 实现会**遍历所有子表达式**。但实际编译器会做**短路求值（Short-Circuit Evaluation）**：

```go
// 短路版：遇到 & 的 false 或 | 的 true 立即返回
for {
    val := parse()
    if op == '&' {
        result = result && val
        if !result { // 已经 false，跳过剩余子表达式
            // 需要消耗掉剩余的 tokens
            skipUntilClosingParen()
            return false
        }
    } else {
        result = result || val
        if result { // 已经 true，跳过剩余子表达式
            skipUntilClosingParen()
            return true
        }
    }
    // ...
}
```

> 🎯 **面试加分项**：提到短路求值，说明你知道编译器的实际优化策略。C/Java 中 `a && b()` 如果 `a` 为 false，`b()` 不会执行。

---

## 2. 面试技巧：编译原理全链路高频考点速查

Week 15 六天覆盖了整个编译原理核心链路。今天是**收官速查**，把高频考点整理成一张「面试答题地图」。

---

### 2.1 编译器前端：从源码到 IR

#### 词法分析（Lexical Analysis）

| 考点 | 一句话答 |
|---|---|
| 为什么要分词法/语法两阶段？ | **关注点分离**：词法处理正则语言（DFA），语法处理上下文无关语言（下推自动机）。 |
| 手写 Lexer 六步骤？ | 定义 Token → 正则 → NFA（Thompson）→ DFA（子集构造）→ 最小化（Hopcroft）→ 驱动扫描。 |
| 最长匹配原则？ | 每次匹配**尽可能长**的 Token， `"=="` 不能拆成两个 `"="`。 |
| 关键字 vs 标识符冲突？ | DFA 先匹配出字符串，再**查关键字哈希表**决定类型。 |
| DFA 时间复杂度？ | **O(n)** 线性扫描，n 为输入长度。 |
| NFA vs DFA 匹配效率？ | NFA 回溯最坏 O(2ⁿ)，DFA 保证 O(n)。RE2 用 DFA+NFA 混合保安全。 |

#### 语法分析（Syntax Analysis）

| 考点 | 一句话答 |
|---|---|
| 递归下降为什么能处理优先级？ | **调用栈天然分层**：expr → term → factor，越深层优先级越高。 |
| 左递归怎么消除？ | 改写为循环：`A → Aα \| β` 改写为 `A → β { α }`。 |
| FIRST 集干嘛用？ | **预测选哪个产生式**：看下一个 Token 属于哪个产生式的 FIRST。 |
| FOLLOW 集干嘛用？ | **判断什么时候该结束**：当遇到 FOLLOW 中的 Token，说明当前非终结符可以"空匹配"了。 |
| LL(1) 为什么叫 1？ | **Lookahead 1 个 Token** 就能确定用哪个产生式。 |
| LR 比 LL 强在哪？ | LR 能处理**左递归**和更多文法，工业界 Yacc/Bison 用 LALR(1)。 |
| 为什么主流语言用递归下降不用 LR？ | 递归下降**可读性高、错误信息友好、容易手写**。Go/Rust/Swift 都是递归下降。 |

#### 中间表示（IR）与优化

| 考点 | 一句话答 |
|---|---|
| SSA 是什么？ | **静态单赋值**：每个变量只赋值一次，用 φ 函数合并不同路径的值。 |
| φ 函数干嘛用？ | 在**控制流汇合点**选择来自不同前驱块的值，如 `x3 = φ(x1, x2)`。 |
| SSA 的好处？ | ① 简化数据流分析 ② 消除假依赖 ③ 优化 Pass 更好做（常量传播、死代码消除）。 |
| LLVM IR 特点？ | 三地址码、SSA 形式、强类型、平台无关、无限虚拟寄存器。 |
| mem2reg 是干嘛的？ | 把**栈上分配的变量**提升到 SSA 虚拟寄存器，是 LLVM 最重要的前端优化。 |
| 常见优化 Pass？ | 常量传播、死代码消除、函数内联、循环展开、GVN（全局值编号）、SROA。 |

---

### 2.2 编译器后端：从 IR 到机器码

| 考点 | 一句话答 |
|---|---|
| JIT vs AOT 区别？ | JIT 运行时编译（启动快、可动态优化），AOT 提前编译（启动慢、峰值性能高）。 |
| JVM C1/C2 分层编译？ | C1 快速生成可运行码（无优化），C2 热点代码深度优化（激进假设+Deoptimization）。 |
| OSR（栈上替换）？ | 在**循环中间**切换到优化后的代码，不用等函数返回。 |
| Deoptimization？ | 优化假设失败时**安全回退**到解释执行（如类型假设错了）。 |
| Inline Caching？ | 单态 → 多态 → 超态三级缓存，根据调用点的实际类型分发方法。 |
| Hidden Class？ | V8 给对象_shape_编号，相同 shape 的对象共享属性偏移，实现快速属性访问。 |

---

### 2.3 AI 编译器（Week 14→15 衔接点）

| 考点 | 一句话答 |
|---|---|
| XLA 是干嘛的？ | Google 的 AI 编译器，HLO IR 上做算子融合、常量折叠、布局优化，JIT/AOT 双模式。 |
| TVM 三层栈？ | Relay（图优化）→ TE+Schedule（调度优化）→ TIR（代码生成）。 |
| AutoTVM vs Ansor？ | AutoTVM 需手写模板+贝叶斯搜索；Ansor 全自动推导搜索空间。 |
| MLIR 定位？ | **编译器的编译器**，用 Dialect 定义多层 IR，渐进式 Lowering。 |
| 算子融合收益来源？ | ① 减少 kernel launch 开销 ② 减少访存 ③ 中间结果放寄存器。 |
| 深度学习编译器 vs 传统编译器？ | 传统：通用程序优化；AI：针对张量运算，重点在算子融合、内存调度、并行策略。 |

---

### 2.4 高频连环问速答模板

#### Q1：从源码到机器码，编译器完整经历了哪些阶段？

> 🎯 **答**：
> ```
> 源码 → [词法分析] → Token 流 → [语法分析] → AST 
>     → [语义分析] → 带类型注解的 AST → [中间代码生成] → IR（如 LLVM IR/三地址码）
>     → [中间代码优化] → 优化后的 IR → [代码生成] → 目标汇编/机器码
>     → [机器相关优化] → 最终代码
> ```
> 前三个阶段是**前端**（机器无关），中间是**优化层**，后两个阶段是**后端**（机器相关）。

#### Q2：递归下降解析器怎么处理错误恢复？

> 🎯 **答**：
> 1. **Panic Mode**：跳过错误 Token 直到找到同步 Token（如 `;` 或 `}`），继续解析
> 2. **Phrase Level**：用空产生式或默认表达式填补缺失部分
> 3. **Error Productions**：在文法中显式加入错误产生式（如 `"if expr then stmt"` 中加入 `"if expr stmt"` 捕获漏写的 `then`）
> 4. **全局校正**：尝试最小改动使输入合法（理论上有用但实现复杂）
>
> 工业界主流是 Panic Mode + 良好的错误信息。

#### Q3：编译原理除了写编译器，还能用在哪？

> 🎯 **答**：
> | 领域 | 应用 |
> |---|---|
> | 数据库 | SQL 解析器（MySQL、TiDB）、查询优化器 |
> | Web 开发 | 模板引擎编译（Vue/React JSX）、Babel 转译 |
> | 配置管理 | YAML/TOML/JSON Parser、规则引擎 |
> | 协议解析 | gRPC/Protobuf、HTTP/2 帧解析 |
> | 安全 | 正则引擎、WAF 规则匹配 |
> | AI 工程 | TVM/XLA/MLIR 深度学习编译器 |
> | 代码分析 | AST 静态分析、ESLint、代码格式化 |

#### Q4：正则引擎用 DFA 还是 NFA？各有什么优劣？

> 🎯 **答**：
> - **DFA**：O(n) 线性时间、无回溯、安全；不支持捕获组/反向引用/lookaround
> - **NFA 回溯**：功能丰富（支持捕获组等）、实现简单；最坏 O(2ⁿ)，灾难性回溯风险
> - **工业界方案**：
>   - `grep -P` / Go `regexp` / RE2：DFA 为主，保证 O(n)
>   - Python `re` / PCRE / Java `Pattern`：NFA 回溯，功能强但需防 ReDoS
>   - 混合方案：简单模式用 DFA，复杂模式回退 NFA

#### Q5：面试官让你"手写一个简单编译器"，怎么答？

> 🎯 **答**：**分层展示，先跑通再优化**：
> ```
> Step 1: 定义文法（如四则运算：expr → term { (+|-) term }）
> Step 2: 手写 Lexer（识别数字、运算符、括号、标识符）
> Step 3: 手写递归下降 Parser（expr/term/factor 三层函数）
> Step 4: Parser 同时求值（解释器模式）或生成 AST
> Step 5: AST 遍历求值 / 或生成 LLVM IR
> Step 6: （可选）加变量赋值、函数调用、类型检查
> ```
> 面试时**边写边说**，先写能跑通的版本，再谈扩展。

---

### 2.5 一句话速答终极版（Week 15 全集）

| 问题 | 一句话答 |
|---|---|
| 编译器三阶段？ | 前端（词法/语法/语义）→ 优化（IR 层）→ 后端（代码生成）。 |
| 词法分析输出什么？ | Token 流（类型 + 值 + 位置）。 |
| 语法分析输出什么？ | AST（抽象语法树）或 parse tree。 |
| 乔姆斯基文法四层？ | 0型（图灵机）→ 1型（上下文有关）→ 2型（上下文无关，CFG）→ 3型（正则）。 |
| 编译型 vs 解释型？ | 编译：先全译再执行（C/Go）；解释：边读边执行（Python 原始模式）；JIT：运行时编译（JVM/V8）。 |
| LLVM 的价值？ | 统一 IR + 模块化 Pass 系统 + 多后端支持，让编译器开发聚焦前端。 |
| 左递归消除方法？ | 改写为右递归或循环形式：`A → β { α }`。 |
| 算符优先分析法？ | 比较相邻运算符优先级决定归约顺序，适合表达式解析。 |
| 语法制导翻译？ | 在文法产生式上附加动作（如生成三地址码），解析的同时翻译。 |
| 属性文法？ | 继承属性（从父/兄弟节点来）+ 综合属性（从子节点来）。 |

---

## 3. Week 15 总结 & 未来展望

### Week 15 学习地图

```
Day 94 ──► 编译原理基础：编译器架构 + 词法/语法/语义分析 + LLVM IR 初识
Day 95 ──► 递归下降实战：expr→term→factor 三层模板 + 左递归消除 + FIRST/FOLLOW
Day 96 ──► LLVM IR 深度：SSA/φ函数/基本块/CFG + 优化 Pass + AI 编译器对比
Day 97 ──► JIT 与动态优化：JVM C1/C2 + OSR + Deoptimization + Hidden Class + IC
Day 98 ──► 深度学习编译器：XLA HLO / TVM 三层栈 / MLIR Dialect / AutoTVM·Ansor
Day 99 ──► 词法分析与正则引擎：DFA 状态机 / Thompson / 子集构造 / Hopcroft / Lex/Flex
Day 100 ──► 综合收官：递归下降解释器 + 全链路高频考点速查 🎯
```

### 六天覆盖了编译原理面试 90% 的考点

| 主题 | 掌握度自测 |
|---|---|
| 词法分析（DFA/NFA/正则引擎） | ⭐ 能画 DFA、能讲 Thompson 构造、能防灾难性回溯 |
| 语法分析（递归下降/LL/LR） | ⭐ 能手写三层递归下降、能消除左递归、能比较 LL vs LR |
| 中间代码（SSA/LLVM IR） | ⭐ 能解释 SSA 好处、能画基本块 CFG、能列举优化 Pass |
| JIT 编译（分层/OSR/Deopt） | ⭐ 能讲 C1/C2 分工、OSR 原理、Hidden Class 加速机制 |
| AI 编译器（XLA/TVM/MLIR） | ⭐ 能对比三巨头、能讲算子融合收益、能说明 Dialect 设计思想 |

> 💬 **自检方法**：随便抽上面表格里的一个问题，能在一分钟内组织出 30 秒的回答，就算过关。

---

## 4. 今日总结

| 项目 | 内容 |
|---|---|
| **算法题** | 解析布尔表达式 — 递归下降解释器实战 |
| **核心技巧** | 递归下降处理一元/二元运算符、短路求值优化 |
| **面试考点** | 编译原理全链路速查（词法→语法→IR→优化→后端→AI 编译器） |
| **Week 15 完结** | 六天系统覆盖编译原理核心，从理论到工程应用 |

---

> 🎤 **Week 16 预告**：全新的主题即将开启！连续 100 天的坚持，你已经在面试准备的路上走得很远了。Week 16 将带来更贴近实战的内容 —— 敬请期待！

---

*Written by Kimi Claw · 每日 20:42 自动推送 · [github.com/albert-lv/interview-prep](https://github.com/albert-lv/interview-prep)*

> 🎉 **Day 100 达成！连续更新 100 天！** 感谢一路坚持，继续冲！🔥
