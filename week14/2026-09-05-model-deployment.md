# Day 93 — 正则表达式匹配 + 模型部署与推理优化

> 📅 2026-09-05 | Week 14 Day 7（收官日）| 主题：人工智能与大模型工程 🧠

---

## 1. 今日算法题：正则表达式匹配

### 题目（LeetCode 10. Regular Expression Matching）

给你一个字符串 `s` 和一个字符规律 `p`，请你来实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配。

- `'.'` 匹配任意单个字符
- `'*'` 匹配零个或多个前面的那一个元素

所谓匹配，是要涵盖整个字符串 `s` 的，而不是部分字符串。

**示例 1：**
```
输入：s = "aa", p = "a"
输出：false
解释："a" 无法匹配 "aa" 整个字符串。
```

**示例 2：**
```
输入：s = "aa", p = "a*"
输出：true
解释：因为 '*' 代表零个或多个前面的元素，在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```

**示例 3：**
```
输入：s = "ab", p = ".*"
输出：true
解释：".*" 表示可匹配零个或多个任意字符。
```

**约束：**
- `1 <= s.length <= 20`
- `1 <= p.length <= 30`
- `s` 只包含小写英文字母
- `p` 只包含小写英文字母、`'.'` 和 `'*'`
- 保证每次出现字符 `'*'` 时，前面都匹配到有效的字符

---

### 解题思路

这道题是**二维动态规划**的经典代表，面试出现频率极高，而且很容易因为状态转移想不清楚而翻车。

#### 核心思想

定义 `dp[i][j]` 表示 `s[0..i-1]` 和 `p[0..j-1]` 是否匹配。

状态转移需要分情况讨论 `p[j-1]` 是什么：

**情况 1：`p[j-1]` 是普通字符或小点 `.`**

直接匹配：
```
dp[i][j] = dp[i-1][j-1] 且 (s[i-1] == p[j-1] 或 p[j-1] == '.')
```

**情况 2：`p[j-1]` 是 `*`**

`*` 前面有个字符 `p[j-2]`，这个 `*` 可以：
- **匹配 0 次**：跳过 `p[j-2]*` → `dp[i][j] = dp[i][j-2]`
- **匹配 1 次或多次**：前提是 `s[i-1]` 能和 `p[j-2]` 匹配上 → `dp[i][j] = dp[i-1][j]`

```
dp[i][j] = dp[i][j-2]  // * 匹配 0 次
        || (dp[i-1][j] 且 (s[i-1] == p[j-2] 或 p[j-2] == '.'))  // * 匹配 >=1 次
```

#### 为什么 `dp[i-1][j]` 而不是 `dp[i-1][j-2]`？

这是最容易想错的地方！

`*` 匹配多次时，我们让 `*` 多吞一个字符（即 `s[i-1]`），然后看 `s[0..i-2]` 和同样的 `p[0..j-1]` 是否匹配。因为 `*` 可以继续匹配更多次，所以要保留 `*` 在状态中。

画个图：
```
s = "aaa", p = "a*"

匹配过程：
  a* 吃 0 个 a → 不匹配（还剩 "aaa"）
  a* 吃 1 个 a → 看 "aa" 和 "a*" 是否匹配
  a* 吃 2 个 a → 看 "a" 和 "a*" 是否匹配
  a* 吃 3 个 a → 看 "" 和 "a*" 是否匹配 ✓
```

#### 边界条件

- `dp[0][0] = true`：空串匹配空模式
- `dp[0][j]`：当 `p[j-1]` 是 `*` 时，`dp[0][j] = dp[0][j-2]`（`*` 消掉前面的字符）

---

### 代码实现

#### Python

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)
        # dp[i][j] = s[0..i-1] 和 p[0..j-1] 是否匹配
        dp = [[False] * (n + 1) for _ in range(m + 1)]
        
        # 边界：空串匹配空模式
        dp[0][0] = True
        
        # 边界：s 为空，p 为 a*b*c* 这种模式
        for j in range(2, n + 1):
            if p[j - 1] == '*':
                dp[0][j] = dp[0][j - 2]
        
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if p[j - 1] == '*':
                    # * 匹配 0 次：跳过 p[j-2]*
                    dp[i][j] = dp[i][j - 2]
                    # * 匹配 >=1 次：s[i-1] 和 p[j-2] 匹配，且前面也匹配
                    if p[j - 2] == '.' or p[j - 2] == s[i - 1]:
                        dp[i][j] = dp[i][j] or dp[i - 1][j]
                else:
                    # 普通字符或 .，直接匹配
                    if p[j - 1] == '.' or p[j - 1] == s[i - 1]:
                        dp[i][j] = dp[i - 1][j - 1]
        
        return dp[m][n]
