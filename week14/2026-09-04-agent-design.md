# Day 92 — 基本计算器 II + Agent 系统设计与工具调用

> **日期**: 2026-09-04  
> **Week**: 14（人工智能与大模型工程 🧠）  
> **主题**: 栈实现表达式求值 + Agent 架构设计、工具调用与多智能体协作

---

## 📌 今日算法题：基本计算器 II

### 题目描述

[LeetCode 227. 基本计算器 II](https://leetcode.cn/problems/basic-calculator-ii/)

给你一个字符串表达式 `s`，请实现一个基本计算器来计算并返回它的值。

整数除法仅保留整数部分。

你可以假设给定的表达式总是有效的。所有中间结果将在 `[-2^31, 2^31 - 1]` 的范围内。

**注意**：不允许使用任何将字符串作为数学表达式求值的内置函数，比如 `eval()`。

### 示例

```
输入：s = "3+2*2"
输出：7

输入：s = " 3/2 "
输出：1

输入：s = " 3+5 / 2 "
输出：5
```

### 解题思路

**核心思想**：乘除优先级高于加减。遍历字符串时，遇到数字先缓存；遇到运算符时，根据上一个运算符决定如何处理缓存的数字：
- `+`：数字入栈（待后续相加）
- `-`：负数入栈（待后续相加）
- `*`：栈顶弹出，乘当前数字后入栈
- `/`：栈顶弹出，除以当前数字后入栈

最后栈中所有数字相加即为结果。

**为什么用栈？**
- 加减法可以延迟计算（先入栈）
- 乘除法必须立即计算（先弹出栈顶）
- 最终所有元素都是「待相加」的项

### 代码实现（Go）

```go
package main

import (
	"fmt"
	"unicode"
)

func calculate(s string) int {
	stack := []int{}
	num := 0
	sign := '+' // 上一个运算符，初始为 '+'

	for i, ch := range s {
		if unicode.IsDigit(ch) {
			num = num*10 + int(ch-'0')
		}

		// 遇到运算符或到达末尾，处理上一个运算符
		if (!unicode.IsDigit(ch) && ch != ' ') || i == len(s)-1 {
			switch sign {
			case '+':
				stack = append(stack, num)
			case '-':
				stack = append(stack, -num)
			case '*':
				// 弹出栈顶，乘法后立即压回
				top := stack[len(stack)-1]
				stack = stack[:len(stack)-1]
				stack = append(stack, top*num)
			case '/':
				// 弹出栈顶，除法后立即压回（保留整数部分，向零截断）
				top := stack[len(stack)-1]
				stack = stack[:len(stack)-1]
				stack = append(stack, top/num)
			}
			// 更新当前运算符，重置数字
			sign = ch
			num = 0
		}
	}

	// 栈中所有数字相加
	result := 0
	for _, v := range stack {
		result += v
	}
	return result
}

func main() {
	fmt.Println(calculate("3+2*2"))       // 7
	fmt.Println(calculate(" 3/2 "))       // 1
	fmt.Println(calculate(" 3+5 / 2 "))  // 5
	fmt.Println(calculate("14-3/2"))      // 13 (14 - 1 = 13)
}
```

### 进阶扩展：支持括号的计算器（LeetCode 224）

```go
// 递归处理括号：遇到 '(' 递归，遇到 ')' 返回
func calculateWithParens(s string) int {
	var dfs func(int) (int, int) // 返回 (结果, 下一个索引)
	dfs = func(i int) (int, int) {
		stack := []int{}
		num := 0
		sign := '+'

		for i < len(s) {
			ch := s[i]
			if ch >= '0' && ch <= '9' {
				num = num*10 + int(ch-'0')
			}

			if ch == '(' {
				// 递归计算括号内
				inner, nextIdx := dfs(i + 1)
				num = inner
				i = nextIdx
			} else if (!isDigit(ch) && ch != ' ') || i == len(s)-1 {
				switch sign {
				case '+':
					stack = append(stack, num)
				case '-':
					stack = append(stack, -num)
				case '*':
					top := stack[len(stack)-1]
					stack = stack[:len(stack)-1]
					stack = append(stack, top*num)
				case '/':
					top := stack[len(stack)-1]
					stack = stack[:len(stack)-1]
					stack = append(stack, top/num)
				}
				sign = ch
				num = 0
				if ch == ')' {
					break // 括号结束，返回结果
				}
			}
			i++
		}

		result := 0
		for _, v := range stack {
			result += v
		}
		return result, i
	}

	res, _ := dfs(0)
	return res
}
```

### 复杂度分析

| 指标 | 复杂度 | 说明 |
|---|---|---|
| 时间复杂度 | O(n) | 遍历字符串一次 |
| 空间复杂度 | O(n) | 栈存储所有数字，最坏全为加减 |

### 核心技巧总结

- 🎯 **延迟计算 vs 立即计算**：加减可以等，乘除必须马上算
- 🎯 **上一个运算符**：遍历到当前运算符时，处理的是「上一个」运算符
- 🎯 **向零截断**：Go 的 `/` 对整数本身就是向零截断，其他语言需注意

---

## 🎯 面试技巧：Agent 系统设计与工具调用

### 1. 什么是 AI Agent？与 LLM 的核心区别

**LLM（大语言模型）** = 被动的「回答问题机器」
- 输入 Prompt → 输出文本
- 知识截止、无法执行动作、不能访问外部世界

**Agent（智能体）** = 主动的「观察-思考-行动」循环体
- 能调用工具（搜索引擎、数据库、API）
- 能规划多步任务并执行
- 能根据环境反馈自我修正

```
┌─────────────────────────────────────────┐
│              AI Agent 循环               │
│                                          │
│   ┌──────┐    ┌──────┐    ┌──────┐     │
│   │ 观察  │───▶│ 思考  │───▶│ 行动  │     │
│   │Observe│    │Think │    │ Act  │     │
│   └──┬───┘    └──────┘    └──┬───┘     │
│      │                       │         │
│      └───────────────────────┘         │
│              ↑ 环境反馈                  │
└─────────────────────────────────────────┘
```

> 💡 **一句话区分**：LLM 是「大脑」，Agent 是「大脑 + 手脚 + 记忆」。

### 2. Agent 核心架构：四大组件

| 组件 | 职责 | 代表实现 |
|---|---|---|
| **Planning（规划）** | 将复杂任务拆解为可执行的子步骤 | ReAct, CoT, ToT, Plan-and-execute |
| **Memory（记忆）** | 存储短期上下文和长期知识 | Buffer Memory, Vector DB, Knowledge Graph |
| **Tools（工具）** | 调用外部 API 获取信息或执行动作 | Function Calling, Tool Use, MCP |
| **Action（执行）** | 实际调用工具并处理返回结果 | 代码执行、HTTP 请求、数据库查询 |

**经典架构图**：

```
┌─────────────────────────────────────────────────────────┐
│                      User Input                          │
│              "帮我查一下北京明天的天气，                   │
│               然后发邮件提醒我带伞"                       │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Planning（规划器）                                       │
│  Step 1: 查询北京明天天气 → 调用天气 API                   │
│  Step 2: 写提醒邮件 → 调用邮件 API                        │
│  Step 3: 确认执行结果 → 返回给用户                        │
└─────────────────────────────────────────────────────────┘
                           │
           ┌───────────────┼───────────────┐
           ▼               ▼               ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ 天气 API  │    │ 邮件 API  │    │ 记忆存储  │
    │  (Tool)   │    │  (Tool)   │    │ (Memory) │
    └────┬─────┘    └────┬─────┘    └────┬─────┘
         │               │               │
         └───────────────┴───────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  执行结果汇总 → 生成自然语言回复 → 返回给用户               │
└─────────────────────────────────────────────────────────┘
```

### 3. 推理模式深度对比

| 模式 | 全称 | 核心思想 | 适用场景 |
|---|---|---|---|
| **CoT** | Chain-of-Thought | 让模型「一步一步想」，显式写出推理步骤 | 数学计算、逻辑推理 |
| **ReAct** | Reasoning + Acting | 推理和行动交替：Thought → Action → Observation → ... | 需要工具调用的复杂任务 |
| **ToT** | Tree of Thoughts | 维护多个思考路径，评估后选择最优分支 | 需要探索的复杂决策 |
| **Plan-and-Execute** | 计划-执行分离 | 先制定完整计划，再按步骤执行 | 多步骤、可并行化的任务 |

**ReAct 循环详解**（最主流的 Agent 模式）：

```
用户："2024年诺贝尔奖物理学奖得主是谁？"

Thought 1: 我需要搜索2024年诺贝尔物理学奖的信息。
Action 1: 调用 search_tool(query="2024 Nobel Prize Physics winner")
Observation 1: "John J. Hopfield and Geoffrey E. Hinton were awarded..."

Thought 2: 我已经得到了答案，Hopfield 和 Hinton 获得了2024年诺贝尔物理学奖。
Action 2: 直接回答用户，无需再调用工具。

Final Answer: 2024年诺贝尔物理学奖授予了 John J. Hopfield 和 Geoffrey E. Hinton...
```

### 4. 工具调用（Function Calling）技术细节

**Function Calling 流程**：

```
用户输入 → LLM 解析意图 → 判断需要调用工具
              │
              ▼
    ┌─────────────────────┐
    │  LLM 输出 JSON 格式   │
    │  {                  │
    │    "name": "get_weather",
    │    "arguments": {   │
    │      "city": "北京", │
    │      "date": "明天"  │
    │    }                │
    │  }                  │
    └─────────────────────┘
              │
              ▼
    系统解析 JSON → 调用对应函数 → 获取结果
              │
              ▼
    将结果注入上下文 → LLM 再次推理 → 生成最终回复
```

**关键设计要点**：

1. **Schema 定义**：每个工具需要明确定义名称、描述、参数类型（JSON Schema）
2. **工具选择**：LLM 需要判断「是否需要工具」+「选哪个工具」+「参数填什么」
3. **错误处理**：工具调用失败时，Agent 需要能捕获错误并决定重试或换方案
4. **并行调用**：多个独立工具可以并行执行（如同时查天气和查交通）

**工具定义示例**：

```json
{
  "type": "function",
  "function": {
    "name": "get_weather",
    "description": "获取指定城市的天气信息",
    "parameters": {
      "type": "object",
      "properties": {
        "city": {
          "type": "string",
          "description": "城市名称，如'北京'"
        },
        "date": {
          "type": "string",
          "description": "日期，如'今天'、'明天'、'2024-09-04'"
        }
      },
      "required": ["city"]
    }
  }
}
```

### 5. 记忆系统（Memory）设计

Agent 的记忆分为三层：

| 记忆类型 | 存储内容 | 实现方式 | 生命周期 |
|---|---|---|---|
| **短期记忆** | 当前对话上下文 | 滑动窗口 Buffer | 单次会话 |
| **工作记忆** | 任务执行中的关键信息 | Key-Value 存储 | 单次任务 |
| **长期记忆** | 用户偏好、历史事实 | 向量数据库 | 持久化 |

**记忆检索流程**：

```
用户输入 → Embedding → 向量检索 → 召回 Top-K 相关记忆
              │
              ▼
    ┌─────────────────────┐
    │  相关记忆注入 Prompt  │
    │  "用户之前提到对花生过敏  │
    │   推荐餐厅时请避开"      │
    └─────────────────────┘
```

**记忆设计的面试要点**：
- 上下文窗口有限，不能全塞进去 → 需要摘要 + 检索
- 用户偏好需要持久化 → 向量数据库（如 Chroma, Pinecone）
- 记忆冲突时怎么办？→ 时间戳加权、显式确认机制

### 6. 多 Agent 协作架构

复杂任务需要多个 Agent 协作完成：

```
┌──────────────────────────────────────────────┐
│           主管 Agent（Supervisor）              │
│     任务拆解 + 分配 + 结果汇总 + 质量检查         │
└──────────────────────────────────────────────┘
            │         │         │
            ▼         ▼         ▼
    ┌──────────┐ ┌──────────┐ ┌──────────┐
    │ 研究 Agent │ │ 写作 Agent │ │ 审核 Agent │
    │ (Research)│ │ (Writer)  │ │ (Reviewer)│
    │ 查资料    │ │ 写文档    │ │ 检查错误  │
    └──────────┘ └──────────┘ └──────────┘
```

**常见协作模式**：

| 模式 | 说明 | 例子 |
|---|---|---|
| **主管-工人** | 一个 Supervisor 分配任务给多个 Worker | AutoGen |
| **流水线** | Agent A 输出 → Agent B 输入 → Agent C | 研报生成：研究→写作→排版 |
| **投票/辩论** | 多个 Agent 独立给出答案，投票决出最终结果 | 代码审查、事实核查 |
| **市场/拍卖** | Agent 竞标任务，价高者得 | 资源调度场景 |

### 7. 高频面试连环问

> **Q1: ReAct 和 CoT 有什么区别？什么时候用 ReAct？**
>
> A: CoT 是「纯推理」，只在模型内部一步步想；ReAct 是「推理+行动交替」，模型每想一步就可能调用外部工具获取新信息。当任务需要实时信息（如查天气、查股价）或需要执行动作（如发邮件、订机票）时，必须用 ReAct。

> **Q2: Function Calling 是怎么实现的？LLM 怎么知道该调哪个函数？**
>
> A: 实现层面，LLM 在训练时被教会了输出特定 JSON 格式。推理时，系统将所有可用工具的 Schema 注入 Prompt，LLM 根据用户意图判断是否需要工具、选哪个工具、参数是什么。本质上是「结构化生成」——模型在受限的 token 空间内做选择。

> **Q3: Agent 调用工具失败了怎么办？**
>
> A: 三层容错：① 工具层捕获异常，返回结构化错误信息给 Agent；② Agent 层判断错误类型（参数错误→重试/参数修复，服务错误→降级/换工具，权限错误→询问用户）；③ 设置最大重试次数和超时，避免无限循环。

> **Q4: 怎么防止 Agent 陷入无限循环？**
>
> A: ① 设置最大迭代次数（如 10 轮）；② 设置超时时间；③ 检测循环状态（如果连续两次 Thought 相同，强制退出）；④ 给 Agent 明确的「终止条件」和「放弃策略」（如"如果找不到答案，直接告诉用户"）。

> **Q5: Agent 的记忆怎么设计？用户问"你还记得我之前说的吗？"**
>
> A: 短期记忆用滑动窗口保留最近 N 轮对话；长期记忆用 Embedding 模型将关键信息存入向量库，每次对话前检索相关记忆注入上下文。对于「你之前说过」这类问题，需要显式存储用户事实到 Key-Value 存储（如"用户偏好"表）。

> **Q6: 多 Agent 系统怎么避免冲突和重复工作？**
>
> A: ① 明确分工：每个 Agent 有清晰的职责边界；② 共享状态：通过中央状态机或消息总线同步进度；③ 主管协调：Supervisor Agent 负责分配和兜底；④ 版本控制：对共享文档使用类似 Git 的合并机制。

> **Q7: Agent 和 RAG 是什么关系？**
>
> A: RAG 是 Agent 的一个组件（记忆/知识部分）。Agent 可以调用 RAG 系统来检索知识，但 Agent 还包含规划、工具调用、执行等能力。简单说：RAG = 给模型装「外脑」，Agent = 给模型装「外脑 + 手脚 + 记忆」。

---

## 📝 今日速记卡

| 考点 | 一句话速答 |
|---|---|
| LLM vs Agent | LLM 是被动的回答机器，Agent 是观察-思考-行动的循环体 |
| ReAct 核心 | Reasoning + Acting 交替：Thought → Action → Observation |
| Function Calling | LLM 输出结构化 JSON → 系统解析并调用对应函数 |
| Agent 四大组件 | Planning（规划）、Memory（记忆）、Tools（工具）、Action（执行） |
| 记忆三层设计 | 短期记忆（Buffer）、工作记忆（KV 存储）、长期记忆（向量库） |
| 防无限循环 | 最大迭代次数 + 超时 + 循环检测 + 终止条件 |
| 多 Agent 协作 | 主管-工人、流水线、投票辩论、市场拍卖四种模式 |
| Agent vs RAG | RAG 是 Agent 的知识组件，Agent 范围更大 |
| 工具调用失败处理 | 参数错误→修复重试，服务错误→降级，权限错误→询问用户 |
| 计划模式选型 | 简单任务用 CoT，需要工具用 ReAct，复杂探索用 ToT |

---

> **明日预告**: Day 93 — 模型部署与推理优化 🚀（Week 14 完结）
