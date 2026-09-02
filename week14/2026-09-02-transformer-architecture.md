# Day 90 — 最长递增子序列 + Transformer 与大模型架构

> 📅 2026-09-02 | Week 14 Day 90 | 主题：人工智能与大模型工程 🧠

---

## 今日算法题：最长递增子序列 (Longest Increasing Subsequence)

### 题目描述

给你一个整数数组 `nums`，找到其中最长严格递增子序列的长度。

**子序列** 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。

**示例：**
```
输入: nums = [10, 9, 2, 5, 3, 7, 101, 18]
输出: 4
解释: 最长递增子序列是 [2, 3, 7, 101]，因此长度为 4。

输入: nums = [0, 1, 0, 3, 2, 3]
输出: 4
```

**进阶：** 你能将算法的时间复杂度降低到 `O(n log n)` 吗？

---

### 解题思路

#### 方法一：动态规划 O(n²)

**状态定义：** `dp[i]` 表示以 `nums[i]` 结尾的最长递增子序列的长度。

**状态转移：**
```
dp[i] = max(dp[j] + 1) for all j < i and nums[j] < nums[i]
```

对于每个位置 `i`，往前找所有比它小的元素 `j`，取 `dp[j] + 1` 的最大值。

**初始化：** 每个元素自身构成长度为 1 的子序列，`dp[i] = 1`。

**答案：** `max(dp)`。

#### 方法二：贪心 + 二分查找 O(n log n) ⭐ 面试重点

核心思想：**维护一个递增数组 `tails`，`tails[i]` 表示长度为 `i+1` 的递增子序列的最小结尾元素。**

为什么维护最小结尾元素？因为结尾元素越小，后面越容易被接上，越有可能形成更长的递增子序列。

**算法流程：**
1. 遍历每个数字 `num`
2. 在 `tails` 中找到第一个 `≥ num` 的位置 `pos`（二分查找）
3. 用 `num` 替换 `tails[pos]`（贪心：让同样长度的子序列结尾更小）
4. 如果 `num` 比 `tails` 中所有元素都大，则追加到末尾

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]

步骤：
10 -> tails = [10]
9  -> tails = [9]        (替换 10，长度为1的结尾更小)
2  -> tails = [2]        (替换 9)
5  -> tails = [2, 5]     (5 > 2，追加)
3  -> tails = [2, 3]     (替换 5，长度为2的结尾更小)
7  -> tails = [2, 3, 7]  (7 > 3，追加)
101-> tails = [2, 3, 7, 101] (追加)
18 -> tails = [2, 3, 7, 18]  (替换 101)

答案 = tails.length = 4
```

> ⚠️ **关键点：** `tails` 数组本身不是 LIS，但长度等于 LIS 长度。这是贪心算法的巧妙之处。

---

### 代码实现

#### Python — O(n²) DP

```python
def lengthOfLIS(nums: List[int]) -> int:
    n = len(nums)
    dp = [1] * n  # 每个元素自身构成长度为1的子序列
    
    for i in range(n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)

# 时间复杂度：O(n²)
# 空间复杂度：O(n)
```

#### Python — O(n log n) 贪心 + 二分

```python
from bisect import bisect_left

def lengthOfLIS(nums: List[int]) -> int:
    tails = []  # tails[i] = 长度为 i+1 的递增子序列的最小结尾
    
    for num in nums:
        # 找第一个 >= num 的位置
        pos = bisect_left(tails, num)
        
        if pos == len(tails):
            # num 比所有结尾都大，可以延长
            tails.append(num)
        else:
            # 替换，让同样长度的子序列结尾更小
            tails[pos] = num
    
    return len(tails)

