# Day 91 — 实现 Trie + RAG 与向量检索系统设计

> **日期**: 2026-09-03  
> **Week**: 14（人工智能与大模型工程 🧠）  
> **主题**: Trie 前缀树实现 + RAG 架构与向量检索核心原理

---

## 📌 今日算法题：实现 Trie（前缀树）

### 题目描述

[LeetCode 208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

Trie（发音类似 "try"），或称前缀树或字典树，是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补全和拼写检查。

请你实现 Trie 类：
- `Trie()` 初始化前缀树对象
- `void insert(String word)` 向前缀树中插入字符串 `word`
- `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即，在检索之前已经插入）；否则，返回 `false`
- `boolean startsWith(String prefix)` 如果之前已经插入的字符串 `word` 的前缀之一为 `prefix`，返回 `true`；否则，返回 `false`

### 示例

```
输入
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
输出
[null, null, true, false, true, null, true]

解释
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 True
trie.search("app");     // 返回 False
trie.startsWith("app"); // 返回 True
trie.insert("app");
trie.search("app");     // 返回 True
```

### 解题思路

**核心思想**：Trie 的每个节点代表一个字符，从根节点到任意节点的路径形成一个字符串前缀。通过共享前缀来节省空间。

**节点设计**：
- `children[26]`：指向子节点的指针数组（假设只包含小写字母）
- `isEnd`：标记当前节点是否是一个完整单词的结尾

**操作复杂度**：
- 插入：`O(m)`，m 为单词长度
- 查找：`O(m)`
- 前缀匹配：`O(m)`

**空间复杂度**：`O(N × m)`，N 是单词数，m 是平均长度。最坏情况下每个字符都不共享。

### 代码实现（Go）

```go
package main

// TrieNode 前缀树节点
type TrieNode struct {
	children [26]*TrieNode
	isEnd    bool
}

// Trie 前缀树
type Trie struct {
	root *TrieNode
}

// Constructor 初始化 Trie
func Constructor() Trie {
	return Trie{root: &TrieNode{}}
}

// Insert 插入一个单词
func (t *Trie) Insert(word string) {
	node := t.root
	for _, ch := range word {
		idx := ch - 'a'
		if node.children[idx] == nil {
			node.children[idx] = &TrieNode{}
		}
		node = node.children[idx]
	}
	node.isEnd = true
}

// Search 查找完整单词
func (t *Trie) Search(word string) bool {
	node := t.searchPrefix(word)
	return node != nil && node.isEnd
}

// StartsWith 查找是否有指定前缀
func (t *Trie) StartsWith(prefix string) bool {
	return t.searchPrefix(prefix) != nil
}

// searchPrefix 查找前缀对应的节点
func (t *Trie) searchPrefix(prefix string) *TrieNode {
	node := t.root
	for _, ch := range prefix {
		idx := ch - 'a'
		if node.children[idx] == nil {
			return nil
		}
		node = node.children[idx]
	}
	return node
}
```

### 进阶扩展：带通配符的搜索

```go
// SearchWithWildcard 支持 '.' 匹配任意字符
func (t *Trie) SearchWithWildcard(word string) bool {
	var dfs func(node *TrieNode, i int) bool
	dfs = func(node *TrieNode, i int) bool {
		if i == len(word) {
			return node.isEnd
		}
		ch := word[i]
		if ch == '.' {
			// 尝试所有可能的子节点
			for _, child := range node.children {
				if child != nil && dfs(child, i+1) {
					return true
				}
			}
			return false
		}
		idx := ch - 'a'
		if node.children[idx] == nil {
			return false
		}
		return dfs(node.children[idx], i+1)
	}
	return dfs(t.root, 0)
}
```

### 复杂度分析

| 操作 | 时间复杂度 | 空间复杂度 |
|---|---|---|
| Insert | O(m) | O(m) |
| Search | O(m) | O(1) |
| StartsWith | O(m) | O(1) |
| SearchWithWildcard | O(26^m) 最坏情况 | O(m) 递归栈 |

### 应用场景

- 🔍 **自动补全**：搜索框实时提示（Google/Bing 搜索建议）
- 📝 **拼写检查**：判断单词是否存在，或查找最接近的建议
- 📇 **IP 路由查找**：最长前缀匹配
- 🤖 **RAG 文档检索**：构建文档词汇索引，快速过滤候选文档

---

## 🎯 面试技巧：RAG 与向量检索系统设计

### 1. RAG（检索增强生成）核心架构

**为什么需要 RAG？**
- LLM 有知识截止和幻觉问题
- 企业私有数据无法直接注入模型训练
- 需要可溯源、可更新的知识

**RAG 标准流程**：

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│  用户查询    │────▶│  Query理解/  │────▶│  向量检索   │
│  "公司年假   │     │  改写/扩展   │     │  ANN 搜索   │
│   政策是什么" │     │              │     │             │
└─────────────┘     └──────────────┘     └──────┬──────┘
                                                │
                       ┌────────────────────────┘
                       ▼
              ┌─────────────────┐
              │  召回 Top-K     │
              │  相关文档片段    │
              └────────┬────────┘
                       │
                       ▼
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│   生成回答   │◀────│  Prompt构建   │◀────│  文档重排序   │
│ （带引用溯源）│     │ 上下文+指令   │     │  精排优化     │
└─────────────┘     └──────────────┘     └─────────────┘
```

### 2. 向量检索核心原理

**Embedding 模型**：
- **文本**：OpenAI text-embedding-3 / BGE / M3E / GTE
- **多模态**：CLIP（图文对齐）
- **选型指标**：向量维度（768/1024/1536）、上下文长度、语种支持、MTEB 榜单排名

**相似度度量**：
| 度量方式 | 公式 | 适用场景 |
|---|---|---|
| 余弦相似度 | cos(θ) = A·B / (\|A\|·\|B\|) | 方向比绝对大小重要，最常用 |
| 欧氏距离 | \|A - B\|₂ | 绝对距离有意义 |
| 点积 | A·B | 已归一化时等价于余弦 |

**ANN（近似最近邻）算法**：

| 算法 | 核心思想 | 时间复杂度 | 空间复杂度 | 代表实现 |
|---|---|---|---|---|
| **HNSW** | 多层导航小世界图，贪心跳转 | O(log N) | O(N × d) | faiss, milvus |
| **IVF** | 倒排文件：先聚类再搜索 | O(√N) ~ O(N/k) | O(N × d) | faiss |
| **PQ** | 乘积量化：向量分段压缩 | O(N × M/k*) | 极低 | faiss |
| **LSH** | 局部敏感哈希，哈希碰撞=相似 | O(1) 查询 | 较高 | Annoy |

> 💡 **工业界标配**：HNSW + IVF 组合（如 faiss.IndexIVFPQ）或纯 HNSW（Milvus/Pinecone 默认）

### 3. 向量数据库选型对比

| 特性 | Milvus/Zilliz | Pinecone | Weaviate | Chroma | pgvector |
|---|---|---|---|---|---|
| **开源** | ✅ Apache 2.0 | ❌ 商业 | ✅ | ✅ | ✅ |
| **部署** | K8s/Docker/托管 | 全托管 | Docker/K8s | 嵌入式/Server | PostgreSQL 插件 |
| **索引类型** | HNSW/IVF/DISKANN | 自动优化 | HNSW | HNSW | HNSW/IVF |
| **混合搜索** | ✅ 向量+标量过滤 | ✅ 元数据过滤 | ✅ | ✅ | ✅ SQL 过滤 |
| **分布式** | ✅ 原生 | ✅ 托管 | ⚠️ Enterprise | ❌ | ❌ |
| **适合场景** | 大规模生产 | 快速启动 | 知识图谱 | 原型/MVP | 已有 PG 生态 |

### 4. RAG 系统优化要点

**文档切分策略**：
- **固定长度**：简单但可能切断语义（512/1024 tokens）
- **递归字符切分**：按段落→句子→单词层级切分（LangChain 默认）
- **语义切分**：用模型判断语义边界，切分点位于语义转折处
- **重叠窗口**：相邻 chunk 重叠 10-20%，避免边界信息丢失

**查询优化**：
- **Query 改写**：LLM 扩展同义词、纠正错别字
- **HyDE（假设文档嵌入）**：让 LLM 先生成假设答案，再 embedding 检索
- **多路召回**：向量检索 + 关键词检索（BM25）+ 图谱检索，结果融合

**重排序（Rerank）**：
- 初召回 Top-100（快但粗）
- Cross-Encoder 精排 Top-5（慢但准）
- 代表模型：BGE-Reranker, Cohere Rerank

### 5. 高频面试连环问

> **Q1: RAG 和 Fine-tuning 怎么选？**
> 
> A: 数据频繁更新/私有数据 → RAG；数据固定且充足/需要行为改变 → Fine-tuning。两者可结合：RAG 提供上下文，Fine-tuned 模型负责生成风格。

> **Q2: 向量检索为什么快？和传统数据库索引的区别？**
> 
> A: 向量检索用 ANN 近似算法，牺牲极少量精度换取数量级加速。传统 B+树索引依赖精确比较和范围查询，在高维空间（>100维）中"维度灾难"导致效率骤降。

> **Q3: Embedding 模型选多大维度？**
> 
> A: 维度越高表达能力越强，但存储和计算成本指数增长。1536（OpenAI）是常见平衡点，768（BGE）性价比更高。实际应通过召回率测试决定。

> **Q4: 如何处理多语言文档？**
> 
> A: 用多语言 Embedding 模型（如 BGE-M3, LaBSE），将所有语言映射到同一向量空间，实现跨语言检索。

> **Q5: RAG 的召回率和准确率怎么平衡？**
> 
> A: 召回端扩大 Top-K（如 100→200），精排端加强重排序模型。或者调整 HNSW 参数 `ef`（搜索时探索的邻居数），越大越准但越慢。

> **Q6: 冷启动问题怎么解决？**
> 
> A: 新文档入库立即 embedding 并插入向量库；新用户/新 query 可通过 few-shot prompt 或规则兜底。

---

## 📝 今日速记卡

| 考点 | 一句话速答 |
|---|---|
| Trie 时间复杂度 | 插入/查找均为 O(m)，m 为字符串长度 |
| Trie 空间优化 | 用 map/字典代替固定数组，压缩前缀（Radix Tree） |
| RAG 解决什么问题 | LLM 知识截止 + 幻觉 + 私有数据 |
| ANN 代表算法 | HNSW（图）、IVF（聚类）、LSH（哈希） |
| HNSW 核心思想 | 多层导航图，贪心跳转近似最近邻 |
| Embedding 选型 | MTEB 榜单 + 语种 + 上下文长度 + 维度 |
| 混合搜索 | 向量检索 + BM25 关键词 + 结果融合（RRF） |
| 文档切分黄金法则 | 语义完整 + 适当重叠 + 长度适中 |
| 重排序作用 | 初召回求快（ANN）→ 精排求准（Cross-Encoder） |
| RAG vs Fine-tuning | 数据更新频繁选 RAG，行为模式固定选 FT |

---

> **明日预告**: Day 92 — Agent 系统设计与工具调用 🔧（Week 14 继续）
