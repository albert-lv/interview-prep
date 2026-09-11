# Day 99 — 有效数字 + 词法分析与正则引擎实现

> 📅 2026-09-11 · Week 15 编译原理与语言实现 🛠️ · Day 99

---

## 1. 今日算法题：有效数字（Valid Number）

**题目链接**：[LeetCode 65](https://leetcode.cn/problems/valid-number/)

### 题目描述

给定一个字符串 `s`，请判断它是否表示一个**有效的数字**。

有效数字的格式（简化文法）：
```
number   → [sign] integer [fraction] [exponent]
sign     → '+' | '-'
integer  → digit { digit }
fraction → '.' digit { digit } | digit { digit } '.'
exponent → ('e' | 'E') [sign] digit { digit }
digit    → '0' | '1' | ... | '9'
```

**示例**：
- ✅ `"2"` → true
- ✅ `"0089"` → true
- ✅ `"-0.1"` → true
- ✅ `"+3.14"` → true
- ✅ `"4."` → true（小数点后可空）
- ✅ `"-.9"` → true（小数点前可空）
- ✅ `"2e10"` → true
- ✅ `"-90E3"` → true
- ✅ `"3e+7"` → true
- ✅ `"+6e-1"` → true
- ✅ `"53.5e93"` → true
- ❌ `"abc"` → false
- ❌ `"1a"` → false
- ❌ `"1e"` → false（e 后必须有数字）
- ❌ `"e3"` → false（e 前必须有数字）
- ❌ `"99e2.5"` → false（e 后必须是整数）
- ❌ `"--6"` → false
- ❌ `"-+3"` → false
- ❌ `"95a54e53"` → false

### 解题思路：DFA 状态机

这道题的本质是**词法分析（Lexical Analysis）**：判断输入字符串是否属于某个正则语言。最优雅的解法是构造一个**确定有限自动机（DFA）**。

#### 状态设计

我们将数字的文法转换为 DFA 状态：

| 状态 | 含义 |
|---|---|
| 0 | 起始状态 / 等待符号或数字 |
| 1 | 已读整数部分 |
| 2 | 已读小数点，且前面无数字（如 `".5"`） |
| 3 | 已读小数点，且前面有数字（如 `"3."` 或 `"3.14"`） |
| 4 | 已读 `e`/`E`，等待指数部分的符号或数字 |
| 5 | 已读指数的符号，等待指数数字 |
| 6 | 已读指数的数字 |

#### 状态转移图

```
           '+'/'-'          digit              '.'
    ┌─────────────────┐  ┌─────────┐       ┌─────────┐
    ▼                 │  ▼         │       ▼         │
┌─────┐    '+'/'-'   ┌─────┐    digit   ┌─────┐   digit   ┌─────┐
│  0  │ ───────────► │  1  │ ─────────► │  3  │ ────────► │  3  │◄──┐
└──┬──┘              └──┬──┘            └──┬──┘           └──┬──┘   │
   │ digit              │ '.'              │ 'e'/'E'         │ '.'  │
   ▼                    ▼                  │                 │      │
┌─────┐              ┌─────┐               │               digit    │
│  1  │◄─────────────│  2  │◄─────────────┘                 │      │
└──┬──┘ digit         └──┬──┘ digit                          │      │
   │ 'e'/'E'             │ 'e'/'E'                           │      │
   ▼                     ▼                                    │      │
┌─────┐   '+'/'-'     ┌─────┐                                │      │
│  4  │ ───────────►  │  5  │                                │      │
└──┬──┘               └──┬──┘                                │      │
   │ digit               │ digit                             │      │
   ▼                     ▼                                    │      │
┌─────┐◄──────────────┐┌─────┐                               │      │
│  6  │◄──────────────┘│  6  │◄──────────────────────────────┘      │
└─────┘ digit          └─────┘ digit                                    │
```

**接受状态**：1（纯整数）、3（小数）、6（科学计数法）

#### 代码实现（Python）

```python
class Solution:
    def isNumber(self, s: str) -> bool:
        """
        DFA 状态机实现
        状态 0: 起始 / 等待符号或数字
        状态 1: 已读整数
        状态 2: 已读小数点(前无数字)
        状态 3: 已读小数点(前有数字) / 小数部分
        状态 4: 已读 e/E，等待指数符号或数字
        状态 5: 已读指数符号，等待指数数字
        状态 6: 已读指数数字
        """
        # 转移表: state -> {char_type: next_state}
        # char_types: 'digit', 'sign', 'dot', 'exp', 'other'
        transitions = {
            0: {'sign': 1, 'digit': 2, 'dot': 3},
            1: {'digit': 2, 'dot': 3},
            2: {'digit': 2, 'dot': 4, 'exp': 5},
            3: {'digit': 4},
            4: {'digit': 4, 'exp': 5},
            5: {'sign': 6, 'digit': 7},
            6: {'digit': 7},
            7: {'digit': 7},
        }
        
        # 这里用一个更简洁的版本：直接编码状态转移
        state = 0
        for ch in s:
            if ch in '+-':
                if state == 0:
                    state = 1
                elif state == 5:
                    state = 6
                else:
                    return False
            elif ch == '.':
                if state == 0 or state == 1:
                    state = 3  # 3: 小数点后有数字才算完整，但先记录
                elif state == 2:
                    state = 3  # 不允许两个小数点
                else:
                    return False
            elif ch in 'eE':
                if state == 2 or state == 4 or state == 7:
                    state = 5
                else:
                    return False
            elif ch.isdigit():
                if state == 0 or state == 1:
                    state = 2  # 整数部分
                elif state == 3:
                    state = 4  # 小数部分（前无整数）
                elif state == 5 or state == 6:
                    state = 7  # 指数部分
                # state 2, 4, 7 保持不变
            else:
                return False
        
        # 接受状态：2（整数）、4（小数点后有数字）、7（科学计数法）
        # 注意 state 3 是 "+." / "-." 这种不完整的
        return state in (2, 4, 7)
```

上面的实现有点容易错，来一版**更清晰、面试时更好画的 DFA**：

```python
class Solution:
    def isNumber(self, s: str) -> bool:
        """
        更清晰的 DFA 版本，状态语义更明确
        """
        # state: 0=start, 1=sign, 2=integer, 3=dot_without_int, 
        #        4=dot_with_int, 5=fraction, 6=exp, 7=exp_sign, 8=exp_int
        state = 0
        
        for c in s:
            if c in '+-':
                if state == 0: state = 1
                elif state == 6: state = 7
                else: return False
            elif c == '.':
                if state in (0, 1): state = 3      # . 或 +. / -.
                elif state == 2: state = 4          # 整数后接.
                else: return False
            elif c in 'eE':
                if state in (2, 4, 5): state = 6    # 整数/小数后接e
                else: return False
            elif c.isdigit():
                if state in (0, 1, 2): state = 2    # 整数部分
                elif state == 3: state = 5          # .5 形式
                elif state == 4: state = 5          # 3.5 形式
                elif state in (5, 6, 7, 8): state = 8  # 指数部分
                else: return False  # state=5 其实可以继续数字
            else:
                return False
        
        # 接受状态
        return state in (2, 4, 5, 8)
```

#### Go 实现（面试手写推荐）

```go
func isNumber(s string) bool {
    // DFA: state -> (char_type -> next_state)
    // 用 map 表示转移，面试时可以直接写 if-else
    
    type state int
    const (
        start state = iota      // 0: 起始
        sign                    // 1: 读到符号
        integer                 // 2: 读到整数
        dotNoInt                // 3: 小数点前无整数
        dotWithInt              // 4: 小数点前有整数
        fraction                // 5: 小数部分
        exp                     // 6: 读到 e/E
        expSign                 // 7: 指数符号
        expInt                  // 8: 指数数字
    )
    
    st := start
    for i := 0; i < len(s); i++ {
        c := s[i]
        switch {
        case c == '+' || c == '-':
            if st == start {
                st = sign
            } else if st == exp {
                st = expSign
            } else {
                return false
            }
        case c == '.':
            if st == start || st == sign {
                st = dotNoInt
            } else if st == integer {
                st = dotWithInt
            } else {
                return false
            }
        case c == 'e' || c == 'E':
            if st == integer || st == dotWithInt || st == fraction {
                st = exp
            } else {
                return false
            }
        case c >= '0' && c <= '9':
            switch st {
            case start, sign, integer:
                st = integer
            case dotNoInt, dotWithInt, fraction:
                st = fraction
            case exp, expSign, expInt:
                st = expInt
            default:
                return false
            }
        default:
            return false
        }
    }
    
    return st == integer || st == dotWithInt || st == fraction || st == expInt
}
```

#### 复杂度分析

| 指标 | 复杂度 | 说明 |
|---|---|---|
| 时间 | **O(n)** | 扫描字符串一次 |
| 空间 | **O(1)** | 仅维护一个状态变量 |

---

### 扩展：手写一个词法分析器（Mini Lexer）

这道题的真正价值是让你理解**如何用 DFA 做词法分析**。下面是一个能识别数字、标识符、运算符的 Mini Lexer：

```python
class MiniLexer:
    """识别数字、标识符、运算符的词法分析器"""
    
    TOKEN_SPEC = [
        ('NUMBER',    r'\d+(\.\d*)?([eE][+-]?\d+)?'),  # 整数/小数/科学计数
        ('IDENT',     r'[A-Za-z_][A-Za-z0-9_]*'),       # 标识符
        ('ASSIGN',    r'='),                             # 赋值
        ('OP',        r'[+\-*/]'),                       # 运算符
        ('LPAREN',    r'\('),                            # 左括号
        ('RPAREN',    r'\)'),                            # 右括号
        ('SKIP',      r'[ \t]+'),                        # 空白
        ('MISMATCH',  r'.'),                             # 其他字符
    ]
    
    def __init__(self, code: str):
        self.code = code
        self.pos = 0
    
    def next_token(self):
        if self.pos >= len(self.code):
            return ('EOF', '')
        
        # 基于最长匹配原则尝试每种模式
        import re
        for token_type, pattern in self.TOKEN_SPEC:
            regex = re.compile(pattern)
            m = regex.match(self.code, self.pos)
            if m:
                self.pos = m.end()
                if token_type == 'SKIP':
                    return self.next_token()
                if token_type == 'MISMATCH':
                    raise SyntaxError(f"Unexpected character: {m.group()}")
                return (token_type, m.group())
        
        raise SyntaxError("Unknown token")

# 使用示例
lexer = MiniLexer("x = 3.14e-2 + foo")
tokens = []
while True:
    tok = lexer.next_token()
    tokens.append(tok)
    if tok[0] == 'EOF':
        break
print(tokens)
# [('IDENT', 'x'), ('ASSIGN', '='), ('NUMBER', '3.14e-2'), 
#  ('OP', '+'), ('IDENT', 'foo'), ('EOF', '')]
```

> 💡 **面试说辞**："实际编译器的 Lexer 不会用 Python 的 `re.match`，而是用 DFA 直接驱动字符扫描，效率更高。正则只是定义 Token 模式的便利方式，真正的词法分析器会预编译成正则 → NFA → DFA 的转移表。"

---

## 2. 面试技巧：词法分析与正则引擎实现

### 2.1 词法分析器（Lexer/Tokenizer）核心要点

**Q：编译器前端为什么要分词法分析和语法分析两个阶段？**

> 🎯 **答**：**关注点分离 + 简化设计**。词法分析处理正则语言（Regular Language），用 DFA 就能搞定；语法分析处理上下文无关语言（Context-Free），需要下推自动机。分开后：
> 1. 语法分析器拿到的是结构化 Token 流，不需要关心空白字符、注释
> 2. 词法规则变化（如新增关键字）不影响语法分析器
> 3. 每个阶段都可以用最优算法（DFA vs LR）

**Q：手写词法分析器的步骤？**

```
1. 定义 Token 类型（用正则描述每种 Token 的模式）
2. 正则 → NFA（Thompson 构造法）
3. NFA → DFA（子集构造法 / Powerset Construction）
4. DFA 最小化（Hopcroft 算法，可选但推荐）
5. 用 DFA 转移表驱动字符扫描，最长匹配原则
6. 遇到多个匹配时按优先级选择（关键字 > 标识符）
```

**Q：最长匹配原则（Maximal Munch）是什么？**

> 🎯 **答**：扫描时每次匹配**尽可能长**的 Token。例如 `"=="` 应被识别为 `EQ`（相等运算符），而不是两个 `ASSIGN`。实现方式：DFA 每步都记录"当前是否在某个接受状态"，直到无法继续转移时，回退到最后一个接受状态。

---

### 2.2 正则 → NFA → DFA 完整链路

这是编译原理中最经典的问题链，面试常考。

#### Thompson 构造法（正则 → NFA）

| 正则表达式 | NFA 构造 |
|---|---|
| `ε` | `start ──ε──► accept` |
| `a`（字符） | `start ──a──► accept` |
| `AB`（连接） | A 的 accept ──ε──► B 的 start |
| `A\|B`（选择） | 新 start ──ε──► A.start / B.start，合并 accept |
| `A*`（Kleene 闭包） | 新 start ──ε──► A.start ──ε──► 新 accept，并加回环 |

> 💡 **面试速记**：Thompson 构造的核心是每个操作都引入**新的 start/accept 状态**，保持"每个状态最多两条 ε 转移"的性质。

#### 子集构造法（NFA → DFA）

```
核心思想：DFA 的每个状态 = NFA 状态的集合（ε-closure）

算法：
1. 计算 NFA 起始状态的 ε-closure → DFA 起始状态
2. 对 DFA 每个未处理状态和每个输入字符：
   a. 计算该状态集合经字符转移后的 ε-closure
   b. 若新集合未出现，加入 DFA 状态集
3. 包含 NFA 接受状态的所有 DFA 状态都是接受状态
```

**复杂度**：NFA 有 n 个状态 → DFA 最坏有 2ⁿ 个状态（指数爆炸）。但实际中很少发生。

#### DFA 最小化（Hopcroft 算法）

```
核心思想：把等价状态合并

两个状态等价 ⟺ 对所有输入字符串，转移行为相同（都接受或都不接受）

算法：
1. 初始划分：接受状态集 / 非接受状态集
2. 反复细分：若某组内状态对同一输入转移到不同组，则拆分
3. 直到无法细分，每组就是一个最小化 DFA 状态
```

**复杂度**：O(n log n)，n 为 DFA 状态数。

---

### 2.3 正则引擎实现：NFA vs DFA 回溯

面试常问："Python/JavaScript 的正则为什么有灾难性回溯？"

| 特性 | DFA 引擎 | NFA 回溯引擎 |
|---|---|---|
| 代表 | `grep -P`、Go `regexp`、Lex/Flex | Python `re`、PCRE、Java `Pattern` |
| 算法 | 状态驱动，无回溯 | 深度优先搜索 + 回溯 |
| 时间复杂度 | **O(n)**，线性！ | 最坏 **O(2ⁿ)** |
| 功能支持 | 不支持反向引用、捕获组 | 支持反向引用、捕获组、Lookaround |
| 空间复杂度 | O(m)，m=模式长度 | O(n) 递归栈 |
| 典型陷阱 | 无 | `a*a*a*a*a*b` 匹配 `"aaaaaac"` 时指数级回溯 |

> 💀 **灾难性回溯示例**：
> ```python
> import re
> # 这个正则匹配 "a" 重复任意次 + "b"
> pattern = re.compile(r'(a+)+b')
> pattern.match('a' * 30 + 'c')  # 超时！指数级回溯
> ```
> 原因：`(a+)+` 对 `"aaa"` 有 2ⁿ 种分组方式，每种都要尝试。

**防护措施**：
1. 使用 possessive quantifier（`++`、`*+`）—— 匹配后不回溯
2. 使用 atomic grouping（`(?>...)`）
3. 限制回溯次数（Python 的 `re` 没有，PCRE 有 `(*LIMIT_MATCH=...)`）
4. **工业界方案**：RE2（Google）用 DFA + NFA 混合，保证 O(n) 时间

---

### 2.4 编译器工具链：Lex/Flex 与 Yacc/Bison

| 工具 | 作用 | 输入 | 输出 |
|---|---|---|---|
| **Lex/Flex** | 词法分析器生成器 | Token 正则定义（`.l` 文件） | C 代码实现的 Lexer |
| **Yacc/Bison** | 语法分析器生成器 | 上下文无关文法（`.y` 文件） | C 代码实现的 Parser（LALR） |

**工作流程**：
```
source.l ──► flex ──► lex.yy.c
source.y ──► bison ──► y.tab.c
              ↓
         gcc 编译 → 可执行编译器前端
```

**Flex 文件示例**（识别数字和标识符）：
```c
%{
#include "y.tab.h"  // Bison 生成的 Token 类型定义
%}

%%
[0-9]+          { yylval.num = atoi(yytext); return NUMBER; }
[a-zA-Z_][a-zA-Z0-9_]*  { yylval.str = strdup(yytext); return IDENT; }
"+"             { return PLUS; }
"-"             { return MINUS; }
[ \t\n]         { /* 跳过空白 */ }
.               { return yytext[0]; }
%%
```

---

### 2.5 高频面试连环问

#### Q1：如何用 DFA 识别关键字和标识符的冲突？

> 🎯 **答**：**优先级规则 + 表查找**。DFA 扫描时，若某个 Token 既匹配 `"int"`（关键字）又匹配标识符规则，优先按关键字处理。实现上：
> 1. 先用 DFA 识别出最长匹配的标识符/关键字字符串
> 2. 查关键字哈希表，命中则返回关键字 Token，否则返回标识符 Token
> 3. 这叫 **"关键字保留"（Reserved Words）** 机制

#### Q2：编译器如何处理注释和空白字符？

> 🎯 **答**：**Lexer 直接丢弃**。在 DFA 中定义空白和注释的转移路径，匹配成功时不返回 Token，直接继续扫描。行注释（`//`）读到换行符结束，块注释（`/* */`）用嵌套状态处理。

#### Q3：正则表达式 `(a|b)*abb` 的 DFA 怎么画？

> 🎯 **答**：这是编译原理教材原题。
> 1. 先 Thompson 构造 NFA：`(a|b)*` 循环 → 连接 `a` → `b` → `b`
> 2. 子集构造：从 ε-closure(0) 开始，计算每个字符的转移
> 3. 最终 DFA 有 4 个状态，接受状态包含 NFA 的终态
> 
> **手绘要点**：接受状态画双线，标注每个 DFA 状态包含的 NFA 状态集合。

#### Q4：手写一个简单的 `atoi`（字符串转整数），如何处理溢出？

> 🎯 **答**：
> ```c
> int myAtoi(char *s) {
>     int sign = 1, i = 0;
>     long result = 0;  // 用 long 检测溢出
>     while (s[i] == ' ') i++;
>     if (s[i] == '-' || s[i] == '+') 
>         sign = (s[i++] == '-') ? -1 : 1;
>     while (s[i] >= '0' && s[i] <= '9') {
>         result = result * 10 + (s[i] - '0');
>         if (sign * result > INT_MAX) return INT_MAX;
>         if (sign * result < INT_MIN) return INT_MIN;
>         i++;
>     }
>     return (int)(sign * result);
> }
> ```
> 关键：**用 long 检测溢出**，不要用 `result > INT_MAX / 10` 这种容易错的写法（除非面试官要求不用 long）。

#### Q5：编译原理在实际工程中有哪些应用？（非编译器领域）

> 🎯 **答**：
> 1. **配置文件解析**（YAML/JSON/TOML Parser）
> 2. **SQL 解析器**（MySQL、TiDB 的 SQL 语法分析）
> 3. **模板引擎**（Jinja2、Vue 模板编译）
> 4. **协议解析**（HTTP/2、gRPC 的二进制协议解析）
> 5. **规则引擎**（风控系统的表达式求值）
> 6. **代码分析与转换**（Babel 转译 ESLint 规则检查）
> 7. **数据序列化**（Protobuf、Thrift 的代码生成）

---

### 2.6 一句话速答模板

| 问题 | 一句话答 |
|---|---|
| DFA vs NFA 区别？ | DFA 每个状态对每个输入有且只有一个转移，NFA 可以有多个（含 ε 转移）。 |
| 为什么词法分析用 DFA 不用 NFA？ | DFA 匹配 O(n) 线性时间，NFA 回溯最坏指数级。 |
| 正则 `(a+)+b` 为什么会卡死？ | `(a+)+` 对 `"aaa"` 有 2ⁿ 种分组方式，回溯引擎每种都要试。 |
| Lex/Flex 输出什么？ | 输入正则定义，输出 C 代码实现的词法分析器。 |
| 关键字和标识符怎么区分？ | 先用 DFA 匹配出字符串，再查关键字哈希表决定 Token 类型。 |
| DFA 最小化干嘛用？ | 合并等价状态，减少状态数，降低内存占用和转移表大小。 |

---

## 3. 今日总结

| 项目 | 内容 |
|---|---|
| **算法题** | Valid Number — DFA 状态机识别数字文法 |
| **核心技巧** | 状态转移表设计、接受状态判定、DFA vs 正则回溯 |
| **面试考点** | 词法分析器工作流程、Thompson 构造、子集构造、DFA 最小化 |
| **工程延伸** | Lex/Flex 工具链、RE2 安全正则引擎、 disaster backtracking 防护 |

---

> 🎤 **明日预告**：Day 100 — 编译原理综合与面试实战（Week 15 完结 🎉），总结编译原理全链路高频考点，进入 Week 16！

---

*Written by Kimi Claw · 每日 20:42 自动推送 · [github.com/albert-lv/interview-prep](https://github.com/albert-lv/interview-prep)*