# 时间复杂度：O(n log n)
# 空间复杂度：O(n)
```

#### Go — O(n log n)

```go
func lengthOfLIS(nums []int) int {
    tails := []int{}
    
    for _, num := range nums {
        // 二分查找第一个 >= num 的位置
        left, right := 0, len(tails)
        for left < right {
            mid := left + (right-left)/2
            if tails[mid] < num {
                left = mid + 1
            } else {
                right = mid
            }
        }
        
        if left == len(tails) {
            tails = append(tails, num)
        } else {
            tails[left] = num
        }
    }
    
    return len(tails)
}
```

#### C++ — O(n log n)

```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;
    
    for (int num : nums) {
        auto it = lower_bound(tails.begin(), tails.end(), num);
        if (it == tails.end()) {
            tails.push_back(num);
        } else {
            *it = num;
        }
    }
    
    return tails.size();
}
```

---

### 复杂度分析

| 方法 | 时间复杂度 | 空间复杂度 | 适用场景 |
|------|-----------|-----------|---------|
| DP O(n²) | O(n²) | O(n) | n ≤ 10⁴，理解基础思路 |
| 贪心+二分 O(n log n) | O(n log n) | O(n) | **面试标准答案**，n ≤ 10⁵ |

---

### 变形题与 Follow-up

**Follow-up 1：如何输出具体的 LIS 序列？**

维护 `prev` 数组记录每个元素的前驱，在 DP 过程中同时记录路径。

```python
def lengthOfLIS_with_path(nums):
    n = len(nums)
    dp = [1] * n
    prev = [-1] * n  # 记录前驱索引
    
    for i in range(n):
        for j in range(i):
            if nums[j] < nums[i] and dp[j] + 1 > dp[i]:
                dp[i] = dp[j] + 1
                prev[i] = j
    
    # 找最大值位置
    max_idx = max(range(n), key=lambda i: dp[i])
    
    # 回溯输出路径
    path = []
    while max_idx != -1:
        path.append(nums[max_idx])
        max_idx = prev[max_idx]
    
    return path[::-1]  # 反转
```

**Follow-up 2：最长连续递增子序列 vs 最长递增子序列**
- **连续**：线性扫描 O(n)，`dp[i] = dp[i-1] + 1 if nums[i] > nums[i-1] else 1`
- **不连续（本题）**：需要回溯前面所有元素

**Follow-up 3：最长递减子序列 / 最长摇摆子序列**
- 递减：条件改为 `nums[j] > nums[i]`，或反转数组
- 摇摆：维护 `up[i]` 和 `down[i]` 两个状态

**Follow-up 4：二维情况 — 俄罗斯套娃信封**
- 先按宽度升序，宽度相同按高度降序，然后对高度求 LIS
- 经典题，见 Day 20 或 LeetCode 354

---

### 面试高频追问

> 💬 **面试官：为什么贪心+二分能得到正确答案？**
>
> 🎯 **你：** 核心在于 `tails[i]` 维护了长度为 `i+1` 的递增子序列的**最小可能结尾**。对于同一个长度的子序列，结尾越小，后面越容易被接上。虽然我们牺牲了"具体序列的正确性"（tails 本身不是 LIS），但我们保证了"长度的正确性"——任何能通过长度为 k 的序列接上的元素，也一定可以通过 tails[k-1] 接上，因为 tails[k-1] ≤ 原序列的结尾。

> 💬 **面试官：如果要严格递增（不能相等）和可以相等有什么区别？**
>
> 🎯 **你：** 严格递增用 `bisect_left`（找第一个 ≥），非严格递增（允许相等）用 `bisect_right`（找第一个 >）。这题要求严格递增，所以用 `bisect_left`。

> 💬 **面试官：如果数组长度是 10⁵，还能优化吗？**
>
> 🎯 **你：** O(n log n) 已经是比较优的了。如果数据范围有限（比如 1~10⁵），可以考虑线段树或树状数组优化 DP，做到 O(n log M) 其中 M 是值域。如果值域也小，可以离散化后用树状数组维护前缀最大值。

---

---

## 面试技巧：Transformer 与大模型架构

### 一、Self-Attention 核心机制

#### 1. Attention 本质是什么？

> 💡 **一句话：Attention 是一种「软寻址」机制，让模型在处理当前位置时，动态地关注输入序列中其他相关位置。**

类比：你在读一篇文章时，眼睛不会逐字匀速移动，而是会根据当前内容自动跳转到相关段落。Attention 就是给模型这种能力。

#### 2. Q、K、V 的含义与计算

```
给定输入 X (seq_len, d_model)

Q = X @ W_Q    (Query: 我要查什么)
K = X @ W_K    (Key: 我有什么)
V = X @ W_V    (Value: 实际内容是什么)

