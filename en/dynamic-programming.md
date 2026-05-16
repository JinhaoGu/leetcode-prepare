# Dynamic Programming

> **Day 15-17** | 8 problems | Difficulty: Easy - Hard
>
> DP is the hardest and most important interview algorithm. Core idea: **break a big problem into smaller ones, remember the small answers to avoid recomputation.**

---

## Table of Contents

1. [Climbing Stairs](#1-climbing-stairs-easy) — Easy
2. [Min Cost Climbing Stairs](#2-min-cost-climbing-stairs-easy) — Easy
3. [House Robber](#3-house-robber-medium) — Medium
4. [Coin Change](#4-coin-change-medium) — Medium
5. [Longest Increasing Subsequence](#5-longest-increasing-subsequence-medium) — Medium
6. [Unique Paths](#6-unique-paths-medium) — Medium
7. [Word Break](#7-word-break-medium) — Medium
8. [Edit Distance](#8-edit-distance-hard) — Hard

---

## DP Five-Step Framework

```
1. Define dp meaning    (dp[i] or dp[i][j] represents what?)
2. Recurrence relation  (how to derive from known subproblems?)
3. Initialize           (dp[0], dp[i][0], etc.)
4. Iteration order      (small→large? large→small?)
5. Return result        (dp[n]? max(dp)?)
```

---

## 1. Climbing Stairs (Easy)

**LeetCode 70. Climbing Stairs** |  DP #1 intro problem

### Problem

Climb n stairs, taking 1 or 2 steps at a time. How many distinct ways?

```
Input: n = 3
Output: 3
Explanation: 1+1+1, 1+2, 2+1
```

### Key Insight

Ways to reach step i = (ways to i-1, then 1 step) + (ways to i-2, then 2 steps).

```python
# Method 1: DP Array
def climbStairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# Method 2: Space-Optimized
def climbStairs(n):
    if n <= 2:
        return n
    a, b = 1, 2
    for i in range(3, n + 1):
        a, b = b, a + b
    return b

# Method 3: Recursion + Memoization (top-down)
def climbStairs(n):
    memo = {1: 1, 2: 2}
    def dfs(i):
        if i in memo:
            return memo[i]
        memo[i] = dfs(i-1) + dfs(i-2)
        return memo[i]
    return dfs(n)
```

---

## 2. Min Cost Climbing Stairs (Easy)

**LeetCode 746. Min Cost Climbing Stairs**

```python
def minCostClimbingStairs(cost):
    n = len(cost)
    dp = [0] * (n + 1)
    for i in range(2, n + 1):
        dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])
    return dp[n]
```

---

## 3. House Robber (Medium)

**LeetCode 198. House Robber** |  High frequency

### Problem

Rob houses along a street; adjacent houses trigger alarms. Maximize loot.

```
Input: nums = [2, 7, 9, 3, 1]
Output: 12
Explanation: rob 2 + 9 + 1 = 12
```

### Recurrence

At house i, choose: rob i (get nums[i] + dp[i-2]) or skip i (get dp[i-1]).

```python
def rob(nums):
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    dp = [0] * len(nums)
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1])
    for i in range(2, len(nums)):
        dp[i] = max(dp[i-1], nums[i] + dp[i-2])
    return dp[-1]
```

---

## 4. Coin Change (Medium)

**LeetCode 322. Coin Change** |  Classic knapsack DP

### Problem

Find the fewest coins to make a given amount. Return -1 if impossible.

```
Input: coins = [1, 2, 5], amount = 11
Output: 3
Explanation: 5 + 5 + 1 = 11
```

### Code

```python
# Method 1: Bottom-Up DP
def coinChange(coins, amount):
    INF = amount + 1
    dp = [INF] * (amount + 1)
    dp[0] = 0
    for i in range(1, amount + 1):
        for coin in coins:
            if i >= coin:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    return dp[amount] if dp[amount] != INF else -1

# Method 2: Top-Down (Recursion + Memo)
def coinChange(coins, amount):
    memo = {0: 0}
    def dfs(rem):
        if rem < 0:
            return float('inf')
        if rem in memo:
            return memo[rem]
        min_coins = float('inf')
        for coin in coins:
            min_coins = min(min_coins, dfs(rem - coin) + 1)
        memo[rem] = min_coins
        return min_coins
    result = dfs(amount)
    return result if result != float('inf') else -1
```

---

## 5. Longest Increasing Subsequence (Medium)

**LeetCode 300. Longest Increasing Subsequence** |  High frequency

### Problem

Find the length of the longest strictly increasing subsequence (not necessarily contiguous).

```
Input: nums = [10, 9, 2, 5, 3, 7, 101, 18]
Output: 4
Explanation: [2, 3, 7, 101]
```

### DP Approach

`dp[i]` = length of LIS ending at `nums[i]`. For each i, check all j < i where nums[j] < nums[i].

```python
def lengthOfLIS(nums):
    dp = [1] * len(nums)
    for i in range(len(nums)):
        for j in range(i):
            if nums[i] > nums[j]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)
```

---

## 6. Unique Paths (Medium)

**LeetCode 62. Unique Paths** |  2D DP intro

### Problem

Robot starts top-left of an m×n grid, can only move right or down. How many paths to bottom-right?

```python
def uniquePaths(m, n):
    dp = [[1] * n for _ in range(m)]
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    return dp[m-1][n-1]
```

---

## 7. Word Break (Medium)

**LeetCode 139. Word Break** | 

### Problem

Can string `s` be segmented into words from a dictionary?

```
Input: s = "leetcode", wordDict = ["leet", "code"]
Output: True
```

```python
def wordBreak(s, wordDict):
    word_set = set(wordDict)
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    return dp[n]
```

---

## 8. Edit Distance (Hard)

**LeetCode 72. Edit Distance** |  Classic 2D DP

### Problem

Minimum number of operations (insert, delete, replace) to convert word1 to word2.

```
Input: word1 = "horse", word2 = "ros"
Output: 3
```

```python
def minDistance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
    return dp[m][n]
```

---

## DP Summary

| Type | Pattern | Examples |
|------|---------|----------|
| Linear DP | 1D array, dp[i] depends on a few before | Climbing Stairs, House Robber |
| Knapsack | Choose or not, capacity constraint | Coin Change |
| 2D DP | Two strings / matrix | Edit Distance, Unique Paths |
| Subsequence | Optimal solution ending at i | LIS |

### DP Recognition Signals

- "How many ways/paths?" → Climbing Stairs, Unique Paths
- "Maximum / Minimum" → House Robber, Coin Change
- "Can / Is it possible?" → Word Break
- "Longest subsequence" → LIS, Edit Distance
