# Day 111 — 救生艇 + DPO 与 RLHF 全流程 🚤

> 📅 2026-09-23 · Week 17 Day 4 · 连续更新第 111 天
>
> 主题：强化学习与 RL 训练工程 🧠 · 从"四个模型同时在线"到"两个模型就够"

Week 17 前半程的脉络很清晰：Day 108 学了 MDP / Bellman，Day 109 学了策略梯度，Day 110 学了 PPO —— 也就是 InstructGPT 用来对齐大模型的那把刀。

但 PPO-RLHF 有个著名的工程地狱：**训练时要同时维护 policy、reference、reward、value 四个模型**，rollout 贵、超参敏感、reward hacking 防不胜防。于是 2023 年 Rafailov 等人提出了 **DPO（Direct Preference Optimization）**：直接从偏好对训练，**不需要奖励模型，不需要采样 rollout，一阶监督训练直接拿下**——现在已经是开源社区对齐的默认起点（Zephyr、Llama-3 Instruct 的配方里都有它）。

今天的算法题「救生艇」是排序 + 双指针的贪心经典，它的内核是"**成对配对**：想清楚谁和谁坐一条船"。这和 DPO 的训练数据形态完美呼应——DPO 学的正是 `(prompt, chosen, rejected)` 三元组里的**成对比较**。比较产生秩序，秩序本身就是奖励。

---

## 1) 今日算法题

### 救生艇（Boats to Save People）

**题意**：给定数组 `people`（`people[i]` 是第 i 个人的体重）和整数 `limit`。每艘船最多坐 **2 人**，且载重之和不能超过 `limit`。求运送所有人的最少船数。

```
输入: people = [3, 2, 2, 1], limit = 3
输出: 3
解释: (1, 2) (2) (3) —— 3 条船
输入: people = [3, 5, 3, 4], limit = 5
输出: 4
解释: (3) (3) (4) (5) —— 5 和 4 谁都带不动别人
约束: 1 <= people.length <= 5×10⁴
```

**关键约束**：每船最多两人。这决定了它是"排序 + 双指针"而不是背包问题。

### 思路：排序 + 双指针（最重的人先安排）

贪心策略一句话：**每次看最重的人 h 和最轻的人 l，能同乘就同乘，不能则 h 独占一船**。

```
排序后: [1, 2, 2, 3], limit = 3
lo=0(1), hi=3(3): 1+3=4 > 3 → 3 独占 → hi--
lo=0(1), hi=2(2): 1+2=3 ≤ 3 → 同乘 → lo++, hi--
lo=2(2), hi=2(2): 只剩 1 人 → 1 ≤ 3 也走"同乘"分支 → lo++, hi--
共 3 条船 ✅
```

**为什么贪心是对的？（交换论证，面试要能讲）**

聚焦最重的人 `h` 和最轻的人 `l`：

1. 若 `h + l > limit`：因为其他所有人都 ≥ l，所以 **h 和任何人同乘都会超载** → h 只能独占一船。这不是选择，是数学强制。
2. 若 `h + l ≤ limit`：让 h 和 l 同乘。**不损失任何可行性**——l 是最轻的，跟 h 能坐下，跟任何人都能坐下；把 l"用掉"之后，剩下的问题（更重的那些人）不比原子问题更难。

两种情况下"先处理最重的人"都导向最优子结构 → 贪心成立。注意第二步里把 l 和 h 配对而不是留给别人，本质是"**用最小的代价满足最重的需求**"，和调度、背包贪心的哲学一脉相承。

**为什么必须先排序？** 不排序时"最轻"和"最重"无从谈起，反例随手构造：`[2, 3, 1], limit=3`，无序扫描先配对 (2,3) 超载 → 2 独占，再 (1) —— 共 3 条；而排序后 (1,2)(3) 只需 2 条。排序 O(n log n) 是这个贪心成立的前提。

### 代码

**Go：**

```go
func numRescueBoats(people []int, limit int) int {
    sort.Ints(people)
    lo, hi := 0, len(people)-1
    boats := 0
    for lo <= hi {
        if people[lo]+people[hi] <= limit {
            lo++ // 最轻的搭上了最重的那条船
        }
        hi-- // 最重的人无论如何都上船了（独占或同乘）
        boats++
    }
    return boats
}
```

**Python：**