Attention(Q, K, V) = softmax(Q @ K^T / √d_k) @ V
                              ↑
                        缩放因子，防止 softmax 饱和
```

**维度变化：**
```
X:      (seq_len, d_model)
W_Q/K/V:(d_model, d_k)  通常 d_k = d_model / num_heads
Q/K/V:  (seq_len, d_k)
Q@K^T:  (seq_len, seq_len)  ← attention score 矩阵
softmax:(seq_len, seq_len)  ← attention weight 矩阵（每行和为1）
output: (seq_len, d_k)
```

**为什么要除以 √d_k？**
- 当 d_k 很大时，Q@K^T 的点积值会很大
- 导致 softmax 的梯度区域非常平缓（饱和区），梯度消失
- 缩放后数值更稳定，训练更顺畅

#### 3. Masked Attention（因果/自回归 Attention）

Decoder 中使用，确保位置 i 只能看到 ≤ i 的位置：

```python
# 下三角 mask
mask = torch.tril(torch.ones(seq_len, seq_len))  # 下三角为1，上三角为0
scores = scores.masked_fill(mask == 0, float('-inf'))  # 上三角置为-inf
weights = softmax(scores)
```

**为什么需要 mask？**
- 训练时：防止模型"偷看"未来的 token（作弊）
- 推理时：保证自回归生成，每次只基于已生成的内容预测下一个

---

### 二、Multi-Head Attention

#### 1. 为什么需要多头？

> 💡 **一句话：单个注意力头可能只关注一种「关系类型」，多头让模型同时关注多种关系（语法、语义、指代、位置等）。**

类比：一个头的注意力像单一视角的监控，多头像多视角的监控系统，能看到更多维度的信息。

#### 2. 计算流程

```python
# 假设 d_model = 512, num_heads = 8, d_k = d_v = 64

class MultiHeadAttention(nn.Module):
    def __init__(self, d_model=512, num_heads=8):
        super().__init__()
        self.num_heads = num_heads
        self.d_k = d_model // num_heads  # 64
        
        self.W_Q = nn.Linear(d_model, d_model)
        self.W_K = nn.Linear(d_model, d_model)
        self.W_V = nn.Linear(d_model, d_model)
        self.W_O = nn.Linear(d_model, d_model)  # 输出投影
    
    def forward(self, X, mask=None):
        batch_size, seq_len, _ = X.shape
        
        # 1. 线性投影
        Q = self.W_Q(X)  # (B, seq, 512)
        K = self.W_K(X)
        V = self.W_V(X)
        
        # 2. 分头: (B, seq, 512) -> (B, 8, seq, 64)
        Q = Q.view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        K = K.view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        V = V.view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        
        # 3. 每个头独立做 attention
        scores = Q @ K.transpose(-2, -1) / math.sqrt(self.d_k)  # (B, 8, seq, seq)
        
        if mask is not None:
            scores = scores.masked_fill(mask == 0, float('-inf'))
        
        weights = F.softmax(scores, dim=-1)
        out = weights @ V  # (B, 8, seq, 64)
        
        # 4. 合并头: (B, 8, seq, 64) -> (B, seq, 512)
        out = out.transpose(1, 2).contiguous().view(batch_size, seq_len, -1)
        
        # 5. 输出投影
        return self.W_O(out)
```

---

### 三、Positional Encoding（位置编码）

#### 1. 为什么需要位置编码？

> 💡 **Attention 本身是无序的（permutation-invariant），位置编码给模型注入「顺序信息」。**

没有位置编码，"我爱你"和"你爱我"对 Attention 来说是一样的。

#### 2. 原始 Transformer：Sinusoidal 位置编码

```python
def get_sinusoidal_pe(seq_len, d_model):
    pe = torch.zeros(seq_len, d_model)
    position = torch.arange(0, seq_len).unsqueeze(1).float()
    
    # 分奇偶维度使用不同频率
    div_term = torch.exp(
        torch.arange(0, d_model, 2).float() * 
        -(math.log(10000.0) / d_model)
    )
    
    pe[:, 0::2] = torch.sin(position * div_term)  # 偶数维
    pe[:, 1::2] = torch.cos(position * div_term)  # 奇数维
    
    return pe
