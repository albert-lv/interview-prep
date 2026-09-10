# Day 98 — 原子的数量 + 深度学习编译器（TVM / MLIR / XLA）

> 日期：2026-09-10  
> 主题：Week 15 · 编译原理与语言实现 🛠️  
> 难度：🌟🌟🌟（Medium）

---

## 今日算法题

### 题目：原子的数量（Number of Atoms）

**LeetCode 726** | [题目链接](https://leetcode.cn/problems/number-of-atoms/)

#### 题目描述

给你一个化学式字符串，返回其中每种原子的数量。

化学式的格式如下：
- 原子名以大写字母开头，后跟零个或多个小写字母（如 `H`、`He`、`Mg`）
- 一个或多个原子组成分子，数量可选（如 `H2O`、`He2Mg3`）
- 括号表示分组，后面可跟数字表示倍数（如 `(OH)2`、`(H2O)2`）
- 括号可以嵌套

结果按原子名的字典序输出，数量为 1 时不输出数字。

**示例：**

```
输入：formula = "H2O"
输出："H2O"
解释：2 个 H，1 个 O

输入：formula = "Mg(OH)2"
输出："H2MgO2"
解释：2 个 O、2 个 H、1 个 Mg（按字典序）

输入：formula = "K4(ON(SO3)2)2"
输出："K4N2O14S4"
解释：K4 + (ON(SO3)2)2 = K4 + 2*N + 2*2*S + 2*2*3*O = K4N2O14S4
```

**约束：**
- `1 <= formula.length <= 1000`
- `formula` 只包含字母、数字和括号，且总是有效的

---

### 解题思路

这道题的核心是**解析嵌套结构**——左括号开启一个新作用域，右括号闭合并应用倍数。这和编译器处理嵌套代码块（函数、循环、条件）如出一辙。

**方法一：栈（模拟作用域）**

1. 遇到 `(`：当前作用域入栈，开启新作用域
2. 遇到 `)`：解析倍数，将当前作用域的所有原子计数乘以倍数，然后合并到上层作用域
3. 遇到原子：解析原子名和可选的数量，加入当前作用域
4. 最后对结果排序输出

> 💡 本质上这就是编译器的**符号表作用域链**——每个括号对开启一个局部作用域，退出时合并到上级。

**方法二：递归下降（更优雅的写法）**

把公式看作一个文法：
```
formula   → atom | group { atom | group }
group     → '(' formula ')' [ number ]
atom      → Upper { Lower } [ number ]
```
递归下降直接按文法解析，遇到 `(` 就递归解析子公式，代码结构更清晰。

---

### 代码实现（栈版本）

```go
package main

import (
	"fmt"
	"sort"
	"strconv"
	"unicode"
)

func countOfAtoms(formula string) string {
	// 栈：每个元素是一个 map，记录当前作用域的原子计数
	stack := []map[string]int{{}}
	i := 0
	for i < len(formula) {
		c := formula[i]
		if c == '(' {
			// 开启新作用域
			stack = append(stack, make(map[string]int))
			i++
		} else if c == ')' {
			// 解析倍数
			i++
			multiplier := 0
			for i < len(formula) && unicode.IsDigit(rune(formula[i])) {
				multiplier = multiplier*10 + int(formula[i]-'0')
				i++
			}
			if multiplier == 0 {
				multiplier = 1
			}
			// 弹出当前作用域，乘以倍数后合并到上层
			curr := stack[len(stack)-1]
			stack = stack[:len(stack)-1]
			for atom, count := range curr {
				stack[len(stack)-1][atom] += count * multiplier
			}
		} else {
			// 解析原子名（大写 + 后续小写）
			atom := string(c)
			i++
			for i < len(formula) && unicode.IsLower(rune(formula[i])) {
				atom += string(formula[i])
				i++
			}
			// 解析数量
			count := 0
			for i < len(formula) && unicode.IsDigit(rune(formula[i])) {
				count = count*10 + int(formula[i]-'0')
				i++
			}
			if count == 0 {
				count = 1
			}
			stack[len(stack)-1][atom] += count
		}
	}

	// 排序并输出
	result := stack[0]
	atoms := make([]string, 0, len(result))
	for atom := range result {
		atoms = append(atoms, atom)
	}
	sort.Strings(atoms)

	var sb []byte
	for _, atom := range atoms {
		sb = append(sb, []byte(atom)...)
		if result[atom] > 1 {
			sb = append(sb, []byte(strconv.Itoa(result[atom]))...)
		}
	}
	return string(sb)
}

func main() {
	fmt.Println(countOfAtoms("H2O"))           // H2O
	fmt.Println(countOfAtoms("Mg(OH)2"))       // H2MgO2
	fmt.Println(countOfAtoms("K4(ON(SO3)2)2")) // K4N2O14S4
}
```

---

### 代码实现（递归下降版本 —— 面试加分项）

```go
package main

import (
	"fmt"
	"sort"
	"strconv"
	"unicode"
)

type Parser struct {
	s string
	i int
}

func (p *Parser) parse() map[string]int {
	count := make(map[string]int)
	for p.i < len(p.s) && p.s[p.i] != ')' {
		if p.s[p.i] == '(' {
			p.i++ // '('
			inner := p.parse()
			p.i++ // ')'
			multiplier := p.parseNumber()
			for atom, c := range inner {
				count[atom] += c * multiplier
			}
		} else {
			atom := p.parseAtom()
			num := p.parseNumber()
			count[atom] += num
		}
	}
	return count
}

func (p *Parser) parseAtom() string {
	start := p.i
	p.i++ // uppercase
	for p.i < len(p.s) && unicode.IsLower(rune(p.s[p.i])) {
		p.i++
	}
	return p.s[start:p.i]
}

func (p *Parser) parseNumber() int {
	if p.i >= len(p.s) || !unicode.IsDigit(rune(p.s[p.i])) {
		return 1
	}
	num := 0
	for p.i < len(p.s) && unicode.IsDigit(rune(p.s[p.i])) {
		num = num*10 + int(p.s[p.i]-'0')
		p.i++
	}
	return num
}

func countOfAtomsRecursive(formula string) string {
	p := &Parser{s: formula}
	count := p.parse()

	atoms := make([]string, 0, len(count))
	for atom := range count {
		atoms = append(atoms, atom)
	}
	sort.Strings(atoms)

	var sb []byte
	for _, atom := range atoms {
		sb = append(sb, []byte(atom)...)
		if count[atom] > 1 {
			sb = append(sb, []byte(strconv.Itoa(count[atom]))...)
		}
	}
	return string(sb)
}

func main() {
	fmt.Println(countOfAtomsRecursive("K4(ON(SO3)2)2")) // K4N2O14S4
}
```

---

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|---|---|---|
| 时间 | O(N × log M) | N 为公式长度，M 为不同原子种类数（排序开销） |
| 空间 | O(N) | 栈深度最坏为嵌套括号层数，不超过 N/2 |

> 🎯 **面试官追问**：如果公式长度是 10⁵ 级别，递归版本会有什么问题？
>
> 递归深度可能超过 Go/Java 的栈限制，需要用栈版本。另外可以用 StringBuilder 避免频繁的字符串拼接。还可以用 TreeMap 直接维护有序性，省去最后的排序。

---

## 面试技巧

### 深度学习编译器 —— TVM / MLIR / XLA 深度对比

传统编译器（GCC/LLVM）优化的是**标量/向量代码**，而深度学习编译器优化的是**张量计算图**。今天从「编译一个函数」进化到「编译一个神经网络」——这是 AI infra 面试的深水区。

---

### 1. 为什么需要深度学习编译器？

| 问题 | 传统方案 | 深度学习编译器方案 |
|---|---|---|
| **算子碎片化** | 每个算子手写 CUDA kernel，开发成本高 | 自动生成 kernel，统一优化 |
| **硬件碎片化** | 每个新芯片要重写一套算子库 | 一次编写，多后端代码生成（CPU/GPU/TPU/NPU） |
| **融合机会丢失** | PyTorch eager 模式逐个算子执行，中间结果写回显存 | 自动识别可融合子图，合并成单 kernel |
| **调度僵化** | 固定 schedule，无法针对输入 shape 调整 | 自动搜索最优调度（AutoTVM / Ansor） |

> 💬 **面试官**：为什么 PyTorch Eager 模式慢，图模式（TorchScript / torch.compile）快？
>
> 🎯 **你**：Eager 模式下每个算子都是独立的 kernel launch，中间结果要来回写显存，kernel launch overhead 和内存带宽成为瓶颈。图模式通过编译器看到完整的计算图，可以做**算子融合**——比如把 `conv + bn + relu` 三个算子合成一个 kernel，中间结果存在寄存器/Shared Memory 里不写出，显存带宽直接省掉。

---

### 2. XLA：Google 的编译器野心

XLA（Accelerated Linear Algebra）是 Google 为 TensorFlow 和 JAX 设计的领域专用编译器。

**核心架构：**
```
TensorFlow/PyTorch 计算图
        ↓
   XLA HLO（High Level Optimizer）
   - 代数化简（Algebraic Simplification）
   - 算子融合（Fusion）
   - 布局优化（Layout Optimization）
   - 内存调度（Buffer Scheduling）
        ↓
   LLVM IR（后端）
        ↓
   目标机器码（GPU/TPU/CPU）
```

**XLA 的关键优化：**

| 优化 | 说明 | 效果 |
|---|---|---|
| **算子融合** | 把 element-wise 算子（add, mul, relu）合并成一个 kernel | 减少 kernel launch，省掉中间显存读写 |
| **常量折叠** | 编译时算出常量表达式的值 | 减少运行时计算 |
| **公共子表达式消除（CSE）** | 相同计算只算一次 | 减少冗余计算 |
| **内存调度** | 分析 tensor 生命周期，复用显存 buffer | 降低显存占用 |
| **布局转换消除** | 减少 NCHW ↔ NHWC 来回转换 | 提升内存局部性 |

> 🎯 **面试金句**："XLA 的核心哲学是「在 HLO 层做尽可能多的硬件无关优化，再交给 LLVM 做硬件相关 codegen」。"

**XLA 的两种模式：**
- **JIT**：运行时捕获计算图，实时编译（TensorFlow 默认）
- **AOT**：提前编译，适合部署场景（移动端、边缘设备）

---

### 3. TVM：开源社区的明星

TVM（Tensor Virtual Machine）是 Apache 基金会项目，由陈天奇等人发起，核心是**「自动代码生成 + 自动调度搜索」**。

**TVM 软件栈：**
```
Relay / Relax（计算图层，类 ONNX）
    ↓
Tensor Expression（张量表达式，描述计算逻辑）
    ↓
Schedule（调度原语：split/reorder/vectorize/unroll/parallel）
    ↓
AutoTVM / Ansor（自动搜索最优 Schedule）
    ↓
TIR（Tensor IR）
    ↓
目标代码生成（CUDA/OpenCL/Vulkan/LLVM C）
```

**TVM 的三层抽象：**

| 层级 | 代表 | 作用 |
|---|---|---|
| **计算图层** | Relay / Relax | 图级优化（算子融合、常量折叠、死代码消除） |
| **调度层** | TE + Schedule | 定义「怎么算」——循环变换、并行化、向量化 |
| **代码生成层** | TIR | 低级 IR，直接对应目标硬件指令 |

**AutoTVM vs Ansor：**

| 特性 | AutoTVM | Ansor |
|---|---|---|
| 搜索空间 | 用户手动定义模板 | 自动推导搜索空间 |
| 学习成本 | 高，需手写 schedule 模板 | 低，全自动 |
| 效果 | 模板好则效果极好 | 通用性强，对复杂算子更友好 |
| 原理 | 基于模板的贝叶斯优化 | 基于进化算法的自动搜索 |

> 💬 **面试官**：TVM 的 Schedule 是什么？为什么需要它？
>
> 🎯 **你**：Schedule 描述的是「同一个计算，用不同的循环组织方式执行」。比如矩阵乘法，我可以把外层循环 split 成小块（tiling），让数据 fits in cache；也可以 reorder 循环顺序，改善内存局部性；还可以 vectorize 内层循环，用 SIMD 加速。TVM 把「计算是什么」和「怎么调度」分离，让你可以灵活探索不同的优化策略，然后用 AutoTVM 自动搜索最优组合。

---

### 4. MLIR：编译器基础设施的元框架

MLIR（Multi-Level Intermediate Representation）是 LLVM 项目的一部分，由 Chris Lattner（Swift/LLVM 作者）主导设计。

**MLIR 的核心理念：不是做一个编译器，而是做「编译器的编译器」。**

**Dialect 系统：**
```mlir
// TensorFlow Dialect
%0 = "tf.Conv2D"(%input, %filter) {strides = [1,1]} : (tensor<*xf32>, tensor<*xf32>) -> tensor<*xf32>

// 转换为 HLO Dialect
%1 = "xla.conv"(%input, %filter) ...

// 转换为 LLVM IR Dialect
%2 = llvm.call @conv_kernel(...) ...
```

MLIR 允许你定义任意层级的 IR（Dialect），并在不同 Dialect 之间做转换（Lower）：

| Dialect | 层级 | 用途 |
|---|---|---|
| **TensorFlow Dialect** | 框架层 | 直接从 TF 图导入 |
| **HLO Dialect** | 图优化层 | XLA 风格的算子级 IR |
| **Linalg Dialect** | 线性代数层 | 结构化线性代数运算 |
| **SCF Dialect** | 控制流层 | Structured Control Flow（循环/条件） |
| **Affine Dialect** | 多面体层 | 多面体模型分析和变换 |
| **LLVM IR Dialect** | 代码生成层 | 对接 LLVM 后端 |

> 🎯 **面试金句**："MLIR 不是替代 LLVM，而是补上了 LLVM 缺少的中间层级——LLVM 只有 C-like 的底层 IR，MLIR 让你可以在「计算图」「线性代数」「循环变换」多个层级上定义 IR 并渐进式下降。"

**MLIR 的优势：**
- **渐进式 Lowering**：从高层 Dialect 逐步下降到底层，每层只做最擅长的优化
- **可扩展性**：新硬件只需定义新 Dialect 和转换 Pass
- **复用性**：不同前端（TF/PyTorch/ONNX）可以共享同一套中间 Dialect

---

### 5. 三巨头对比速查表

| 维度 | XLA | TVM | MLIR |
|---|---|---|---|
| **发起方** | Google | Apache (陈天奇等) | LLVM (Chris Lattner) |
| **定位** | 端到端编译器 | 端到端编译器 | 编译器基础设施/框架 |
| **IR 层级** | HLO（单层高级 IR） | Relay→TE→TIR（三层） | 多层 Dialect（可任意扩展） |
| **核心优化** | 算子融合、布局优化、内存调度 | 自动调度搜索（AutoTVM/Ansor） | Dialect 转换、渐进式 Lowering |
| **自动调优** | 有限（主要靠启发式） | 强（AutoTVM / Ansor） | 依赖上层实现 |
| **硬件支持** | TPU/GPU/CPU | 广泛（GPU/ARM/FPGA/ASIC） | 任意（需实现 Dialect） |
| **前端** | TensorFlow / JAX | PyTorch/ONNX/TensorFlow/MLX | 任意（MLIR 是基础设施） |
| **适用场景** | TPU 训练、JAX 研究 | 边缘部署、自定义加速器 | 构建新的 AI 编译器 |

---

### 6. 高频面试连环问

#### Q1：算子融合（Operator Fusion）是什么？为什么重要？

> 🎯 **你**：算子融合是把计算图中相邻的多个算子合并成一个 kernel。比如 `conv → bn → relu` 三个算子，eager 模式下要 launch 3 个 kernel，中间结果写回显存 2 次；融合后只需要 1 个 kernel，中间结果存在寄存器或 Shared Memory 里。显存带宽往往是瓶颈，融合后直接省掉中间显存读写，通常能提速 1.5-3x。

**可融合的pattern：**
- Element-wise + Element-wise：`add → relu` ✅
- Reduce + Element-wise：`softmax` 内部融合 ✅
- Conv + BN + ReLU：经典融合 ✅
- Conv + Conv：通常 ❌（计算密度已经够高，融合收益有限）

#### Q2：XLA 和 TVM 怎么选？

> 🎯 **你**：如果是 TPU 训练或 JAX 生态，XLA 是首选，Google 深度优化了 TPU 后端。如果需要部署到边缘设备、ARM、FPGA 或自定义 ASIC，TVM 更灵活，AutoTVM 能针对具体硬件自动搜索最优 schedule。如果是要**从零构建一个新的 AI 编译器**，MLIR 是最合适的基础设施。

#### Q3：什么是 Polyhedral Compilation（多面体编译）？

> 🎯 **你**：多面体编译是用数学方法（整数线性规划）分析和优化循环嵌套。把循环迭代空间建模为多面体，依赖关系建模为约束，然后做循环变换（tiling、reorder、skewing）来优化局部性和并行性。MLIR 的 Affine Dialect 就是基于多面体模型，TVM 的 Schedule 也借鉴了类似思想。它的优势是能处理复杂的循环依赖，缺点是计算复杂度高，只适合仿射循环（下标是线性表达式）。

#### Q4：显存优化在深度学习编译器里怎么做？

> 🎯 **你**：三个层面：
> 1. **算子融合**：中间结果不写出显存，最直接的节省
> 2. **内存复用（Buffer Reuse）**：分析 tensor 生命周期，一个 tensor 死后复用它的显存给后面的 tensor（类似内存池）
> 3. **重计算 vs 重存（Recomputation vs Rematerialization）**：反向传播时，如果某些中间结果占用显存太大，可以选择在前向时只存 checkpoint，反向时重算——这就是 Gradient Checkpointing 的编译器自动版

#### Q5：模型量化在编译器层面怎么做？

> 🎯 **你**：编译器层面的量化通常指 PTQ（Post-Training Quantization）：
> 1. **权重量化**：把 FP32 权重离线量化为 INT8/FP16，做校准（calibration）找最优缩放因子
> 2. **激活量化**：统计典型输入下的激活值分布，确定 per-tensor 或 per-channel 的缩放
> 3. **算子融合量化**：把 `conv + bn + relu` 融合后统一量化，避免多次反量化/重量化的精度损失
> 4. **目标代码生成**：生成 INT8 TensorCore / DP4A 指令
>
> TVM 的 BYOC（Bring Your Own Codegen）和 MLIR 的 Dialect 都可以接入第三方量化库（如 NVIDIA TensorRT、ARM Ethos）。

---

### 7. 一句话速答模板

| 问题 | 一句话速答 |
|---|---|
| XLA 的核心优势？ | 与 TPU 深度协同，HLO 层的算子融合和布局优化极强。 |
| TVM 的核心优势？ | 自动调度搜索（AutoTVM/Ansor），对新硬件和边缘设备支持好。 |
| MLIR 的核心优势？ | 不是编译器是框架，Dialect 系统让构建领域专用编译器变得模块化。 |
| 算子融合的收益从哪来？ | 省掉中间结果的显存读写，减少 kernel launch overhead。 |
| AutoTVM 和 Ansor 的区别？ | AutoTVM 需要手写 schedule 模板，Ansor 全自动推导搜索空间。 |
| 为什么需要多级 IR？ | 不同层级的优化关注点不同——图级做融合，循环级做局部性，指令级做向量化。 |
| MLIR 的 Dialect 是什么？ | 一种可扩展的 IR 定义机制，让不同领域（TF/HLO/线性代数/LLVM）可以在同一框架下共存和转换。 |
| 深度学习编译器和传统编译器的本质区别？ | 传统编译器优化标量/向量代码；DL 编译器优化张量计算图，核心挑战是算子融合、内存调度和并行映射。 |

---

## 今日小结

- **算法**：栈/递归下降解析嵌套结构——遇到 `(` 开启新作用域，`)` 闭合时合并。这是编译器符号表和作用域链的简化版。
- **深度学习编译器**：
  - **XLA**：Google 出品，HLO 层做算子融合和布局优化，TPU 训练首选
  - **TVM**：自动调度搜索是王牌，AutoTVM/Ansor 让「手写 CUDA」变成「自动搜 CUDA」
  - **MLIR**：编译器的元框架，Dialect 系统让多层级 IR 和渐进式 Lowering 成为可能
- **核心洞察**：AI 编译器的优化和传统编译器一脉相承——融合是内联的延伸，内存调度是寄存器分配的放大，自动调优是 profile-guided optimization 的自动化。

> 明天（Day 99）：词法分析与正则引擎实现 🔜

---

## 参考资源

- [TVM 官方文档](https://tvm.apache.org/docs/)
- [MLIR 官方文档](https://mlir.llvm.org/)
- [XLA 架构概述](https://www.tensorflow.org/xla/architecture)
- 陈天奇：《深度学习编译器的演进》
- Chris Lattner：《MLIR: A Compiler Infrastructure for the End of Moore's Law》
