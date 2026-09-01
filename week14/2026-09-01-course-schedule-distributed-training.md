# Day 89 — 课程表 II + 模型训练与分布式训练

> 📅 2026-09-01 | Week 14 Day 3 | 主题：人工智能与大模型工程 🧠

---

## 今日算法题：课程表 II

### 题目（LeetCode 210）

现在你总共有 `numCourses` 门课需要选，记为 `0` 到 `numCourses - 1`。给你一个数组 `prerequisites`，其中 `prerequisites[i] = [a, b]` 表示如果要学习课程 `a` 则**必须**先学习课程 `b`。

- 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0`，你需要先完成课程 `1`。

请你返回你为了完成所有课程所安排的学习顺序。可能会有多个正确的顺序，你只要返回**任意一种**就可以了。如果不可能完成所有课程，返回**空数组**。

**示例 1：**
```
输入：numCourses = 2, prerequisites = [[1,0]]
输出：[0,1]
解释：总共有 2 门课程。要学习课程 1，你需要先完成课程 0。因此，正确的课程顺序为 [0,1] 。
```

**示例 2：**
```
输入：numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
输出：[0,1,2,3] 或 [0,2,1,3]
解释：课程 0 没有任何先修课，可以直接学；课程 1 和 2 都依赖课程 0；课程 3 依赖课程 1 和 2。
      所以 [0,1,2,3] 和 [0,2,1,3] 都是合法的。
```

**示例 3：**
```
输入：numCourses = 2, prerequisites = [[1,0],[0,1]]
输出：[]
解释：课程 0 依赖课程 1，课程 1 又依赖课程 0，形成环，无法完成。
```

**约束：**
- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= numCourses * (numCourses - 1)`
- `prerequisites[i].length == 2`
- `0 <= a, b < numCourses`
- `a != b`
- 所有 `[a, b]` **互不相同**

---

### 思路：拓扑排序（Kahn 算法 / DFS）

这道题是**课程表 I（LeetCode 207）的升级版**，不只要判断能否完成，还要输出一个合法的完成顺序。

核心思想：**拓扑排序（Topological Sort）**

> 拓扑排序是对**有向无环图（DAG）**的节点进行线性排序，使得对于图中的每条有向边 `u → v`，节点 `u` 在排序中都位于节点 `v` 之前。

**类比分布式训练中的计算图：**
- 每个课程 = 计算图中的一个算子（operation）
- 先修依赖 = 数据依赖关系（某个算子的输入是另一个算子的输出）
- 拓扑排序结果 = 合法的执行顺序，确保依赖满足后再执行
- 环检测 = 检测计算图中是否存在循环依赖（这种图无法执行）

在深度学习框架（PyTorch/TensorFlow）中，**反向传播的计算图本质上就是一个 DAG**，框架内部正是用类似拓扑排序的方式来确定梯度计算的先后顺序。

---

#### 方法一：Kahn 算法（BFS，推荐）

**核心思想**： repeatedly 找入度为 0 的节点，加入结果，并移除它出发的所有边。

1. **建图**：构建邻接表，同时统计每个节点的**入度**（in-degree）
2. **初始化**：将所有入度为 0 的节点加入队列（这些节点没有前置依赖，可以先学）
3. **BFS**：
   - 取出队列中的节点，加入结果
   - 遍历该节点的所有邻居，将邻居的入度减 1
   - 如果邻居入度变为 0，加入队列
4. **判断**：如果结果中的节点数等于总课程数，说明无环，返回结果；否则有环，返回空数组

**时间复杂度**：`O(V + E)` — 每个节点和边只被访问一次  
**空间复杂度**：`O(V + E)` — 邻接表 + 入度数组 + 队列

---

### 代码实现