```

**优点：**
- 可以外推到训练时没见过的长度
- 有明确的相对位置关系：`PE(pos+k)` 可以用 `PE(pos)` 线性表示

#### 3. 现代大模型：Learnable Positional Embedding / RoPE / ALiBi

| 方法 | 代表模型 | 核心思想 |
|------|---------|---------|
| **Learnable PE** | GPT-2/3, BERT | 把位置当词嵌入一样学 |
| **RoPE (Rotary)** | LLaMA, Mistral, Qwen | 旋转位置编码，通过旋转矩阵注入相对位置 |
| **ALiBi** | BLOOM | 在 attention score 上加线性偏置，外推能力强 |
| **No PE** | 某些探索性工作 | 用因果 mask 隐式编码位置（效果有限） |

**RoPE 核心思想：**
- 不直接加位置信息，而是将 Q、K 在二维平面上旋转一定角度
- 旋转角度与位置成正比 → 自然编码相对位置
- 外推性好，是现在主流方案

---

### 四、Transformer 整体架构

#### 1. Encoder-Decoder 架构（原始 Transformer）

```
Input Embedding + Positional Encoding
    ↓
[Encoder Block] × N
    ├── Multi-Head Self-Attention
    ├── Add & Norm (Residual + LayerNorm)
    ├── Feed Forward (FFN)
    └── Add & Norm
    ↓
[Decoder Block] × N
    ├── Masked Multi-Head Self-Attention
    ├── Add & Norm
    ├── Cross-Attention (Q from decoder, K/V from encoder)
    ├── Add & Norm
    ├── Feed Forward
    └── Add & Norm
    ↓
Linear + Softmax → Output Probabilities
```

#### 2. 只含 Decoder 的架构（GPT 系列）

```
Input Embedding + Positional Encoding
    ↓
[Decoder Block] × N
    ├── Masked Multi-Head Self-Attention
    ├── Add & Norm
    ├── Feed Forward
    └── Add & Norm
    ↓
Linear → Next Token Prediction
```

**特点：**
- 自回归生成，只能看到前面的 token
- 预训练任务：Next Token Prediction
- 代表：GPT-1/2/3/4, LLaMA, Claude

#### 3. 只含 Encoder 的架构（BERT）

```
Input Embedding + Positional Encoding
    ↓
[Encoder Block] × N
    ├── Multi-Head Self-Attention（双向，无 mask）
    ├── Add & Norm
    ├── Feed Forward
    └── Add & Norm
    ↓
[CLS] token for classification / [MASK] for MLM
```

**特点：**
- 双向注意力，可以看到全部上下文
- 预训练任务：MLM（Masked Language Model）+ NSP
- 适合：理解任务（分类、NER、相似度）

#### 4. Encoder-Decoder（T5、BART）

- T5：把所有 NLP 任务统一为 text-to-text
- 适合：翻译、摘要、问答等生成任务

---

### 五、GPT vs BERT vs T5 对比

| 特性 | GPT (Decoder-only) | BERT (Encoder-only) | T5 (Encoder-Decoder) |
|------|-------------------|---------------------|---------------------|
| **注意力方向** | 单向（因果 mask） | 双向 | Encoder双向 + Decoder单向 |
| **预训练任务** | Next Token Prediction | MLM + NSP | Span Corruption |
| **典型应用** | 文本生成、对话、代码 | 文本理解、分类、NER | 翻译、摘要、问答 |
| **代表模型** | GPT-4, LLaMA, Claude | BERT, RoBERTa | T5, BART, UL2 |
| **训练数据** | 海量无标注文本 | 维基百科 + BookCorpus | C4 (Colossal Clean Crawled Corpus) |

---

### 六、Feed-Forward Network (FFN)

```python
class FFN(nn.Module):
    def __init__(self, d_model=512, d_ff=2048):
        super().__init__()
        self.linear1 = nn.Linear(d_model, d_ff)
        self.linear2 = nn.Linear(d_ff, d_model)
    
    def forward(self, x):
        # 原始 Transformer: ReLU
        # 现代: GELU ( smoother，训练更稳定 )
        return self.linear2(F.gelu(self.linear1(x)))
