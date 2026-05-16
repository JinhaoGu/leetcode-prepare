# Greedy (贪心算法)

> **Day 18** | 3 题 | 难度: Medium
>
> 贪心算法：每一步都做出当前看起来最好的选择，期望最终得到全局最优解。与 DP 的区别：贪心不回头，DP 会考虑所有可能。

---

## 贪心 vs 动态规划

| | 贪心 | 动态规划 |
|------|------|---------|
| 决策 | 每步选最优，不回头 | 考虑所有子问题，选全局最优 |
| 证明 | 需要证明贪心正确 | 天然正确（枚举所有可能） |
| 效率 | 通常 O(n) | 通常 O(n²) 或 O(n) |
| 适用 | 问题有"贪心选择性质" | 有重叠子问题 |

---

## 目录

1. [Jump Game (跳跃游戏)](#1-jump-game-medium) — Medium
2. [Jump Game II (跳跃游戏 II)](#2-jump-game-ii-medium) — Medium
3. [Gas Station (加油站)](#3-gas-station-medium) — Medium

---

## 1. Jump Game (Medium)

**LeetCode 55. Jump Game** | 🌟🌟

### 题目

数组每个元素表示从该位置最多能跳的距离。判断能否从起点跳到终点。

```
输入: nums = [2, 3, 1, 1, 4]
输出: True
解释: 跳1步到索引1(值3)，再跳3步到终点

输入: nums = [3, 2, 1, 0, 4]
输出: False
解释: 无论怎样都会在索引3(值0)卡住
```

### 思路讲解

**关键**：维护 `max_reach` = 目前能够到达的最远位置。

遍历每个位置 i，如果 i > max_reach（当前位置到不了），直接返回 False。

```
nums = [2, 3, 1, 1, 4]

i=0: max_reach = max(0, 0+2) = 2
i=1: 1 ≤ 2(到得了), max_reach = max(2, 1+3) = 4  ← 能到终点了!
     (实际上此时已可返回True)
i=2: max_reach = max(4, 2+1) = 4
i=3: max_reach = max(4, 3+1) = 4
i=4: 4 ≤ 4, 已到终点 → True
```

```
nums = [3, 2, 1, 0, 4]

i=0: max_reach = 3
i=1: max_reach = max(3, 1+2) = 3
i=2: max_reach = max(3, 2+1) = 3
i=3: max_reach = max(3, 3+0) = 3
i=4: 4 > 3 → 到不了! False
```

### 代码

```python
def canJump(nums):
    max_reach = 0
    for i, jump in enumerate(nums):
        if i > max_reach:  # 当前位置到不了
            return False
        max_reach = max(max_reach, i + jump)
        if max_reach >= len(nums) - 1:
            return True
    return True
```

### 复杂度

- **时间**: O(n)
- **空间**: O(1)

---

## 2. Jump Game II (Medium)

**LeetCode 45. Jump Game II** | 🌟🌟

### 题目

假设一定能跳到终点，求最少的跳跃次数。

```
输入: nums = [2, 3, 1, 1, 4]
输出: 2
解释: 跳1步到索引1(值3)，再跳3步到终点 = 2跳
```

### 思路讲解

**BFS 的思想**：每次跳跃看作一层。维护当前跳跃能到的范围和下一跳能到的最远范围。

```
nums = [2, 3, 1, 1, 4]

初始: jumps=0, cur_end=0, cur_farthest=0

i=0: cur_farthest = max(0, 0+2) = 2
      i == cur_end(0) → 必须跳了
      jumps = 1, cur_end = 2

i=1: cur_farthest = max(2, 1+3) = 4
      i != cur_end → 继续
i=2: cur_farthest = max(4, 2+1) = 4
      i == cur_end(2) → 必须跳了
      jumps = 2, cur_end = 4

i=3: i < cur_end(4) → 继续
i=4: 到达终点

答案: jumps = 2
```

### 代码

```python
def jump(nums):
    jumps = 0
    cur_end = 0       # 当前跳跃范围的最右端
    cur_farthest = 0  # 在当前范围内能到的最远位置

    for i in range(len(nums) - 1):  # 注意：不到最后一个
        cur_farthest = max(cur_farthest, i + nums[i])

        # 到达当前跳跃范围的边界，必须再跳一次
        if i == cur_end:
            jumps += 1
            cur_end = cur_farthest  # 更新范围

    return jumps
```

---

## 3. Gas Station (Medium)

**LeetCode 134. Gas Station** | 🌟🌟

### 题目

环形路线上有 n 个加油站，`gas[i]` 是第 i 站的加油量，`cost[i]` 是第 i 站到下一站的油耗。找出能绕一圈的出发站。如果不存在，返回 -1。

```
输入: gas = [1, 2, 3, 4, 5], cost = [3, 4, 5, 1, 2]
输出: 3
解释:
  站3(气4)→站4(耗1): 油箱 = 4-1 = 3
  站4(气5)→站0(耗2): 油箱 = 3+5-2 = 6
  站0(气1)→站1(耗3): 油箱 = 6+1-3 = 4
  站1(气2)→站2(耗4): 油箱 = 4+2-4 = 2
  站2(气3)→站3(耗5): 油箱 = 2+3-5 = 0
  回到站3! ✓
```

### 思路讲解

**两个关键事实**：
1. 如果总加油量 < 总油耗 → 不可能完成，返回 -1
2. 如果从 A 出发到不了 B，那么 A 到 B 之间任何一站出发也到不了 B（因为从 A 出发到中间某站时油箱 ≥ 0）→ 直接跳到 B+1 重新尝试

```
gas  = [1, 2, 3, 4, 5]
cost = [3, 4, 5, 1, 2]
diff = [-2, -2, -2, 3, 3]

总 diff = 0 ≥ 0 → 可能有解

start=0, tank=0
i=0: tank = -2 < 0 → 重置: start=1, tank=0
i=1: tank = -2 < 0 → 重置: start=2, tank=0
i=2: tank = -2 < 0 → 重置: start=3, tank=0
i=3: tank = 3 > 0 → 继续
i=4: tank = 3+3 = 6 > 0

最终 start=3
```

### 代码

```python
def canCompleteCircuit(gas, cost):
    # 先判断总油量是否够
    if sum(gas) < sum(cost):
        return -1

    start = 0
    tank = 0  # 当前油箱

    for i in range(len(gas)):
        tank += gas[i] - cost[i]
        if tank < 0:
            # 从 start 到不了 i+1，从 i+1 重新开始
            start = i + 1
            tank = 0

    return start
```

---

## 贪心总结

### 常见贪心题型

| 题型 | 贪心策略 | 题号 |
|------|---------|------|
| 跳跃 | 维护能到的最远距离 | 55, 45 |
| 加油站 | 油不够就换起点 | 134 |
| 区间选择 | 按结束时间排序 | 435 |
| 分配问题 | 按需求排序 | 455 |

### 何时能用贪心？

贪心不一定总是对的！使用时需要想清楚：**局部最优是否一定导致全局最优？**

如果不能证明贪心正确 → 用 DP 更安全。

对于 Jump Game：因为每步的可达范围是连续的，所以贪心（维护最远可达）一定正确。

对于 Gas Station：如果总 gas ≥ 总 cost，从累计油箱最低点的下一站出发一定能完成，可证明。