```

#### Go

```go
func isMatch(s string, p string) bool {
    m, n := len(s), len(p)
    dp := make([][]bool, m+1)
    for i := range dp {
        dp[i] = make([]bool, n+1)
    }
    
    dp[0][0] = true
    
    // s 为空串时
    for j := 2; j <= n; j++ {
        if p[j-1] == '*' {
            dp[0][j] = dp[0][j-2]
        }
    }
    
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if p[j-1] == '*' {
                // 匹配 0 次
                dp[i][j] = dp[i][j-2]
                // 匹配 >=1 次
                if p[j-2] == '.' || p[j-2] == s[i-1] {
                    dp[i][j] = dp[i][j] || dp[i-1][j]
                }
            } else {
                if p[j-1] == '.' || p[j-1] == s[i-1] {
                    dp[i][j] = dp[i-1][j-1]
                }
            }
        }
    }
    
    return dp[m][n]
}
```

#### C++

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int m = s.size(), n = p.size();
        vector<vector<bool>> dp(m + 1, vector<bool>(n + 1, false));
        
        dp[0][0] = true;
        
        for (int j = 2; j <= n; j++) {
            if (p[j - 1] == '*') {
                dp[0][j] = dp[0][j - 2];
            }
        }
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (p[j - 1] == '*') {
                    dp[i][j] = dp[i][j - 2];
                    if (p[j - 2] == '.' || p[j - 2] == s[i - 1]) {
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                    }
                } else {
                    if (p[j - 1] == '.' || p[j - 1] == s[i - 1]) {
                        dp[i][j] = dp[i - 1][j - 1];
                    }
                }
            }
        }
        
        return dp[m][n];
    }
};
```

---

### 复杂度分析

| 维度 | 复杂度 | 说明 |
|---|---|---|
| **时间** | O(m × n) | m = len(s), n = len(p)，双重循环填表 |
| **空间** | O(m × n) | 二维 DP 数组 |

**空间优化：** 可以压缩到 O(n)，因为 `dp[i]` 只依赖 `dp[i-1]` 和当前行。但实际面试中二维写法更不容易出错，建议先写对的再谈优化。

---

### 🎤 面试官连环追问

> **Q：如果字符串长度可以到 10⁵，还能用 DP 吗？**
>
> 不行，O(mn) 会超时。需要换成贪心或双指针的变体。但本题有 `*`，贪心很难处理，可能需要结合递归下降解析器的思想，或者用 NFA（非确定有限自动机）状态转换来做，复杂度取决于状态压缩的效果。

> **Q：`*` 的状态转移为什么用 `dp[i-1][j]` 而不是 `dp[i-1][j-2]`？**
>
> 因为 `*` 可以匹配多次。如果用 `dp[i-1][j-2]`，表示 `*` 只匹配一次，然后把它消掉。但 `*` 可能还要匹配更多次，所以要保留 `*` 继续匹配的能力。`dp[i-1][j]` 的含义是：让 `*` 多吞一个字符，然后继续用同样的模式去匹配剩余部分。

> **Q：你能把空间优化到 O(n) 吗？**
>
> 可以，只用一维数组。因为 `dp[i][j]` 只依赖：
> - `dp[i][j-2]`（左边两格）
> - `dp[i-1][j]`（上方）
> - `dp[i-1][j-1]`（左上）
>
> 从左到右扫描时，`dp[i-1][j]` 和 `dp[i-1][j-1]` 是上一行的值，需要额外保存。实现稍微麻烦一点，但原理就是滚动数组。

> **Q：如果再加一个 `+`（匹配 1 次或多次），状态转移怎么改？**
>
> `+` 等价于 `xx*`，可以直接转换。如果要原生支持，`+` 的转移和 `*` 类似，但不能匹配 0 次，所以少了 `dp[i][j-2]` 那条分支。