```python
def numRescueBoats(people, limit):
    people.sort()
    lo, hi = 0, len(people) - 1
    boats = 0
    while lo <= hi:
        if people[lo] + people[hi] <= limit:
            lo += 1
        hi -= 1
        boats += 1
    return boats
```

> ⚠️ **边界陷阱**：`lo <= hi` 的等号情况（最后剩一个人）必须走循环体——此时 `people[lo]+people[hi]` 是他自己加自己？不，`lo == hi` 时 sum = 2·w ≥ w，但判断只看 `<= limit`；只要 `w ≤ limit`（题目保证），就会 `lo++` 然后 `hi--`，循环结束，船数 +1，正确。写错成 `lo < hi` 会漏掉最后一条船。

### 复杂度

| 维度 | 复杂度 | 说明 |
|---|---|---|
| 时间 | `O(n log n)` | 排序主导，双指针扫描 O(n) |
| 空间 | `O(1)` | 忽略排序栈开销；原地双指针 |

### 面试官连环问

> 💬 **问：每艘船改成最多坐 k 个人，贪心还成立吗？**
>
> 🎯 答：k=2 是本题，贪心最优。k≥3 时问题变质——它逼近装箱问题（Bin Packing），双指针贪心**不再保证最优**（反例：`limit=10, k=3, [5,5,5,5,1,1,1,1]`，贪心先装满前三条 5+5+5，剩四个 1 还要 2 条共 5 条；而 5+1+1 的组合 4 条就够）。面试答法：k 小时贪心近似 + 说明失效场景；要求精确最优则转 DP/搜索，或二分船数 + 可行性贪心校验。

> 💬 **问：如果是数据流（人来一个走一个），怎么维护？**
>
> 🎯 答：需要"动态有序结构"：用平衡树 / 两个堆维护当前体重 multiset，每次来新人时取最轻与最重做同样的配对判定，离开则删除。代价是每次操作 O(log n)。这就是"双指针"在动态场景的标准退化形态——有序性是贪心的燃料。

> 💬 **问：如果要求同船两人的体重差 ≤ d 呢？**
>
> 🎯 答：约束变化后配对策略从"两极配对"变成"就近配对"：排序后滑窗/双指针找每个 i 能匹配的最远 j（满足 `w_j − w_i ≤ d` 且 `w_i + w_j ≤ limit`），变成区间匹配问题，可用贪心 + 计数。核心还是那句：**排序之后，"谁和谁配对"才看得见**。

---

## 2) 面试技巧：DPO 与 RLHF 全流程

### 2.1 先串全景：RLHF 三步流水线（面试要求"一张图讲 3 分钟"）

被问"讲讲 RLHF"，标准展开是 InstructGPT（Ouyang et al., 2022）确立的三段式：

```
① SFT（Supervised Fine-Tuning）
   演示数据 (prompt, 人类写的优质回答) → 常规监督微调
   学的是："像人一样说话"
        │
② RM（Reward Model）
   同一 prompt 采样多条回答 → 人类标注 A/B 偏好 (y_w ≻ y_l)
   用 Bradley-Terry 模型训一个标量打分器 r_φ(x, y)
   学的是："什么叫好"
        │
③ RL（PPO 阶段）
   π_θ 采样回答 → r_φ 打分 → PPO 最大化奖励
   目标: max E[r_φ(x,y)] − β·KL(π_θ ‖ π_ref)   ← Day 110 全套在这里
   学的是："在一众好答案里，更讨好奖励函数"
```

一句话总结三段分工：**SFT 学"像人"，RM 学"什么是好"，RL 学"怎么更好"**。前三天学的 MDP / 策略梯度 / PPO，全部住在第③步。

而 DPO 的故事就是：**第③步太贵了，而且第②步训练出来的 RM 可以被"偷"出来塞进第①步的监督目标里**——整个 RLHF 塌缩成一次监督训练。

### 2.2 Reward Model：从"成对比较"到标量分数

先看 RM 怎么训练，因为 DPO 的数学正是从这里长出来的。

**为什么不用绝对打分？** 让人类给回答打 1~10 分，标注员之间的一致性很差（同一个人不同时刻打分都漂移）；而"两条回复你更喜欢哪条"这个**相对比较**，标注一致性高得多、成本也低。这是整个偏好学习大厦的地基。

**Bradley-Terry 模型**（1952 年的老统计模型，2022 年被 RLHF 翻红）把成对偏好变成概率：

