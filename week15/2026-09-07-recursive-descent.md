# Day 95 · 表达式求值（递归下降版）+ 手写递归下降解析器实战

> 📅 2026-09-07 | Week 15 Day 2 | 主题：编译原理与语言实现 🛠️

---

## 今日算法题

### 表达式求值（支持 `+ - * / ( )` 和空格）

**题目描述**：
给定一个字符串表达式 `s`，包含非负整数、`+`、`-`、`*`、`/` 运算符、括号以及空格。返回表达式的计算结果。

- 整数除法向零截断（如 `7/3 = 2`，`-7/3 = -2`）
- 除数不会为零
- 输入表达式总是有效的

**示例**：
```
输入: "3+5*2-(8/2)"
输出: 9

输入: " 14-3/2 "
输出: 13

输入: "(2+3)*(4-1)"
输出: 15
```

---

### 解题思路

这道题是**递归下降解析器**的经典入门题。核心思想是把表达式拆成不同层级的语法规则，每个规则对应一个递归函数。

#### 语法规则（BNF）

```bnf
expr    → term { ('+' | '-') term }
term    → factor { ('*' | '/') factor }
factor  → number | '(' expr ')'
number  → digit { digit }
```

层层递进：
- `expr` 处理加减（最低优先级）
- `term` 处理乘除（较高优先级）
- `factor` 处理数字和括号（最高优先级）

**为什么这样分层？** 因为递归下降天然利用调用栈实现了优先级：越深的调用优先级越高。括号里的 `expr` 又会递归调用，完美解决嵌套。

#### 解析器结构

用一个类/结构体维护：
- `s`：输入字符串
- `pos`：当前读取位置
- `n`：字符串长度

辅助方法：
- `peek()`：看当前字符（不移动）
- `consume()`：消费当前字符，pos++
- `skipSpace()`：跳过空格

---

### 代码实现（Python）

```python
class Solution:
    def calculate(self, s: str) -> int:
        self.s = s
        self.pos = 0
        self.n = len(s)
        return self.expr()
    
    def expr(self) -> int:
        """expr → term { ('+' | '-') term }"""
        result = self.term()
        while True:
            self.skipSpace()
            if self.pos >= self.n or self.s[self.pos] not in '+-':
                break
            op = self.s[self.pos]
            self.pos += 1
            right = self.term()
            if op == '+':
                result += right
            else:
                result -= right
        return result
    
    def term(self) -> int:
        """term → factor { ('*' | '/') factor }"""
        result = self.factor()
        while True:
            self.skipSpace()
            if self.pos >= self.n or self.s[self.pos] not in '*/':
                break
            op = self.s[self.pos]
            self.pos += 1
            right = self.factor()
            if op == '*':
                result *= right
            else:
                # Python 向零截断需要特殊处理
                result = int(result / right)
        return result
    
    def factor(self) -> int:
        """factor → number | '(' expr ')'"""
        self.skipSpace()
        if self.pos < self.n and self.s[self.pos] == '(':
            self.pos += 1  # consume '('
            result = self.expr()
            self.skipSpace()
            self.pos += 1  # consume ')'
            return result
        return self.number()
    
    def number(self) -> int:
        """number → digit { digit }"""
        self.skipSpace()
        val = 0
        while self.pos < self.n and self.s[self.pos].isdigit():
            val = val * 10 + int(self.s[self.pos])
            self.pos += 1
        return val
    
    def skipSpace(self):
        while self.pos < self.n and self.s[self.pos] == ' ':
            self.pos += 1
```

---

### 代码实现（Go）

```go
package main

import (
    "fmt"
    "unicode"
)

type Parser struct {
    s   string
    pos int
    n   int
}

func NewParser(s string) *Parser {
    return &Parser{s: s, n: len(s)}
}

func (p *Parser) skipSpace() {
    for p.pos < p.n && p.s[p.pos] == ' ' {
        p.pos++
    }
}

func (p *Parser) peek() byte {
    p.skipSpace()
    if p.pos >= p.n {
        return 0
    }
    return p.s[p.pos]
}

func (p *Parser) consume() byte {
    ch := p.s[p.pos]
    p.pos++
    return ch
}

// expr → term { ('+' | '-') term }
func (p *Parser) expr() int {
    result := p.term()
    for {
        p.skipSpace()
        if p.pos >= p.n || (p.s[p.pos] != '+' && p.s[p.pos] != '-') {
            break
        }
        op := p.consume()
        right := p.term()
        if op == '+' {
            result += right
        } else {
            result -= right
        }
    }
    return result
}

// term → factor { ('*' | '/') factor }
func (p *Parser) term() int {
    result := p.factor()
    for {
        p.skipSpace()
        if p.pos >= p.n || (p.s[p.pos] != '*' && p.s[p.pos] != '/') {
            break
        }
        op := p.consume()
        right := p.factor()
        if op == '*' {
            result *= right
        } else {
            result /= right // Go 整数除法天然向零截断
        }
    }
    return result
}

// factor → number | '(' expr ')'
func (p *Parser) factor() int {
    p.skipSpace()
    if p.pos < p.n && p.s[p.pos] == '(' {
        p.consume() // '('
        result := p.expr()
        p.skipSpace()
        p.consume() // ')'
        return result
    }
    return p.number()
}

// number → digit { digit }
func (p *Parser) number() int {
    p.skipSpace()
    val := 0
    for p.pos < p.n && unicode.IsDigit(rune(p.s[p.pos])) {
        val = val*10 + int(p.s[p.pos]-'0')
        p.pos++
    }
    return val
}

func calculate(s string) int {
    p := NewParser(s)
    return p.expr()
}

func main() {
    fmt.Println(calculate("3+5*2-(8/2)")) // 9
    fmt.Println(calculate(" 14-3/2 "))    // 13
    fmt.Println(calculate("(2+3)*(4-1)")) // 15
}
```

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|------|--------|------|
| **时间** | O(n) | 每个字符只被扫描常数次 |
| **空间** | O(n) | 递归栈深度，最坏情况是全括号嵌套如 `((((1))))` |

