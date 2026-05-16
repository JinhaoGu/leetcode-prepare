# Greedy

> **Day 18** | 3 problems | Difficulty: Medium
>
> Greedy algorithms: at each step, make the locally optimal choice, hoping for a globally optimal result. Unlike DP, greedy doesn't look back.

---

## Table of Contents

1. [Jump Game](#1-jump-game-medium) — Medium
2. [Jump Game II](#2-jump-game-ii-medium) — Medium
3. [Gas Station](#3-gas-station-medium) — Medium

---

## Greedy vs DP

| | Greedy | DP |
|------|------|------|
| Decision | Best at each step, no going back | Considers all subproblems |
| Proof | Must prove correctness | Naturally correct (exhaustive) |
| Efficiency | Usually O(n) | Usually O(n²) or O(n) |

---

## 1. Jump Game (Medium)

**LeetCode 55. Jump Game** | 

### Problem

Each element = max jump distance from that position. Can you reach the last index?

```
Input: nums = [2, 3, 1, 1, 4]
Output: True
```

### Approach

Maintain `max_reach` = farthest index reachable so far. If at any point `i > max_reach`, return False.

```python
def canJump(nums):
    max_reach = 0
    for i, jump in enumerate(nums):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + jump)
    return True
```

---

## 2. Jump Game II (Medium)

**LeetCode 45. Jump Game II** | 

### Problem

Assuming you can always reach the end, find the minimum number of jumps.

```
Input: nums = [2, 3, 1, 1, 4]
Output: 2
```

### Approach

Treat each jump as a BFS "level". Track the farthest reachable within the current jump range.

```python
def jump(nums):
    jumps = 0
    cur_end = 0
    cur_farthest = 0
    for i in range(len(nums) - 1):
        cur_farthest = max(cur_farthest, i + nums[i])
        if i == cur_end:  # must jump
            jumps += 1
            cur_end = cur_farthest
    return jumps
```

---

## 3. Gas Station (Medium)

**LeetCode 134. Gas Station** | 

### Problem

Circular route with gas stations. `gas[i]` = fuel available, `cost[i]` = fuel to next station. Find the starting station to complete the circuit, or -1.

```
Input: gas = [1, 2, 3, 4, 5], cost = [3, 4, 5, 1, 2]
Output: 3
```

### Approach

If total gas < total cost → impossible. Otherwise, whenever the tank goes negative, reset and try the next station as the new start.

```python
def canCompleteCircuit(gas, cost):
    if sum(gas) < sum(cost):
        return -1
    start = 0
    tank = 0
    for i in range(len(gas)):
        tank += gas[i] - cost[i]
        if tank < 0:
            start = i + 1
            tank = 0
    return start
```

---

## Greedy Summary

### Common Greedy Problems

| Type | Greedy Strategy | Problems |
|------|----------------|----------|
| Jump | Maintain farthest reachable | 55, 45 |
| Gas Station | Reset start when tank < 0 | 134 |
| Interval selection | Sort by end time | 435 |
| Assignment | Sort by demand | 455 |

### When is greedy correct?

Greedy is not always correct! You need to reason: does a locally optimal choice always lead to a globally optimal solution? If you can't prove it → use DP to be safe.