```
P(y_w ≻ y_l | x) = σ( r_φ(x, y_w) − r_φ(x, y_l) )
                    └──── sigmoid ────┘
```

即"chosen 的分数比 rejected 高越多，人类选它的概率越大"。训练 loss 就是二分类交叉熵：

```
L_RM = −E_{(x,y_w,y_l)~D} [ log σ( r_φ(x,y_w) − r_φ(x,y_l) ) ]
```

两个高频考点：

1. **RM 的标度不唯一**：r 整体加一个常数、乘一个正数，比较结果不变。奖励只需要"**序**"（ordering），不需要绝对值——这点在 DPO 推导里是关键伏笔。
2. **RM 本质是二分类器**：它预测的"人类偏好概率"只在差值上有意义，** RM 分数的绝对值没有跨 prompt 可比性**。

### 2.3 PPO 阶段的工程地狱（DPO 存在的理由）

Day 110 学的 PPO 在 LLM 上跑起来是这样的：

| 要同时在线的东西 | 作用 | 代价 |
|---|---|---|
| policy π_θ | 被训练的语言模型 | 必须 |
| reference π_ref | KL 惩罚的锚点（冻结的 SFT 模型） | 一份完整权重 + 前向 |
| reward model r_φ | 给 rollout 打分 | 一份完整权重 + 前向 |
| value model V（Critic） | GAE 的 bootstrap | 常与 policy 共享 backbone，另挂 value head |

四个大模型，rollout 生成（自回归采样，慢）、四个前向/反向、PPO 的超参组（clip ε、KL β、GAE λ、epoch K）互相纠缠——**训崩一次损失一天 GPU 时**。InstructGPT 论文自己都在附录里报告了大量 PPO 翻车实录。

工业界因此一直在问：**能不能不要 RM、不要 rollout，直接用偏好数据把模型训好？** 之前试过 best-of-n 蒸馏、ranked reward 之类的折中，直到 DPO 给出一个干净到令人发指的答案。

### 2.4 DPO 推导：把奖励函数"偷"出来（面试要求徒手推三步）

**第一步：KL 约束的 RL 目标有闭式解。**

PPO 阶段解的其实是这个目标（固定 prompt x，省略期望）：

```
max_π  E_{y~π}[ r(x,y) ] − β·D_KL( π(y|x) ‖ π_ref(y|x) )
```

好消息：这个目标**有解析最优解**（变分法 / 拉格朗日一步出）：

```
π*(y|x) = (1 / Z(x)) · π_ref(y|x) · exp( r(x,y) / β )

其中 Z(x) = Σ_y π_ref(y|x)·exp(r(x,y)/β)   ← 配分函数，只依赖 x
```

**第二步：反解出奖励 r。**

把上式取对数、整理：

```
r(x,y) = β·log( π*(y|x) / π_ref(y|x) ) + β·log Z(x)
           └──────────┬──────────┘        └─────┬─────┘
           策略比值项（可算！）            配分函数（讨厌）
```

含义炸裂：**最优策略 π\* 本身就藏着奖励函数**——奖励 = 策略对参考策略的比值（乘 β），只差一个只依赖 prompt 的常数项。

**第三步：代回 Bradley-Terry，常数项消掉。**

```
P(y_w ≻ y_l|x) = σ( r(x,y_w) − r(x,y_l) )
              = σ( β·log(π*(y_w)/π_ref(y_w)) − β·log(π*(y_l)/π_ref(y_l)) )
              
              Z(x) 在两项里各出现一次，相减抵消！✨
```

把 π\* 换成我们手头正在训练的 π_θ，直接对这个概率做最大似然，就得到 **DPO loss**：

```
L_DPO(θ) = −E_{(x,y_w,y_l)~D} log σ( β·[ Δ_θ ] )

其中 Δ_θ = [ log π_θ(y_w|x) − log π_ref(y_w|x) ]
         − [ log π_θ(y_l|x) − log π_ref(y_l|x) ]
           └────── 隐式奖励(implicit reward)的差 ──────┘
```

**一句话读懂**：DPO 用 `β·log(π_θ/π_ref)` 作为隐式奖励，监督信号还是 Bradley-Terry 那套成对比较——**模型自己就是奖励模型**，不再需要独立的 r_φ。这就是"偷"的含义：RM 的序信息被完整编码在"当前策略相对参考策略的偏移"里。