```

**为什么 d_ff = 4 × d_model？**
- 经验值，提供足够的非线性表达能力
- 这是 Transformer 参数量的主要来源（约占 2/3）

---

### 七、Layer Normalization 与 Residual Connection

```python
# Pre-LN（现代主流，LLaMA/GPT-3 等使用）
x = x + Attention(LayerNorm(x))
x = x + FFN(LayerNorm(x))

# Post-LN（原始 Transformer）
x = LayerNorm(x + Attention(x))
x = LayerNorm(x + FFN(x))
```

**为什么 Pre-LN 更好？**
- 梯度传播更直接，训练更稳定
- 可以使用更大的学习率
- 深层模型（>24 层）时优势明显

---

### 八、KV Cache（推理优化核心）

#### 1. 问题背景

Decoder 推理时，每次只生成一个新 token，但如果重新计算整个序列的 Attention，复杂度是 O(n²)。

#### 2. KV Cache 原理

```python
# 推理时，缓存之前所有 token 的 K 和 V
past_k = []  # 缓存历史 K
past_v = []  # 缓存历史 V

for i in range(max_new_tokens):
    # 只计算当前 token 的 Q
    q = W_Q(current_token)
    
    # K, V 只需要计算当前 token 的，然后拼接到缓存
    k = torch.cat([past_k, W_K(current_token)], dim=1)
    v = torch.cat([past_v, W_V(current_token)], dim=1)
    
    # Attention 计算
    scores = q @ k.transpose(-2, -1) / sqrt(d_k)
    weights = softmax(scores)
    output = weights @ v
    
    # 更新缓存
    past_k, past_v = k, v
```

**复杂度优化：**
- 无缓存：O(n²) 每步，总 O(n³)
- 有缓存：O(n) 每步，总 O(n²)

**代价：**
- 显存占用：2 × num_layers × num_heads × seq_len × d_head × batch_size × sizeof(float)
- 例如 LLaMA-7B，batch=1，seq_len=4096：约 2 × 32 × 32 × 4096 × 128 × 2 ≈ 2GB

---

### 九、FlashAttention（显存优化）

#### 1. 问题

标准 Attention 需要存储巨大的 (seq_len, seq_len) 注意力矩阵，显存占用随序列长度平方增长。

#### 2. FlashAttention 核心思想

> 💡 **分块计算 + 在线 softmax + 重计算（recomputation），避免实例化完整的注意力矩阵。**

**关键观察：**
- softmax 可以增量计算
- 不需要同时保存完整的 Q@K^T 矩阵

```python
# 分块处理
for block_q in Q.chunks(block_size):
    # 逐个加载 K, V 块，增量计算 softmax
    for block_k, block_v in zip(K.chunks(block_size), V.chunks(block_size)):
        scores = block_q @ block_k.T
        # 在线更新 softmax 统计量
        # ...
    # 输出当前 Q 块的结果
```

**效果：**
- 显存：O(n²) → O(n)
- 速度：由于更好的内存访问模式（减少 HBM 读写），实际更快
- 精度：完全等价，无近似

---

### 十、Scaling Laws（扩展法则）

OpenAI 提出的经验规律：

```
Loss ∝ (N^(-α)) , (D^(-β))