---

### 关键要点 & 面试官追问

1. **为什么要递归？** 括号需要匹配，递归调用天然维护了一个"括号栈"，遇到 `(` 进一层，遇到 `)` 返回一层。

2. **优先级怎么体现？** `expr` → `term` → `factor` 的调用链越往里优先级越高。乘除在 `term` 里先算完，结果作为整体返回给 `expr` 做加减。

3. **怎么处理一元正负号？** 扩展 `factor`：
   ```bnf
   factor → ('+' | '-')* (number | '(' expr ')')
   ```
   连续多个正负号可以折叠成一个。

4. **如果加入变量怎么办？** 在 `factor` 里增加对标识符的解析，维护一个符号表（`map[string]int`）查值。

5. **如果语法有左递归怎么办？** 比如 `expr → expr '+' term`，直接翻译成递归会无限循环。需要改写为右递归或循环形式：`expr → term { '+' term }`。

---

## 面试技巧

### 递归下降解析器手写模板

面试如果让你"手写一个计算器"或"实现一个简单的表达式解析"，用这个框架，绝对不会乱：

```
1. 定义 Token 类型（可选，简单场景可以直接读字符）
2. 为每条语法规则写一个函数
3. 每个函数内部：先解析左边，再看后面有没有重复的操作符
4. 用成员变量维护 pos 和输入串
5. 括号 = 递归调用 expr()
```

**万能开场白**：
> "我会用递归下降解析器来实现。先把表达式按优先级拆成三层语法规则：加减层、乘除层、原子层。每层对应一个函数，括号通过递归 expr 来处理。"

---

### 高频面试连环问

#### Q1：递归下降 vs LL(1) 有什么关系？
**速答**：递归下降是最直观的 LL(1) 实现方式。LL(1) 是理论模型（从左到右读入、最左推导、向前看1个Token），递归下降是工程实现（每个非终结符对应一个函数）。不是每个递归下降都是严格的 LL(1)，但手写时通常按 LL(1) 设计。

#### Q2：什么是左递归？怎么消除？
**速答**：左递归是 `A → Aα | β` 这种形式，会导致递归下降无限循环。消除方法——改写为右递归或循环：
```bnf
; 消除前
expr → expr '+' term | term

; 消除后
expr → term { '+' term }
```

#### Q3：FIRST 集和 FOLLOW 集有什么用？
**速答**：
- **FIRST(α)**：从 α 能推导出的第一个终结符集合。用于预测：看当前输入属于哪个产生式的 FIRST，就选哪个分支。
- **FOLLOW(A)**：在句型中能紧跟 A 后面的终结符集合。用于判断什么时候可以认为非终结符 A 已经"解析完毕"（比如空串产生式）。

手写简单解析器时不需要显式算这两个集合，但面试问到要能说清楚。

#### Q4：递归下降的优缺点？
**速答**：
- **优点**：实现直观、易调试、可读性强、不需要生成器工具
- **缺点**：手写工作量大、对左递归敏感、错误恢复较难、大规模语法维护成本高
- **对比 LR**：LR 能处理更复杂的语法（包括左递归），但需要工具生成（Yacc/Bison），错误信息通常不如手写递归下降友好。

#### Q5：怎么给解析器加错误提示？
**速答**：
1. 每个解析函数记录当前位置
2. 失败时抛异常/返回错误，带上 `pos` 和期望的 token
3. 外层捕获后根据 `pos` 定位到源代码行列
4. 进阶：用 panic-recover 或返回 `Result<T, Error>` 模式

---

### 实战扩展：三步把计算器变成 Tiny 语言

如果在面试中表现好，面试官可能会让你扩展功能。提前准备这三个方向：

| 扩展 | 改动点 | 难度 |
|------|--------|------|
| **一元正负号** | `factor` 里加循环处理前缀 `+/-` | ⭐ |
| **变量赋值** | 新增 `stmt → identifier '=' expr`，维护符号表 | ⭐⭐ |
| **函数调用** | `factor` 里加 `identifier '(' [expr {',' expr}] ')'` | ⭐⭐⭐ |

**一个冷知识**：Python 的标准库 `ast` 模块和许多教学编译器（如 `tcc`）的前端都是递归下降实现的。V8 引擎的早期解析器也是手写递归下降。

---

> 💡 **今日心法**：递归下降的精髓不是递归，而是"分层"。每一层只关心自己的优先级，把更高优先级的丢给下一层。这种分层思维也是设计任何复杂系统的通用方法。