### 2.5 DPO loss 逐符号解读 + 代码

逐项拆解 Δ_θ：

| 项 | 含义 |
|---|---|
| `log π_θ(y_w) − log π_θ(y_l)` | 当前模型对 chosen 相对 rejected 的偏好强度 |
| `log π_ref(y_w) − log π_ref(y_l)` | 参考模型（SFT 版）原本就有的偏好强度 |
| 两者相减 | **相对参考模型，我们"额外"偏好了 chosen 多少**——这就是隐式奖励的差 |
| `β` | 隐式奖励的温度 / KL 强度系数。β→0 放任自由，β→∞ 钉死参考模型 |
| `log σ(·)` | 让这个差越大越好 → 增大 chosen 概率、压低 rejected 概率，但被迫和 π_ref 锚着 |

**实现要点（工程面试高频）：**

1. **只对 completion token 累加 log-prob**，prompt token 不计（prompt 在 chosen/rejected 里相同，会抵消，但显式排除更干净、也避免污染）；
2. chosen 和 rejected **拼接成 batch 一次前向**，省一半算力；
3. π_ref **冻结、不反传**——它的 logps 可以预计算缓存，甚至用 LoRA 时 ref 就是"base + 零初始化增量"的恒等技巧；
4. β 典型值 **0.1~0.5**（论文消融区间 0.01~0.5），学习率小、**通常只训 1 epoch**（过拟合会退化，见 2.8）。

**PyTorch 最小实现：**

```python
import torch
import torch.nn.functional as F

def dpo_loss(policy_chosen_logps, policy_rejected_logps,
             ref_chosen_logps,   ref_rejected_logps,
             beta=0.1):
    """
    policy_*_logps: 当前策略对 chosen/rejected 的 sum-log-prob [B]
    ref_*_logps:    参考策略的对应值（detach，不反传）
    """
    pi_logratios  = policy_chosen_logps - policy_rejected_logps   # Δ_θ 的策略部分
    ref_logratios = ref_chosen_logps  - ref_rejected_logps        # Δ_ref
    logits = beta * (pi_logratios - ref_logratios)                # 隐式奖励差 × β
    loss = -F.logsigmoid(logits).mean()                           # BT 二分类 NLL
    return loss

def sum_logps(logits, labels, prompt_len):
    """只对 completion 部分累加 log-prob（labels 中 -100 为 prompt mask）"""
    logps = torch.log_softmax(logits[:, :-1], dim=-1)
    token_logps = torch.gather(logps, 2, labels[:, 1:].unsqueeze(-1)).squeeze(-1)
    mask = (labels[:, 1:] != -100).float()
    return (token_logps * mask).sum(-1)
```

### 2.6 DPO 训练流程（和 PPO 对比着记）

```
DPO:
  数据 (prompt, y_chosen, y_rejected)      ← 静态偏好对，训前备好
  模型 π_θ（SFT 初始化）+ π_ref（SFT 冻结）
  循环:
    batch → 前向算 policy logps + ref logps → dpo_loss → 反传一步
  完。没有 rollout，没有 RM，没有 Critic。

PPO-RLHF:
  循环:
    rollout 采样 → RM 打分 → GAE → K epoch 更新 → 同步旧策略
  四个模型在线，每一步都在烧钱。
```

**显存对比**：PPO-RLHF 阶段要装 policy(train) + ref(infer) + RM(infer) + value(train)；DPO 只要 policy(train) + ref(infer)——**砍掉一半以上**，单卡 24G 都能跑小模型的 DPO。这就是为什么开源社区对齐几乎默认从 DPO 起步（Zephyr-7B、Llama-3  instruct 的 post-training 配方）。

和用户的 veRL 世界连接一下：veRL 这类 agentic RL 框架里，**DPO 是对齐阶段的 baseline recipe，GRPO 才是 RL 主线**（Day 112 的主角）——DPO 训不动的"需要在线探索"的任务（数学推理、agent 轨迹），才轮到 GRPO 上 rollout 采样。

### 2.7 DPO vs PPO-RLHF 对比表