```python
from collections import deque
from typing import List

class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        # 1. 建图 + 统计入度
        graph = [[] for _ in range(numCourses)]  # 邻接表
        in_degree = [0] * numCourses              # 入度数组
        
        for course, pre in prerequisites:
            graph[pre].append(course)   # 先修课 pre -> 课程 course
            in_degree[course] += 1      # 课程 course 的入度 +1
        
        # 2. 初始化队列：入度为 0 的课程可以直接学
        queue = deque()
        for i in range(numCourses):
            if in_degree[i] == 0:
                queue.append(i)
        
        # 3. BFS 拓扑排序
        result = []
        while queue:
            curr = queue.popleft()
            result.append(curr)
            
            # 学完 curr 后，它后继课程的入度 -1
            for next_course in graph[curr]:
                in_degree[next_course] -= 1
                if in_degree[next_course] == 0:
                    queue.append(next_course)
        
        # 4. 判断是否有环
        return result if len(result) == numCourses else []
```

---

#### 方法二：DFS + 状态标记（更直观地理解环检测）

**核心思想**：用三种状态标记节点，DFS 过程中如果遇到「正在访问」的节点，说明有环。

- `0 = 未访问`（白色）
- `1 = 正在访问`（灰色）— 当前 DFS 路径上的节点
- `2 = 已访问`（黑色）— 该节点及其所有后继都已处理完毕

```python
class SolutionDFS:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        # 0=未访问, 1=正在访问, 2=已访问
        visited = [0] * numCourses
        graph = [[] for _ in range(numCourses)]
        
        for course, pre in prerequisites:
            graph[pre].append(course)
        
        result = []
        has_cycle = False
        
        def dfs(course: int) -> None:
            nonlocal has_cycle
            if has_cycle:
                return
            
            if visited[course] == 1:   # 遇到「正在访问」的节点 -> 有环！
                has_cycle = True
                return
            if visited[course] == 2:   # 已处理过，直接返回
                return
            
            visited[course] = 1         # 标记为「正在访问」
            for next_course in graph[course]:
                dfs(next_course)
            visited[course] = 2         # 标记为「已访问」
            result.append(course)       # 后序遍历：子节点先加入，父节点后加入
        
        for i in range(numCourses):
            if visited[i] == 0:
                dfs(i)
        
        if has_cycle:
            return []
        
        # DFS 后序遍历的逆序就是拓扑排序结果
        return result[::-1]
```

---

### 复杂度对比

| 方法 | 时间复杂度 | 空间复杂度 | 特点 |
|------|-----------|-----------|------|
| Kahn (BFS) | O(V + E) | O(V + E) | 直观，适合找字典序最小的拓扑序（用优先队列） |
| DFS | O(V + E) | O(V + E) | 代码更简洁，后序遍历逆序即结果 |

---

### 🎤 面试官连环问

> **Q1：如果要求返回字典序最小的课程顺序，怎么做？**
> 
> 🎯 **你**：把 Kahn 算法中的普通队列换成**最小堆（优先队列）**，每次取编号最小的入度为 0 的节点。时间复杂度变为 `O(V log V + E)`。

> **Q2：如何检测图中是否存在环？**
> 
> 🎯 **你**：两种方法：
> - Kahn 算法：最后结果节点数 < 总节点数，说明有环
> - DFS：用三色标记法，遇到「正在访问」的节点即说明有环

> **Q3：这个算法和深度学习框架的计算图有什么关系？**
> 
> 🎯 **你**：PyTorch 的 autograd 引擎在反向传播时，需要按照**拓扑序**遍历计算图来求梯度。每个 tensor 的 `grad_fn` 构成了一个 DAG，框架内部会确保依赖节点的梯度先计算完毕，再计算当前节点的梯度。如果用户代码写成了循环引用（比如 `a = a + 1` 无限循环），就会触发类似「环检测」的错误。

> **Q4：如果课程有「学期」限制（每学期最多上 k 门课），最少需要几个学期？**
> 
> 🎯 **你**：这是「并行课程安排」问题。在 Kahn 算法的基础上，按层 BFS，每一层就是可以并行上的课程。记录 BFS 的层数即为最少学期数。如果每层最多 k 门，需要额外判断是否超出限制，超出的顺延到下一层。

---

## 面试技巧：模型训练与分布式训练

### 一、训练范式全景图