---

## 2. 面试技巧：模型部署与推理优化

> 🔥 **Week 14 收官日** — 这是 AI 工程面试中最实操、最容易被问到"你做过什么"的部分。理论讲了那么多天，今天聚焦**怎么把模型搬上线、怎么让它跑得又快又省**。

---

### 2.1 模型压缩技术（三驾马车）

#### 量化（Quantization）

把 FP32/FP16 权重压缩到 INT8/INT4，甚至二值化。

| 方法 | 原理 | 精度损失 | 加速比 | 适用场景 |
|---|---|---|---|---|
| **PTQ 训练后量化** | 直接对训练好的模型做统计校准（如 min/max、KL 散度） | 低（1-2%） | 2-4x | 快速部署，首选方案 |
| **QAT 量化感知训练** | 训练时模拟低精度，让模型适应量化误差 | 极低 | 2-4x | 精度敏感场景 |
| **GPTQ / AWQ** | 逐层优化，最小化量化误差 | 中低 | 2-4x | LLM 专用，4bit 主流 |
| **SmoothQuant** | 把激活值的量化难度迁移到权重上 | 低 | 2x | LLM 推理，解决激活 outlier 问题 |

**面试金句：**
> "量化不是简单的四舍五入。PTQ 用 calibration 找最优 scale，但 LLM 的激活值有 outlier，所以有了 SmoothQuant 把难度从激活移到权重。如果精度还不够，就上 QAT，在训练时模拟低精度前向传播。"

**高频追问：**
- **Q：为什么 LLM 量化比 CV 模型难？**
  - 激活值分布有极端 outlier（某些 channel 的值特别大），直接量化会压垮大部分信息。
  - 解决方案：SmoothQuant（迁移难度）、LLM.int8()（混合精度，outlier 保持 FP16）。
- **Q：INT4 比 INT8 慢是怎么回事？**
  - 解压开销。INT4 需要解包到 INT8 才能做矩阵乘，如果硬件不支持原生 INT4 计算，反而更慢。

---

#### 剪枝（Pruning）

去掉不重要的权重或整个神经元/通道。

| 类型 | 粒度 | 效果 | 难度 |
|---|---|---|---|
| **非结构化剪枝** | 单个权重 | 压缩率高（90%+），但硬件不友好 | 需专用硬件/编译器支持 |
| **结构化剪枝** | 整行/整列/通道 | 直接减少矩阵维度，硬件友好 | 压缩率较低（30-50%） |
| **半结构化（2:4 稀疏）** | NVIDIA Ampere 支持 | 2:4 模式有硬件加速 | 需要重训练恢复精度 |

**面试金句：**
> "非结构化剪枝的压缩率很高，但稀疏矩阵乘在 GPU 上并不快，除非用 cuSPARSE 或专用编译器。结构化剪枝虽然压缩率低，但能直接减少 FLOPs，工程上更实用。NVIDIA Ampere 的 2:4 稀疏是折中方案。"

---

#### 知识蒸馏（Knowledge Distillation）

让小模型（Student）学大模型（Teacher）的输出分布。

```
Teacher (大模型) → softmax with temperature → soft labels
                          ↓
Student (小模型) → 同时拟合 hard label + soft label
```

- **Soft Target**：Temperature > 1 时，概率分布更平滑，包含更多类别间关系信息
- **Hard Target**：正常的 one-hot 标签
- **Loss**：α × CE(hard) + (1-α) × KL(soft_student || soft_teacher)

**进阶变体：**
- **MiniLLM / GKD**：让学生反向 KL 更好学习
- **Speculative Decoding**：用小模型做 draft，大模型做 verify（后面详说）

**面试金句：**
> "蒸馏不只是让小模型拟合答案，而是让它学习大模型的'思考方式'——soft target 包含了类别之间的相似性关系，比如'猫'和'老虎'的概率关联，这是 hard label 没有的。"

---

### 2.2 推理加速技术

#### KV Cache — 大模型推理的命根子

**为什么需要 KV Cache？**

Transformer 自回归生成时，每一步都要计算前面所有 token 的 Attention。但前面 token 的 Key 和 Value 是不变的，可以缓存下来。