| 维度 | PPO-RLHF | DPO |
|---|---|---|
| 训练范式 | 在线 RL（on-policy rollout） | **离线监督**（静态偏好对） |
| 奖励模型 | 必须，单独训 | **不需要**（隐式奖励 β·log π_θ/π_ref） |
| 训练时采样 | 必须（自回归 rollout，最贵的一步） | **无** |
| 同时在线模型 | 4 个（policy/ref/RM/value） | 2 个（policy/ref，ref 还可缓存） |
| 稳定性 |  notoriously 敏感（clip/KL/熵超参组） | 接近普通 SFT，很稳 |
| 天花板 | 高（可在线探索、可迭代 RM） | 受静态数据分布限制 |
| reward hacking 面 | RM 被钻空子 | BT 假设被钻空子（见 2.8） |
| 数据 | 偏好对（标 RM 用）+ prompt 池 | 偏好对（直接训练） |
| 典型代表 | InstructGPT / GPT-4 早期 | Zephyr / Llama-3 / 开源默认起点 |

### 2.8 DPO 的阿喀琉斯之踵与家族变体

**① 长度偏置（length bias）**：偏好数据里 chosen 往往更长（人类偏好详尽的回答），DPO 会学到"**变长 = 变好**"的捷径，输出越长越啰嗦。监控指标要看 `chosen/rejected 长度差` 的混淆；缓解手段：构造等长偏好对、SimPO 的平均 log-prob、或显式长度正则。

**② 退化模式（degeneration）**：loss 只约束**比值**，不约束绝对概率——policy 可以把 chosen 和 rejected 的 logps **一起压低**，只要差值拉大，loss 照样降。后果是模型整体变丧、输出质量隐性劣化。所以工程上必须**同时监控 chosen 的绝对 logps**（要升或持平），不能只盯 reward margin。

**③ Offline 天花板**：DPO 学的是数据分布内的偏好，**不会探索数据之外的回答**。需要在线改进时演进出 iterative DPO / online DPO（采样新回答 → AI/人类打分 → 加入偏好对重训），再往前一步就是 Self-Rewarding（模型自己当标注员）和 **RLAIF**（AI Feedback 替代人类标注，Constitutional AI 路线）。

**④ 家族变体**（能报出名字就赢了一半）：

| 变体 | 一句话卖点 | 解决什么 |
|---|---|---|
| **IPO**（2023） | 去掉 BT 假设，直接对隐式奖励差做平方损失 | BT 过拟合偏好噪声 |
| **KTO**（2024） | 只要"好/坏"标签，不用成对数据（前景理论） | 成对标注太贵 |
| **ORPO**（2024） | 彻底删掉参考模型，odds-ratio 项并进 SFT loss | π_ref 的显存与前向 |
| **SimPO**（2024） | reference-free：平均 log-prob + 固定 margin γ | 长度偏置 + 参考模型成本 |

### 2.9 高频连环问速答

> 💬 **问：推导里那个 Z(x) 凭什么能消掉？**
>
> 🎯 配分函数 Z(x) = Σ_y π_ref·exp(r/β) 只依赖 prompt x，对同一条 x 下的 y_w 和 y_l 是同一个常数。BT 模型用的是**两个奖励的差**，常数项相减归零。这也是为什么 DPO 不需要知道"奖励的绝对值"——它只需要序，而序不受 Z 影响。

> 💬 **问：DPO 还需要奖励模型吗？它和 RM 是什么关系？**
>
> 🎯 不需要独立 RM。DPO 把奖励定义为隐式奖励 `r_θ = β·log(π_θ/π_ref)`——**当前策略自己充当奖励模型**。可以理解为：RM 的序信息被压缩进了"当前策略相对 SFT 锚点的偏移"里，训练就是在把这个偏移推向偏好数据的方向。

> 💬 **问：β 是干什么的？调大调小分别会怎样？**
>
> 🎯 β 是 KL 强度的倒数旋钮。β 小 → 允许大幅偏离 π_ref，奖励信号强、学得快，但容易退化、语言质量崩；β 大 → 死贴参考模型，稳但几乎学不到东西。论文典型值 0.1~0.5，调参时跟 reward margin、`chosen logps` 绝对值一起网格搜索。

> 💬 **问：DPO 到底算不算强化学习？**
>
> 🎯 谱系上它是**离线 RL**：它解的是和 PPO-RLHF 同一个 KL 约束 RL 目标（闭式解反推回来的），只是求解方式退化成监督学习。所以它两个身份都有——"用监督学习的成本解 RL 的目标"，这也是它工程上如此诱人的原因。面试里答"offline RL 的监督式求解"是最稳的说法。