```
单机单卡                    单机多卡                    多机多卡
   │                         │                         │
   ▼                         ▼                         ▼
┌─────────┐            ┌─────────────┐           ┌──────────────┐
│ Data    │            │ DataParallel│           │ Distributed  │
│ Parallel│            │ (DP)        │           │ DataParallel │
│ (最简)  │            │ 单进程多线程 │           │ (DDP)        │
└─────────┘            │ 单卡负载大   │           │ 多进程       │
                       └─────────────┘           │ 推荐 ✅      │
                                                 └──────────────┘
                                                          │
                    ┌─────────────────────────────────────┼───────────────────────┐
                    ▼                                     ▼                       ▼
              ┌──────────┐                          ┌──────────┐             ┌──────────┐
              │ ZeRO-1   │                          │ ZeRO-2   │             │ ZeRO-3   │
              │ 优化器   │                          │ 优化器+  │             │ 优化器+  │
              │ 状态分片 │                          │ 梯度分片 │             │ 参数分片 │
              └──────────┘                          └──────────┘             └──────────┘
                    │                                     │                       │
                    └─────────────────────────────────────┴───────────────────────┘
                                          │
                                          ▼
                                    ┌──────────┐
                                    │ FSDP     │
                                    │ PyTorch  │
                                    │ 官方实现 │
                                    └──────────┘
```

---

### 二、数据并行（Data Parallelism, DP）

**核心思想**：每个 GPU 都有**完整的模型副本**，数据被分成多份分别送入不同 GPU，各自计算梯度后**同步**。

```python
# PyTorch DDP 最小示例
import torch
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

def setup():
    dist.init_process_group("nccl")  # NCCL = NVIDIA Collective Communication Library
    
model = MyModel().to(local_rank)
ddp_model = DDP(model, device_ids=[local_rank])

# 前向 + 反向
ddp_model(input).loss.backward()
# DDP 自动在所有 GPU 间同步梯度（AllReduce）
optimizer.step()
```

**DDP vs DP 的关键区别：**

| 特性 | DataParallel (DP) | DistributedDataParallel (DDP) |
|------|-------------------|-------------------------------|
| 进程模型 | 单进程多线程 | 多进程（每个 GPU 一个进程） |
| 梯度同步 | 主 GPU 收集梯度 | 分布式 AllReduce |
| 通信效率 | 低（GIL 瓶颈） | 高（NCCL 优化） |
| 推荐使用 | ❌ 不推荐 | ✅ 生产标准 |

**DDP 的梯度同步机制：**

```
GPU 0: 计算梯度 g0 ──┐
GPU 1: 计算梯度 g1 ──┼──> AllReduce ──> 每个 GPU 得到平均梯度 (g0+g1+g2+g3)/4
GPU 2: 计算梯度 g2 ──┤
GPU 3: 计算梯度 g3 ──┘
```

> 💡 **Ring AllReduce**：DDP 默认使用 Ring AllReduce 算法，通信复杂度 `O(2(N-1)/N * data_size)`，近似线性扩展。

---

### 三、模型并行（Model Parallelism, MP）

**核心思想**：模型太大，单卡放不下，把**模型的不同层**放在不同 GPU 上。

```python
# 简单的模型并行：把模型拆到两个 GPU
class ModelParallelModel(nn.Module):
    def __init__(self):
        super().__init__()
        # 第一层在 GPU 0
        self.layer1 = nn.Linear(1024, 4096).to('cuda:0')
        # 第二层在 GPU 1
        self.layer2 = nn.Linear(4096, 1024).to('cuda:1')
    
    def forward(self, x):
        x = self.layer1(x.to('cuda:0'))
        x = self.layer2(x.to('cuda:1'))  # 数据从 GPU 0 -> GPU 1
        return x
```

**问题**：
- **GPU 利用率低**：同一时间只有一个 GPU 在计算，其他在等
- **通信开销大**：层间激活值需要在 GPU 间传输

---

### 四、流水线并行（Pipeline Parallelism, PP）

**核心思想**：把模型按层分段，不同段在不同 GPU 上，同时引入 **micro-batch** 实现流水线填充。

```
传统模型并行（无流水线）：          流水线并行（GPipe / PipeDream）：
                                   
GPU 0: [====L1====]                GPU 0: [L1][L1][L1][L1]  ← micro-batch 流水
GPU 1:           [====L2====]      GPU 1:    [L2][L2][L2][L2]
GPU 2:                     [==L3=] GPU 2:       [L3][L3][L3][L3]
       ↑ 大量空闲                          ↑ 气泡减少，利用率提升
```