- **无 Cache**：每步 O(n²) 的 Attention 计算，n 为序列长度
- **有 Cache**：每步只需计算当前 token 的 Q，和缓存的 K/V 做 Attention → O(n)

**显存占用估算：**
```
KV Cache 显存 = 2 × num_layers × num_heads × head_dim × seq_len × batch_size × sizeof(dtype)

以 LLaMA-7B 为例：
- 32 层，32 头，128 维，batch=1，FP16，seq_len=4096
- 2 × 32 × 4096 × 4096 × 2 bytes ≈ 2 GB
- batch=32 时 ≈ 64 GB → 这就是 A100 40GB 爆显存的原因
```

**面试金句：**
> "KV Cache 让 LLM 推理从 O(n³) 降到 O(n²)，但显存是瓶颈。batch 大了显存线性增长，所以有了 PagedAttention 和 Continuous Batching。"

---

#### PagedAttention（vLLM 核心）

**问题：** 传统 KV Cache 是预分配连续显存，但不同请求的序列长度不同，产生大量内存碎片和浪费。

**解决：** 把 KV Cache 分页管理，像 OS 虚拟内存一样。

```
传统：每个请求预分配 max_seq_len 的连续显存 → 内部碎片严重
PagedAttention：把 KV Cache 切成固定大小的 blocks，按需分配 → 几乎无碎片
```

**效果：**
- 吞吐量提升 2-4x（同样的 GPU 能服务更多并发）
- 支持更大的 batch size

**面试金句：**
> "vLLM 的 PagedAttention 灵感来自 OS 的虚拟内存分页。传统 KV Cache 是连续分配的，每个请求都按最大长度预分配，长度不齐就浪费。PagedAttention 把 KV 切成 blocks，按需分配，碎片率接近零，吞吐量直接翻倍。"

---

#### Continuous Batching（In-flight Batching）

**问题：** 静态 batching 要等 batch 里所有请求都完成才释放，短请求等长请求，GPU 空转。

**解决：** 动态地把新请求塞进正在运行的 batch，完成的请求随时退出。

```
静态 Batch：[
  [=================]  长请求
  [====]              短请求 ← 完成了也得等长的
  [==========]        中请求
] 一起开始，一起结束

Continuous：
  t0: [req1====][req2==][req3========]
  t1: [req1========][req3========][req4==]  ← req2 完成退出，req4 动态加入
  t2: [req1=============][req3========][req4====][req5=]
```

**面试金句：**
> "静态 batching 的 GPU 利用率取决于 batch 里最长的那个请求，短的完成也得干等。Continuous batching 让 GPU 一直满负荷运转，吞吐量提升 10-20 倍。"

---

#### Speculative Decoding（投机采样）

**核心思想：** 用小而快的模型（draft model）预测多个 token，大模型一次性验证。

```
1. Draft Model（比如 1B）快速生成 5 个 token：[the, cat, sat, on, the]
2. Target Model（比如 70B）并行验证这 5 个 token
3. 从第一个不匹配的位置截断，保留匹配的，重新采样
4. 重复

效果：如果 draft 的接受率 80%，理论加速比 ≈ 1/(1-0.8) = 5x
```

**面试金句：**
> "大模型生成一个 token 和小模型生成一个 token，时间差可能是 100 倍。Speculative Decoding 让小模型'蒙'几个，大模型批量验证，匹配的就白嫖，不匹配的重来。只要 draft 质量不太差，整体能快 2-3 倍。"

---

#### FlashAttention / FlashDecoding

- **FlashAttention**：分块计算 Attention，减少 HBM 访问次数，显存从 O(N²) → O(N)
- **FlashDecoding**：FlashAttention 的推理优化版，支持长序列的并行解码

**面试金句：**
> "Attention 的瓶颈不在计算量，而在显存带宽。FlashAttention 用 tiling 分块，把中间结果留在 SRAM 里，大幅减少 HBM 读写。这不是近似算法，是精确计算，只是重排了计算顺序。"

---

### 2.3 部署架构与工程实践

#### 模型服务框架选型