> 💬 **问：为什么 loss 里只对 completion token 算 log-prob？**
>
> 🎯 三点：① prompt 在 chosen/rejected 间完全相同，其 logps 相减抵消，算了白算；② 不排除会污染梯度（尤其对共享 prefix 的实现）；③ 工程惯例（TRL 的 DPOTrainer 就是这么做的）。但注意 **prompt 的 mask 要用 -100**，让 gather 时不误采。

> 💬 **问：偏好对里的 chosen 必须是同一个模型生成的吗？**
>
> 🎯 不需要，任何来源的 (y_w, y_l) 都能训——人类写的、不同模型采的、历史版本采的混合都可以。但要注意 **off-policy 问题**：数据离当前策略越远，隐式奖励的估计越不准（等价于重要性采样方差），所以迭代式 DPO 每轮都要用**当前模型**重新采样刷新数据。

> 💬 **问：效果上 DPO 和 PPO-RLHF 谁好？**
>
> 🎯 分场景。纯偏好对齐（ helpfulness、无害性）上 DPO 性价比碾压，效果接近；但**需要在线探索的任务**（数学推理、代码、agent 多步轨迹）里 PPO/GRPO 的天花板明显更高——因为 rollout 能产生数据分布之外的新样本，DPO 只能吃数据里已有的。实际配方常是：**SFT → DPO 打底 → 在线 RL（GRPO）冲顶**。

> 💬 **问：Day 110 学的 PPO 是不是白学了？**
>
> 🎯 恰恰相反。① DPO 的目标函数就是 PPO 那个 KL 约束目标的闭式解推出来的，不懂 PPO 的 KL 惩罚就读不懂 DPO 的 β；② 工业界冲顶依然靠在线 RL（GRPO 是 PPO 的减配变体，Day 112 见）；③ 面试连环问里 PPO→DPO→GRPO 是标准三连，缺一环故事就断了。

### 2.10 Day 111 自检清单

- [ ] 一张图默写 RLHF 三步流水线（SFT → RM → PPO），每步说清"学什么"
- [ ] 写出 Bradley-Terry 模型并解释为什么人类标注用相对比较
- [ ] 说出 RM 标度不唯一的原因（只需序，常数/正数倍不变），以及它和 Z(x) 消去的联系
- [ ] 徒手推 DPO 三步：闭式解 → 反解 r → 代入 BT 消 Z(x)
- [ ] 默写 DPO loss，说清 Δ_θ 每一项的含义
- [ ] 写出最小 PyTorch 实现，包括 completion-only logps 的 mask 处理
- [ ] 报出 β 的典型范围（0.1~0.5）和"只训 1 epoch"的工程共识
- [ ] 说出 DPO 两大病理：length bias、degeneration，及各自监控指标
- [ ] 说出至少 3 个 DPO 变体（IPO/KTO/ORPO/SimPO）各自解决什么
- [ ] 能解释"为什么 DPO 是 offline RL 的监督式求解"

---

## 📚 今日参考

- Rafailov et al. (2023), *Direct Preference Optimization: Your Language Model is Secretly a Reward Model*（arXiv:2305.18290）—— DPO 原论文，10 页，推导干净
- Ouyang et al. (2022), *Training Language Models to Follow Instructions with Human Feedback*（InstructGPT，RLHF 三段式的教科书）
- Bradley & Terry (1952), *Rank Analysis of Incomplete Block Designs* —— BT 模型原始论文
- Christiano et al. (2017), *Deep Reinforcement Learning from Human Preferences* —— 用人类偏好做 RL 的开山之作
- Azar et al. (2023), *A General Theoretical Paradigm to Understand Learning from Human Preferences*（IPO）
- Ethayarajh et al. (2024), *KTO: Model Alignment as Prospect Theoretic Optimization*
- Meng et al. (2024), *SimPO: Simple Preference Optimization with a Reference-Free Reward*
- LeetCode 881. Boats to Save People
- TRL 库 `DPOTrainer` 源码 —— 工业级 DPO 实现，工程细节宝库

---

> 🧠 **今天的一句话**：PPO-RLHF 是先造一个裁判（RM），再让模型拼命讨好裁判；DPO 发现裁判根本不需要单独造——他藏在新旧策略的比值里。比较产生秩序，而秩序本身，就是奖励。