**GPipe**：
- 前向计算完所有 micro-batch 才开始反向
- 需要保存多个 micro-batch 的中间激活值 → **内存开销大**

**PipeDream**：
- 1F1B（1 Forward 1 Backward）调度
- 每个 micro-batch 前向完立即反向
- 内存效率高，但权重版本不一致（需要巧妙处理）

---

### 五、ZeRO：零冗余优化器（Zero Redundancy Optimizer）

**核心思想**：数据并行时，每个 GPU 都保存一份完整的**优化器状态、梯度、参数**。ZeRO 把它们**分片**到不同 GPU 上。

| 阶段 | 分片内容 | 显存节省 | 示例：Adam + FP16 |
|------|----------|----------|-------------------|
| ZeRO-1 | 优化器状态（16 bytes/param） | 4x | 7.5B 模型可放到 8x V100 |
| ZeRO-2 | + 梯度（2 bytes/param） | 8x | 14B 模型可放到 8x V100 |
| ZeRO-3 | + 参数（2/4 bytes/param） | 与数据并行度线性相关 | 100B+ 模型 |

```
ZeRO-3 通信模式：

前向传播：         反向传播：           参数更新：
GPU 0: [p0]       GPU 0: [g0]         AllGather 收集完整参数
GPU 1: [p1]  ──>  GPU 1: [g1]  ──>    本地计算更新
GPU 2: [p2]       GPU 2: [g2]         ReduceScatter 分发更新后参数
GPU 3: [p3]       GPU 3: [g3]

AllGather: 收集分片 -> 完整参数
ReduceScatter: 计算完 -> 分片存储
```

**DeepSpeed ZeRO-Offload**：把优化器状态甚至计算放到 CPU/NVMe 上，用显存换内存/磁盘。

---

### 六、3D 并行（3D Parallelism）

工业界大模型训练通常组合多种并行策略：

```
3D 并行 = 数据并行 (DP) × 流水线并行 (PP) × 张量并行 (TP)

示例：GPT-3 175B 在 1024 张 A100 上训练
- 张量并行 (TP)：8 GPUs（同一节点内 NVLink 高速互联）
- 流水线并行 (PP)：12 个 stage
- 数据并行 (DP)：1024 / (8 × 12) = 约 10

总 GPU 数 = TP × PP × DP
```

**张量并行（Tensor Parallelism）**：
- 把单层内的计算拆分（如矩阵乘法的列拆分 / 行拆分）
- Megatron-LM 的核心技术
- 需要高效的 AllReduce/AllGather

---

### 七、混合精度训练（Mixed Precision Training）

```
FP32（单精度）：1 符号位 + 8 指数位 + 23 尾数位 = 32 bit
FP16（半精度）：1 符号位 + 5 指数位 + 10 尾数位 = 16 bit → 范围小，容易下溢
BF16（脑浮点）：1 符号位 + 8 指数位 + 7 尾数位 = 16 bit → 范围同 FP32，精度略低
```

**FP16 训练的问题**：
1. **梯度下溢**（Gradient Underflow）：梯度过小，FP16 表示不了，变成 0
2. **舍入误差**（Rounding Error）：FP16 累加精度不够

**解决方案：AMP（Automatic Mixed Precision）**

```python
from torch.cuda.amp import autocast, GradScaler

scaler = GradScaler()

for data, label in dataloader:
    optimizer.zero_grad()
    
    with autocast():  # 前向用 FP16
        loss = model(data)
    
    scaler.scale(loss).backward()  # 梯度缩放，防止下溢
    scaler.step(optimizer)
    scaler.update()  # 动态调整缩放因子
```

**Loss Scaling 原理**：
- 前向时 loss 乘以一个大的缩放因子（如 `2^16`）
- 反向传播时梯度也按相同比例放大
- 更新参数前再除以缩放因子
- 如果梯度出现 Inf/NaN，跳过更新并降低缩放因子

**BF16 vs FP16**：
- BF16：不需要 Loss Scaling，更稳定，但精度稍低 → **推荐**
- FP16：需要 Loss Scaling，省显存更多 → 老硬件（不支持 BF16）用

