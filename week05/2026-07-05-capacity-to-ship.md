# Day 30: 在 D 天内送达包裹的能力（Capacity To Ship Packages Within D Days）

> Week 5 第 4 天 | 贪心与二分专题 | 2026-07-05

---

## 今日算法题

**题目描述**

传送带上有一排包裹，第 `i` 个包裹重量为 `weights[i]`。每天按顺序装船，且当天船载重量不超过运载能力。求能在 `D` 天内送达所有包裹的最小运载能力。

**示例**

```
weights = [1,2,3,4,5,6,7,8,9,10], D = 5
输出：15
解释：第1天: 1+2+3+4+5=15，第2天: 6+7=13，第3天: 8，第4天: 9，第5天: 10
```

**思路：二分答案**

- 运载能力最小值：`max(weights)`（最重的包裹必须能装下）
- 运载能力最大值：`sum(weights)`（一天全运完）
- 关键观察：运载能力越大，所需天数越少 → **单调性** → **二分答案**

**代码示例（Python）**

```python
def shipWithinDays(weights, D):
    def canShip(capacity):
        days = 1
        cur = 0
        for w in weights:
            if cur + w > capacity:
                days += 1
                cur = 0
            cur += w
            if days > D:
                return False
        return True
    
    left, right = max(weights), sum(weights)
    while left < right:
        mid = (left + right) // 2
        if canShip(mid):
            right = mid
        else:
            left = mid + 1
    return left
```

**复杂度分析**

- 时间：O(n log(sum−max)) — 验证 O(n)，二分范围 O(log(sum−max))
- 空间：O(1)

**关键点**

- 二分答案的本质：**答案具有单调性，且验证比求解更容易**
- 验证函数必须按顺序装，不能回头重排
- `left` 初始化为 `max(weights)` 而不是 `0`，否则验证函数会卡住

---

## 面试技巧

### 1. "二分答案" 框架怎么讲

推荐表达模板：
> "我观察到答案具有单调性——运载能力越大，所需天数越少。所以先确定上下界，然后写验证函数 `canShip(capacity)`，二分查找最小的可行解。"

面试官一听就知道你懂套路，而不是硬凑解法。

### 2. 验证函数是常错点

常见错误：
- 忘记 `days` 从 1 开始（不是 0）
- 在 `for` 循环里提前 `return False` 放错位置
- 左边界设成 0，导致 `canShip(0)` 永远为 False

### 3. 常见追问及应对

- **如果包裹可以任意顺序组合？** → 变成 NP-hard 的装箱问题，二分答案不再适用
- **如果要求每天工作量尽量均衡？** → 需要 DP 或更复杂的调度算法
- **D 很大或很小时的边界？** → D ≥ len(weights) 时答案就是 max(weights)；D = 1 时答案就是 sum(weights)

### 4. 同类题目推荐

- 875. 爱吃香蕉的 Koko（二分答案 + 验证函数）
- 410. 分割数组的最大值（本题几乎等价）
- 1011. 在 D 天内送达包裹的能力（本题）

---

> 明日预告：Week 5 第 5 天，继续二分 / 贪心进阶
