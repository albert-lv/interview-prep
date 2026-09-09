# Day 97 — 为运算表达式设计优先级 + JIT 编译与动态优化

> 日期：2026-09-09  
> 主题：Week 15 · 编译原理与语言实现 🛠️  
> 难度：🌟🌟🌟（Medium）

---

## 今日算法题

### 题目：为运算表达式设计优先级（Different Ways to Add Parentheses）

**LeetCode 241** | [题目链接](https://leetcode.cn/problems/different-ways-to-add-parentheses/)

#### 题目描述

给你一个由数字和运算符组成的字符串 `expression`，按不同优先级组合数字和运算符，计算出所有可能的结果并返回。

你可以按任意顺序组合括号，但需要保证不改变原表达式的相对顺序。

**示例：**

```
输入：expression = "2-1-1"
输出：[0, 2]
解释：
  ((2-1)-1) = 0
  (2-(1-1)) = 2

输入：expression = "2*3-4*5"
输出：[-34, -14, -10, -10, 10]
解释：
  (2*(3-(4*5))) = -34
  ((2*3)-(4*5)) = -14
  ((2*(3-4))*5) = -10
  (2*((3-4)*5)) = -10
  (((2*3)-4)*5) = 10
```

**约束：**
- `1 <= expression.length <= 20`
- `expression` 由数字和算符 `'+'`、`'-'`、`'*'` 组成
- 题目保证给定表达式总是有效的

---

### 解题思路

这道题的本质是：**每个运算符都可以成为最后一层计算的根节点**。一旦确定了根，左边和右边就是两个独立的子问题。

所以自然想到 **分治法**：

1. 扫描表达式，遇到运算符 `op` 时，将表达式拆成 `left` 和 `right` 两部分
2. 递归计算 `left` 的所有可能结果、`right` 的所有可能结果
3. 双重循环组合左右结果，用 `op` 计算，收集所有结果
4. **base case**：如果表达式中没有运算符，说明是纯数字，直接返回 `[num]`

> 💡 这和编译器构建表达式语法树的过程几乎一模一样——每个运算符都是一个 AST 节点，左右子树就是操作数。这道题其实就是在枚举所有可能的 AST 结构。

---

### 代码实现

```go
package main

import (
	"strconv"
	"strings"
)

func diffWaysToCompute(expression string) []int {
	// base case：纯数字
	if !strings.ContainsAny(expression, "+-*") {
		num, _ := strconv.Atoi(expression)
		return []int{num}
	}

	var res []int
	for i, ch := range expression {
		if ch == '+' || ch == '-' || ch == '*' {
			// 递归求解左右两部分的所有结果
			left := diffWaysToCompute(expression[:i])
			right := diffWaysToCompute(expression[i+1:])

			// 组合左右结果
			for _, l := range left {
				for _, r := range right {
					switch ch {
					case '+':
						res = append(res, l+r)
					case '-':
						res = append(res, l-r)
					case '*':
						res = append(res, l*r)
					}
				}
			}
		}
	}
	return res
}
```

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(N × C(N)) | 其中 C(N) 是卡特兰数第 N 项，即不同括号方案数。每个子问题被重复计算，可用记忆化优化到 O(N³)。 |
| 空间 | O(N) | 递归栈深度，最坏情况下表达式完全左结合或右结合。 |

**记忆化优化版（面试加分项）：**

```go
var memo = map[string][]int{}

func diffWaysToComputeMemo(expression string) []int {
	if v, ok := memo[expression]; ok {
		return v
	}
	if !strings.ContainsAny(expression, "+-*") {
		num, _ := strconv.Atoi(expression)
		return []int{num}
	}
	var res []int
	for i, ch := range expression {
		if ch == '+' || ch == '-' || ch == '*' {
			for _, l := range diffWaysToComputeMemo(expression[:i]) {
				for _, r := range diffWaysToComputeMemo(expression[i+1:]) {
					if ch == '+' { res = append(res, l+r) }
					if ch == '-' { res = append(res, l-r) }
					if ch == '*' { res = append(res, l*r) }
				}
			}
		}
	}
	memo[expression] = res
	return res
}
```

> 🎯 **面试官追问**：如果表达式里有除法怎么办？
> 
> 要注意除零保护，而且整数除法截断方向要统一（向零还是向下）。另外不同括号顺序可能导致不同的截断结果，这也是这道题如果是除法会更有趣的原因。

---

## 面试技巧

### JIT 编译与动态优化

今天从「编译完就完事了」进化到「运行时还在持续优化」——这是现代动态语言（Java、JavaScript、Python）性能吊打直觉的核心秘密。

---

### 1. JIT 的本质：解释执行 vs AOT vs JIT

| 方式 | 原理 | 代表 | 优点 | 缺点 |
|---|---|---|---|---|
| **解释执行** | 逐条读取字节码，边解释边执行 | CPython、Ruby MRI | 启动快、实现简单 | 运行慢，每条指令都要解码 |
| **AOT 编译** | 运行前全部编译成机器码 | C/C++、Rust、Go | 运行时无编译开销，极致性能 | 编译慢、平台相关、动态特性受限 |
| **JIT 编译** | 运行时把热点代码编译成机器码 | JVM HotSpot、V8、LuaJIT、PyPy | 兼顾启动速度和峰值性能、可利用运行时信息优化 | 编译本身消耗 CPU/内存、需要预热 |

> 💬 **面试官**：为什么 Python 慢，但 PyPy 能快很多？
>
> 🎯 **你**：CPython 是纯解释执行，字节码逐条 dispatch  overhead 很大。PyPy 用了 JIT，对热点循环直接编译成机器码，消除了解释器 dispatch 开销；更重要的是它能利用运行时的类型信息做**类型特化**，比如发现某处一直是整数加法，就生成专门的整数加法机器码，而不是走通用的 PyObject 通用加法路径。

---

### 2. 热点检测：怎么知道哪段代码值得编译？

JIT 不会编译所有代码，只编译「热点」（Hot Spot）。检测方式两种：

**计数器法（JVM HotSpot）**
- 每个方法/循环有一个调用计数器 + 回边计数器（back-edge）
- 超过阈值（默认 10,000 次左右，可配置）触发 JIT 编译
- 半衰期衰减：防止启动阶段的老代码长期占用编译资源

**采样法（V8 早期）**
- 周期性中断，看当前执行到哪段代码
- 被采样到的代码标记为热点
- 开销低但精度不如计数器

> 🎯 **面试速记**：HotSpot 的名字就来自于「只优化热点代码」，方法计数器 + 回边计数器双管齐下。

---

### 3. JVM 的分层编译：C1 vs C2

JVM HotSpot 有两套 JIT 编译器，不是二选一，而是**一起上**：

| 编译器 | 别名 | 目标 | 特点 |
|---|---|---|---|
| **C1** | Client Compiler | 快速编译，快速执行 | 优化简单、编译速度快、代码质量一般 |
| **C2** | Server Compiler | 极致优化 | 激进优化、编译慢、生成代码质量高 |

**分层编译策略（Tiered Compilation）：**
- 第 0 层：解释执行
- 第 1 层：C1 编译，带简单优化 + **性能分析（Profiling）**
- 第 2 层：C1 编译，更激进优化
- 第 3 层：C1 编译，全开 profiling
- 第 4 层：C2 编译，基于 profiling 数据做激进优化

> 💬 **面试官**：为什么要分层？直接上 C2 不行吗？
>
> 🎯 **你**：C2 编译太慢了，如果等 C2 编译完再执行，启动延迟接受不了。分层编译的策略是「先让代码跑起来，再逐步升级」——先用 C1 快速编译拿到比解释执行好的性能，同时收集 profiling 数据，等代码真的够热，再用 C2 做深度优化。

---

### 4. 动态优化的核心武器箱

#### 4.1 内联缓存（Inline Caching, IC）

动态语言的对象属性访问是慢的，因为每次都要查哈希表。IC 的思路是：**第一次查到后，把结果缓存下来，下次直接命中**。

V8 的 IC 分三级：
- **monomorphic**：只见过一种类型，直接内联跳转
- **polymorphic**：见过 2-4 种类型，用 small switch
- **megamorphic**：类型太多，退化回通用查找

> 🎯 **面试金句**："Monomorphic IC 是动态语言的性能甜点——一旦类型稳定，属性访问成本和 C++ 虚表差不多。"

#### 4.2 隐藏类（Hidden Class）/ 形状（Shape）

JavaScript 对象看似随意加属性，但 V8 内部会给相同结构的对象分配同一个 Hidden Class。

```js
// 这两个对象会共享同一个 Hidden Class
let a = { x: 1, y: 2 };
let b = { x: 3, y: 4 };

// 但这个不会——属性顺序不同会导致不同的 Hidden Class
let c = { y: 5, x: 6 };
```

有了 Hidden Class，属性偏移量固定，访问属性就变成**基地址 + 固定偏移**的 O(1) 操作，和 C 结构体一样快。

> 🎯 **面试金句**："V8 让 JS 对象跑得像 C struct 的秘密就是 Hidden Class——前提是不要动态增删属性、不要改变属性顺序。"

#### 4.3 类型特化（Type Specialization）

JIT 在运行时看到了类型的真实分布，可以生成**类型专用**的机器码：

```python
# Python 伪代码，PyPy 会这样优化：
def add(a, b):
    return a + b

# 如果运行时观察到 a, b 总是 int
# PyPy 会生成专门的 int_add 机器码，绕过 PyObject 通用路径
```

如果后来类型变了（比如突然传了字符串），JIT 会**去优化（Deoptimization）**，退回到解释执行或更通用的编译版本。

#### 4.4 OSR（On-Stack Replacement）栈上替换

问题：一个循环已经跑了 1 万次，第 10001 次时 JIT 编译完成了，怎么办？

等循环结束再换？那还要跑很久。

OSR 的答案是：**直接在循环中途切换到编译后的代码**。JVM 在循环回边（back-edge）插入检查点，满足条件时把当前栈帧映射到编译后代码的入口，无缝切换。

> 🎯 **面试速记**：OSR 解决的是「热点代码正在执行中，编译刚好完成」的问题，让 JIT 的优化收益零延迟生效。

---

### 5. 去优化（Deoptimization）

JIT 的优化是基于**猜测**的：
- 猜测某个变量总是 int
- 猜测某个方法不会被继承覆盖
- 猜测某个条件分支总是走一边

如果猜测错了，不能崩溃，必须能**安全回退**。去优化就是把执行从编译代码切回解释器，撤销错误的假设。

JVM 的 **Speculative Optimization + Guard + Deopt** 模式：
1. C2 基于 profiling 猜测「这里总是 int」
2. 生成机器码时插入 Guard 检查
3. 如果 Guard 失败，触发 Deoptimization，回到解释执行

> 💬 **面试官**：去优化有性能代价吗？
>
> 🎯 **你**：有，主要是两方面：一是去优化本身要重建解释器栈帧，有开销；二是回退后代码以解释模式跑，性能下降。但如果猜测命中率高（比如 99%），总体收益还是正的。极端情况下如果类型一直抖动，JIT 可能停止优化这段代码。

---

### 6. 经典 JIT 编译器对比速查

| JIT | 语言 | 核心特点 |
|---|---|---|
| **JVM HotSpot** | Java/Scala/Kotlin | C1+C2 分层编译、OSR、Deoptimization、逃逸分析、锁消除 |
| **V8** | JavaScript | Ignition 解释器 + TurboFan JIT、Hidden Class、IC、Sea of Nodes IR |
| **LuaJIT** | Lua |  tracing JIT（不是 method-based）、极轻量、性能接近 C |
| **PyPy** | Python |  tracing JIT、RPython 元编译框架、自动 GC、STM |
| **HHVM** | PHP/Hack |  region-based JIT、支持 HIR→LIR→机器码 |

> 🎯 **面试速记**："LuaJIT 是 tracing JIT 的代表——不是按方法编译，而是追踪解释执行的热点 trace 路径，把整条 trace 编译成机器码，跨方法内联更激进。"

---

### 7. JIT 在 AI 编译器中的延伸

| 概念 | 传统 JIT | AI 编译器 |
|---|---|---|
| 热点 | 高频执行的方法/循环 | 高频执行的算子/子图 |
| 优化 | 内联、逃逸分析、锁消除 | 算子融合、内存布局优化、自动微分 |
| 代表 | JVM HotSpot | **TensorFlow XLA**、**PyTorch TorchScript**、**TVM JIT** |

XLA 的 JIT 编译模式：
- 捕获 TensorFlow/PyTorch 的计算图
- 用 HloPass 做代数化简、算子融合、布局优化
- 生成 LLVM IR，再编译成目标 GPU/TPU 机器码

> 💬 **面试官**：XLA 的 JIT 和 JVM 的 JIT 有什么本质区别？
>
> 🎯 **你**：JVM JIT 面向的是通用程序，优化的是控制流和内存访问；XLA JIT 面向的是**数据流图**，优化的是张量操作的并行度和内存局部性。XLA 的编译单元是 computation graph 而不是 method，它的核心优化是**算子融合**——把多个 element-wise 操作合成一个 kernel，减少 kernel launch 和显存读写开销。

---

### 8. 高频面试速答模板

| 问题 | 一句话速答 |
|---|---|
| 解释执行、AOT、JIT 的区别？ | 解释执行逐条翻译，启动快运行慢；AOT 预编译，运行快启动慢；JIT 运行时编译热点，兼顾两者。 |
| 为什么 JIT 比 AOT 更适合动态语言？ | AOT 需要提前知道类型，动态语言类型在运行时才能确定；JIT 可利用运行时的真实类型信息做特化。 |
| JVM 为什么分层编译？ | C1 编译快但优化弱，先跑起来；C2 优化强但编译慢，等代码够热再上。 |
| 什么是 OSR？ | 热点代码正在执行时 JIT 编译完成，OSR 允许在循环中途无缝切换到编译后的代码。 |
| 什么是 Deoptimization？ | JIT 基于猜测做激进优化，猜测失败时安全回退到解释执行或通用版本。 |
| Hidden Class 解决什么问题？ | 让动态对象的属性访问从哈希查找变成固定偏移访问，速度接近 C 结构体。 |
| Inline Caching 三级状态？ | monomorphic（单类型）→ polymorphic（2-4 类型）→ megamorphic（太多类型，退化）。 |
| LuaJIT 和 HotSpot 的 JIT 策略有什么不同？ | HotSpot 是 method-based JIT；LuaJIT 是 tracing JIT，追踪热点执行路径跨方法编译。 |

---

## 今日小结

- **算法**：分治法枚举所有括号方案 = 枚举所有可能的 AST 结构。记住「每个运算符都可以当根」。
- **JIT**：不是「编译一次」，而是「持续优化」。分层编译、热点检测、OSR、Deoptimization 这套组合拳，让动态语言也能跑出接近静态语言的性能。
- **延伸**：AI 编译器（XLA/TVM）的 JIT 思路和传统 JIT 一脉相承，只是优化对象从字节码变成了计算图。

> 明天（Day 98）：深度学习编译器专题 — TVM / MLIR / XLA 深度对比与面试实战 🚀