其中：
N = 模型参数量
D = 训练 token 数
α ≈ 0.076, β ≈ 0.095
```

**核心结论：**
1. **模型越大，效果越好**（在数据充足的前提下）
2. **数据量和模型量要匹配**：Chinchilla 论文提出最优比例 `D ≈ 20 × N`（20 tokens/parameter）
3. **计算量固定时，模型大小和数据量需要权衡**

| 模型 | 参数量 | 训练 token 数 | 比例 (tokens/param) |
|------|--------|--------------|---------------------|
| GPT-3 | 175B | 300B | ~1.7 |
| Chinchilla | 70B | 1.4T | ~20 |
| LLaMA-2 | 70B | 2T | ~28 |
| LLaMA-3 | 70B | 15T | ~214 |

> 趋势：数据量增长快于参数量，「数据为王」

---

### 十一、高频面试连环问

#### Q1: Self-Attention 的时间/空间复杂度？

> **答：** 时间复杂度 O(n² × d)，空间复杂度 O(n²)。n 是序列长度，d 是维度。主要瓶颈在 Q@K^T 的 (n, n) 矩阵。长序列是 Transformer 的痛点。

#### Q2: Transformer 为什么比 RNN 快？

> **答：** 
> 1. **并行性**：RNN 必须顺序计算，Transformer 可以并行处理整个序列
> 2. **长距离依赖**：RNN 需要一步步传递，长序列梯度消失；Attention 直接连接任意两个位置，路径长度 O(1)
> 3. **硬件友好**：矩阵运算高度优化（CUDA/cuBLAS）

#### Q3: 为什么用 LayerNorm 不用 BatchNorm？

> **答：**
> - BatchNorm 对一个 batch 的同一特征做归一化，对序列数据不友好（序列长度变化、batch size 小）
> - LayerNorm 对单个样本的所有特征做归一化，更适合变长序列
> - 在 NLP 中，同一位置的 token 在不同样本中语义不同，batch 统计不稳定

#### Q4: Transformer 的参数量怎么算？

> **答：**
> - Embedding: Vocab_size × d_model
> - Attention: 4 × d_model² (Q/K/V/O 四个矩阵)
> - FFN: 2 × d_model × d_ff (通常 d_ff = 4 × d_model)
> - LayerNorm: 2 × d_model (γ, β)
> - 每层：4d² + 8d² = 12d²（当 d_ff=4d）
> - 总参数量 ≈ Vocab_size × d + N_layers × 12d²

**计算示例（GPT-3 175B）：**
- Vocab = 50257, d = 12288, layers = 96
- Embedding: 50257 × 12288 ≈ 0.6B
- Layers: 96 × 12 × 12288² ≈ 174B
- 总计 ≈ 175B ✅

#### Q5: 什么是梯度检查点（Gradient Checkpointing）？

> **答：** 用计算换显存。前向传播时只保存部分层的激活值，反向传播时重新计算丢弃的激活。可以训练更大的模型，但速度降低约 20-30%。

#### Q6: 多头注意力中，head 的数量怎么选？

> **答：** 经验值，通常 d_model / num_heads = 64 或 128。比如 d=512 用 8 头，d=1024 用 16 头，d=4096 用 32 头。关键是让每个 head 的维度足够表达一种「关系类型」。

#### Q7: Decoder-only 模型为什么能成为主流？

> **答：**
> 1. **统一框架**：一个模型同时做理解和生成（通过 prompt engineering）
> 2. **Scaling 友好**：结构简单，容易做大
> 3. **工程简单**：不需要 encoder-decoder 对齐
> 4. **涌现能力**：足够大后出现上下文学习、推理等能力

---

### 十二、快速记忆口诀

```
Attention = Q找K，加权求V
Multi-Head = 多视角看同一序列
Mask =  Decoder不能偷看未来
Positional Encoding = 给模型方向感
Pre-LN = 训练更深的网络
KV Cache = 推理加速必备
FlashAttention = 省显存的神器
Scaling Law = 大力出奇迹，但数据要跟上
```

---

## 📚 扩展阅读

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762) — Transformer 原论文
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) — 可视化理解
- [FlashAttention](https://arxiv.org/abs/2205.14135) — 显存优化
- [RoPE](https://arxiv.org/abs/2104.09864) — 旋转位置编码
- [Chinchilla](https://arxiv.org/abs/2203.15556) — 训练最优比例

---

> 🎯 **今日要点回顾：**
> 1. LIS 双解法：DP O(n²) 理解思路，贪心+二分 O(n log n) 是面试答案
> 2. Self-Attention = Q@K^T 缩放 softmax 后乘 V
> 3. Multi-Head = 多组 Q/K/V 并行，最后拼接投影
> 4. 位置编码：从 Sinusoidal 到 RoPE 的演进
> 5. GPT(Decoder) vs BERT(Encoder) vs T5(Encoder-Decoder)
> 6. KV Cache 让推理从 O(n³) 降到 O(n²)
> 7. Scaling Law：参数量、数据量、计算量的权衡

---

*Created by Kimi Claw | 第 90 天打卡 ✅*