| 框架 | 特点 | 适用场景 |
|---|---|---|
| **vLLM** | PagedAttention + Continuous Batching，吞吐量最高 | LLM 在线服务，首选 |
| **TGI (HuggingFace)** | 生态好，支持多模态 | 快速原型，HF 生态 |
| **TensorRT-LLM** | NVIDIA 官方，极致优化 | 生产环境，NVIDIA GPU |
| **llama.cpp** | C++ 实现，支持 CPU + 多种量化 | 边缘设备、本地部署 |
| **ONNX Runtime** | 跨平台，模型格式标准化 | 传统 ML 模型 |
| **Triton Inference Server** | NVIDIA 企业级，多模型并发 | 大规模生产环境 |

---

#### 部署架构模式

**1. 单体部署（Single Instance）**
```
Client → [Load Balancer] → [vLLM Instance] → GPU
```
- 简单，适合起步
- 缺点：无高可用，升级要停机

**2. 多副本 + 负载均衡**
```
Client → [LB] → [vLLM-1] [vLLM-2] [vLLM-3]
                   ↓          ↓          ↓
                 GPU-1      GPU-2      GPU-3
```
- 轮询或按并发度分发
- 注意：LLM 是有状态的（KV Cache），请求要路由到同一个实例 → 用 session affinity

**3. 分离式架构（Prefill-Decode Disaggregated）**
```
Prefill Phase (计算密集)  →  [Prefill Cluster]  → 大 batch，高算力
Decode Phase (内存带宽密集) → [Decode Cluster]  → 低延迟，高并发
```
- Prefill：第一次 forward，计算量大，需要算力
- Decode：自回归生成，受限于显存带宽，需要高吞吐
- 分离后可以分别优化，整体效率更高

**面试金句：**
> "LLM 推理有两个阶段：Prefill（第一次计算，算力密集）和 Decode（自回归生成，带宽密集）。把它们拆到不同的集群，Prefill 用大 batch 吃满算力，Decode 用高并发吃满带宽，整体效率比混部高 30% 以上。"

---

#### 多机多卡部署（Tensor Parallel + Pipeline Parallel）

| 并行方式 | 切分维度 | 通信量 | 适用 |
|---|---|---|---|
| **Tensor Parallel（TP）** | 层内切分（如 attention 头切到不同卡） | 大（每步 all-reduce） | 单节点多卡 |
| **Pipeline Parallel（PP）** | 层间切分（不同层放不同卡） | 小（仅相邻卡通信） | 跨节点 |
| **Tensor + Pipeline** | 2D 并行 | 中等 | 大模型标准方案 |

**面试金句：**
> "单节点用 Tensor Parallel，因为 NVLink 带宽高，all-reduce 开销小。跨节点用 Pipeline Parallel，通信只在 stage 边界，对网络带宽要求低。70B 模型在 8×A100 上，TP=8 放一台机器；405B 就得 TP+PP 一起上。"

---

### 2.4 边缘与移动端部署

| 技术 | 说明 |
|---|---|
| **MobileLLM / EdgeLLM** | 针对 ARM CPU 优化的架构（如 DeepSeek 的 MLA） |
| **CoreML / NNAPI** | 移动端推理框架 |
| **GGML / GGUF** | llama.cpp 的格式，支持多种量化，CPU 也能跑 |
| **Neural Engine** | Apple 的 NPU，专用加速 |

**面试金句：**
> "端侧部署不是简单地把 70B 模型塞手机里。要么用 1-3B 的小模型 + 蒸馏，要么用 MoE 架构只激活部分专家。GGUF 格式 + llama.cpp 让 MacBook 也能跑 7B 模型，但延迟和精度是 trade-off。"

---

### 2.5 高频面试连环问

> **Q：量化后精度下降了，怎么排查？**
>
> 1. 先看哪层量化误差最大（逐层对比 FP16 和 INT8 输出）
> 2. 激活值 outlier → 上 SmoothQuant 或 LLM.int8()
> 3. 权重分布不均匀 → 用 GPTQ/AWQ 做逐层优化
> 4. 实在不行 → QAT 重新训练

> **Q：vLLM 的 PagedAttention 和传统缓存管理有什么区别？**
>
> 传统是预分配连续显存，每个请求按最大长度分配，产生内部碎片。PagedAttention 把 KV 切成固定大小的 blocks，用页表管理，按需分配、回收、共享（copy-on-write），碎片率接近零，同时支持高效的内存共享（比如 parallel sampling 时共享 prompt 的 KV）。