---

### 八、梯度累积（Gradient Accumulation）

**场景**：GPU 显存不够放大的 batch size，但模型需要大 batch 才能收敛好。

**核心思想**：把大 batch 拆成多个小 batch，每个小 batch 计算梯度但不立即更新，**累积多个小 batch 的梯度后再更新**。

```python
# 等效 batch_size = 64，但 GPU 每次只处理 8
accumulation_steps = 8

for i, (data, label) in enumerate(dataloader):
    loss = model(data) / accumulation_steps  # 注意：loss 要除以步数
    loss.backward()  # 梯度累积（自动累加到 .grad）
    
    if (i + 1) % accumulation_steps == 0:
        optimizer.step()   # 累积够了再更新
        optimizer.zero_grad()
```

**注意点**：
- Loss 要除以 `accumulation_steps`，保证等效梯度一致
- BatchNorm 层会有影响（统计量是小 batch 的），可以用 GroupNorm 替代
- 学习率调度要和等效 batch size 对齐

---

### 九、Checkpoint 与断点续训

**模型检查点保存策略**：

| 策略 | 频率 | 内容 | 适用场景 |
|------|------|------|----------|
| Full Checkpoint | 每 epoch / 固定步数 | 模型参数 + 优化器状态 + 学习率调度器 | 常规训练 |
| Sharded Checkpoint | 同上 | ZeRO 分片存储 | 大模型，节省单卡存储 |
| Minimal Checkpoint | 关键节点 | 仅模型参数 | 推理部署、快速恢复 |

```python
# PyTorch FSDP 检查点（sharded）
from torch.distributed.checkpoint import save_state_dict, load_state_dict

# 保存：每个 rank 只存自己的分片
save_state_dict(state_dict=fsdp_model.state_dict(), storage_writer=writer)

# 加载：每个 rank 只加载自己的分片
load_state_dict(state_dict=fsdp_model.state_dict(), storage_reader=reader)
```

**断点续训要注意的**：
1. 随机种子要续上（dataloader 的 sampler 状态）
2. 优化器状态要恢复（尤其是 Adam 的 momentum 缓冲）
3. 学习率调度器状态
4. 全局步数（影响 warmup、annealing 等）

---

### 十、高频面试连环问

> **Q1：DDP 的梯度同步是怎么实现的？**
> 
> 🎯 **你**：DDP 使用 **Ring AllReduce** 算法。每个 GPU 只和相邻的两个 GPU 通信，形成一个环。通信分两步：
> 1. **Reduce-Scatter**：每个 GPU 把自己的梯度分片传给下一个 GPU，同时接收上一个 GPU 的分片，经过 N-1 轮后，每个 GPU 拥有一部分完整的累加梯度
> 2. **All-Gather**：每个 GPU 把自己拥有的累加梯度分片广播给所有其他 GPU，经过 N-1 轮后，所有 GPU 拥有完整的平均梯度
> 
> 总通信量：`2(N-1)/N * data_size`，当 N 很大时近似 `2 * data_size`，与 GPU 数无关。

> **Q2：ZeRO-1/2/3 的区别是什么？**
> 
> 🎯 **你**：ZeRO 是零冗余优化器，解决数据并行中每个 GPU 都存完整副本的冗余问题：
> - **ZeRO-1**：只分片**优化器状态**（ Adam 的 momentum/variance），显存省 4 倍
> - **ZeRO-2**：加分片**梯度**，显存省 8 倍
> - **ZeRO-3**：加分片**参数**，显存节省与数据并行度线性相关
> 
> 代价是通信量增加：ZeRO-3 需要在 forward 前 AllGather 参数，backward 后 ReduceScatter 更新。

> **Q3：什么情况下用数据并行，什么情况下用模型并行？**
> 
> 🎯 **你**：**先数据并行，不够再模型并行**：
> - 数据并行：模型能放进单卡，想加速训练 → 首选 DDP
> - 模型并行：模型太大单卡放不下 → 必须 MP
> - 混合并行：超大模型（如 GPT-3）→ 3D 并行（DP × PP × TP）
> 
> 决策流程：单卡能放下？→ DDP。能放下但 batch 太小？→ 梯度累积。放不下？→ Pipeline Parallel。单层放不下？→ Tensor Parallel。