> **Q：Continuous Batching 怎么实现？**
>
> 核心是：batch 不是固定的，新请求随时加入，完成的随时退出。实现上需要：
> 1. 调度器维护一个等待队列
> 2. 每次 forward 前，看有没有空间加入新请求
> 3. 有请求完成时，立即把等待队列的补进来
> 4. Attention 计算要支持变长序列（用 mask 或 padding-free）

> **Q：Speculative Decoding 的 draft model 怎么选？**
>
> 一般是原模型的 1/10 到 1/100 大小。可以是：
> - 同系列的小版本（如 LLaMA-7B draft + LLaMA-70B target）
> - 蒸馏得到的小模型
> - 甚至用 n-gram 做 draft（Medusa / Lookahead Decoding）
>
> 关键指标是接受率，一般 60-80% 就有明显加速。

> **Q：模型部署时怎么监控？**
>
> - **延迟**：TTFT（Time To First Token）、TPOT（Time Per Output Token）、端到端延迟
> - **吞吐**：tokens/sec、requests/sec
> - **资源**：GPU 利用率、显存占用、显存带宽利用率
> - **质量**：perplexity、下游任务指标（防量化/剪枝导致的质量下降）
> - **稳定性**：错误率、超时率、KV Cache 命中率

> **Q：如果 GPU 显存不够，有哪些方案？**
>
> 1. 量化（INT8/INT4）→ 权重和 KV Cache 都变小
> 2. 减少 batch size / 序列长度
> 3. ZeRO-Inference / Tensor Parallel 分片到多卡
> 4. CPU offloading（DeepSpeed ZeRO-Offload）
> 5. 分页/压缩 KV Cache（H2O、Scissorhands 等动态 eviction）

---

### 2.6 一句话速记卡

| 概念 | 一句话 |
|---|---|
| **PTQ** | 训好后直接量化，快但精度可能掉 |
| **QAT** | 训练时模拟低精度，精度高但成本高 |
| **SmoothQuant** | 把激活的 outlier 难度迁移到权重 |
| **KV Cache** | 缓存前面 token 的 K/V，避免重复计算 |
| **PagedAttention** | 分页管理 KV Cache，消除碎片 |
| **Continuous Batching** | 动态增减请求，GPU 一直满负荷 |
| **Speculative Decoding** | 小模型 draft，大模型 verify |
| **TP vs PP** | 层内切分 vs 层间切分 |
| **Prefill vs Decode** | 第一次计算 vs 自回归生成 |
| **TTFT / TPOT** | 首 token 延迟 / 每 token 延迟 |

---

## 3. Week 14 总结

🎉 **Week 14 — 人工智能与大模型工程 完结！**

这 7 天我们覆盖了 AI 工程面试的完整知识栈：

| 天数 | 主题 | 核心收获 |
|---|---|---|
| Day 87 | 机器学习基础与特征工程 | 特征编码、评估指标、过拟合六招 |
| Day 88 | 深度学习核心原理 | 激活函数选型、反向传播、优化器进化 |
| Day 89 | 分布式训练 | DDP/ZeRO/3D 并行、混合精度、OOM 排查 |
| Day 90 | Transformer 与大模型架构 | Attention QKV、KV Cache、FlashAttention |
| Day 91 | RAG 与向量检索 | ANN 算法、向量数据库、文档切分策略 |
| Day 92 | Agent 系统设计与工具调用 | ReAct、Function Calling、多 Agent 协作 |
| **Day 93** | **模型部署与推理优化** | **量化/剪枝/蒸馏、PagedAttention、Continuous Batching、Speculative Decoding** |

---

### 🚀 下一步？

Week 15 主题即将揭晓 —— 可能是**大模型应用开发实战**、**多模态技术栈**，或者**面试模拟与真题复盘**。拭目以待！

> "训练是科学，部署是艺术。一个 70B 模型在纸上的指标再漂亮，TTFT 5 秒、TPOT 200ms，用户也不会用。推理优化是让 AI 从实验室走进产品的最后一公里。"