> **Q4：混合精度训练中 Loss Scaling 是为什么？**
> 
> 🎯 **你**：FP16 的表示范围比 FP32 小很多，小数点后很多位直接变成 0。反向传播时梯度可能下溢（underflow）变成 0，导致参数不更新。Loss Scaling 在反向传播前把 loss 乘以一个大的因子（如 `2^16`），梯度也按同比例放大，更新前再除回去。同时用一个动态缩放因子，如果出现 Inf/NaN 就减小因子，连续正常就增大。

> **Q5：训练大模型时 OOM 了，排查思路是什么？**
> 
> 🎯 **你**：按这个顺序排查：
> 1. **减小 batch size** — 最直接
> 2. **梯度累积** — 保持等效 batch size
> 3. **混合精度训练** — FP16/BF16 省一半显存
> 4. **激活值检查点（Activation Checkpointing）** — 用计算换显存，重算前向不存中间结果
> 5. **ZeRO-Offload** — 把优化器状态放到 CPU
> 6. **模型并行 / 流水线并行** — 分布式拆分模型
> 7. **DeepSpeed ZeRO-Infinity** — 把参数 offload 到 NVMe

> **Q6：AllReduce / AllGather / ReduceScatter 的区别？**
> 
> 🎯 **你**：
> - **AllReduce**：所有节点都有数据，运算后所有节点得到相同结果（如求平均梯度）
> - **AllGather**：每个节点有不同分片，收集后所有节点得到完整数据
> - **ReduceScatter**：每个节点有完整数据，运算后每个节点只得到一部分结果
> 
> DDP 用 AllReduce 同步梯度，ZeRO-3 用 AllGather 收集参数、ReduceScatter 分发更新。

---

### 十一、速查表：分布式训练选型决策树

```
单卡能放下模型？
├── 是 → 数据并行 (DDP)
│       └── batch_size 够大？
│           ├── 是 → 直接训练 ✅
│           └── 否 → 梯度累积 或 换大 batch 优化器 (LARS/LAMB)
└── 否 → 模型太大
        ├── 单层能放下？
        │   ├── 是 → Pipeline Parallel (GPipe/PipeDream)
        │   └── 否 → Tensor Parallel (Megatron-LM)
        └── 都放不下 → 3D 并行 (DP + PP + TP) + ZeRO-Offload
```

---

### 十二、关键术语中英对照

| 中文 | 英文 | 一句话解释 |
|------|------|-----------|
| 数据并行 | Data Parallelism (DP) | 多卡各有一份模型，数据分片并行 |
| 模型并行 | Model Parallelism (MP) | 模型拆分到多卡，数据串行流过 |
| 流水线并行 | Pipeline Parallelism (PP) | 模型按层分段，micro-batch 流水填充 |
| 张量并行 | Tensor Parallelism (TP) | 单层内矩阵拆分计算 |
| 分布式数据并行 | DistributedDataParallel (DDP) | PyTorch 多进程数据并行 |
| 完全分片数据并行 | Fully Sharded Data Parallel (FSDP) | PyTorch 官方 ZeRO-3 实现 |
| 全归约 | AllReduce | 多节点数据聚合，结果分发到所有节点 |
| 全收集 | AllGather | 多节点数据收集，每个节点得到完整数据 |
| 归约分散 | ReduceScatter | 聚合后分片分发 |
| 混合精度 | Mixed Precision | FP16/BF16 + FP32 混合计算 |
| 损失缩放 | Loss Scaling | 防止 FP16 梯度下溢 |
| 梯度累积 | Gradient Accumulation | 小 batch 累积模拟大 batch |
| 激活值检查点 | Activation Checkpointing | 用重算换显存 |
| 分片优化器状态 | ZeRO / FSDP | 分布式存储优化器状态/梯度/参数 |

---

> 🔥 **今日金句**：分布式训练的核心矛盾是**通信 vs 计算**的权衡。数据并行通信少但显存冗余，模型并行显存高效但利用率低，3D 并行是工业界的「我全都要」。
