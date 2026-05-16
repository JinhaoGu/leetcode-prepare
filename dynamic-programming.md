# Dynamic Programming (动态规划)

> **Day 15-17** | 8 题 | 难度: Easy - Hard
>
> 动态规划（DP）是面试中最难也最重要的算法。核心思想：**把大问题拆成小问题，记住小问题的答案避免重复计算。**

---

## 什么是动态规划？

用一个最简单的例子理解：

### 斐波那契数列

```
F(0)=0, F(1)=1, F(n)=F(n-1)+F(n-2)

暴力递归:
F(5) = F(4) + F(3)
     = [F(3)+F(2)] + [F(2)+F(1)]
     = [F(2)+F(1)+F(2)] + [F(2)+F(1)]
     = ...
F(2) 被重复计算了3次！→ 浪费！

DP 解法：用数组记住已经算过的结果
dp[0]=0, dp[1]=1
dp[2]=dp[1]+dp[0]=1
dp[3]=dp[2]+dp[1]=2
dp[4]=dp[3]+dp[2]=3
dp[5]=dp[4]+dp[3]=5
每个只算一次！
```

**DP 的两个核心要素**：
1. **状态定义**：`dp[i]` 代表什么？
2. **状态转移方程**：`dp[i]` 如何从之前的 `dp` 推导？

---

## 目录

1. [Climbing Stairs (爬楼梯)](#1-climbing-stairs-easy) — Easy
2. [Min Cost Climbing Stairs (使用最小花费爬楼梯)](#2-min-cost-climbing-stairs-easy) — Easy
3. [House Robber (打家劫舍)](#3-house-robber-medium) — Medium
4. [Coin Change (零钱兑换)](#4-coin-change-medium) — Medium
5. [Longest Increasing Subsequence (最长递增子序列)](#5-longest-increasing-subsequence-medium) — Medium
6. [Unique Paths (不同路径)](#6-unique-paths-medium) — Medium
7. [Word Break (单词拆分)](#7-word-break-medium) — Medium
8. [Edit Distance (编辑距离)](#8-edit-distance-hard) — Hard

---

## 1. Climbing Stairs (Easy)

**LeetCode 70. Climbing Stairs** | 🌟 DP 入门第一题

### 题目

爬楼梯，每次可以爬 1 或 2 阶。爬到第 n 阶有多少种爬法？

```
输入: n = 3
输出: 3
解释: 1+1+1, 1+2, 2+1 三种方法
```

### 思路讲解

**关键洞察**：到达第 i 阶的方法 = 从 i-1 走 1 步上来 + 从 i-2 走 2 步上来。

```
dp[i] = 到达第 i 阶的方法数
dp[1] = 1  (只有1步)
dp[2] = 2  (1+1 或 2)
dp[3] = dp[2] + dp[1] = 2 + 1 = 3
dp[4] = dp[3] + dp[2] = 3 + 2 = 5
...
这不就是斐波那契！
```

### 代码

```python
# 方法一：标准 DP 数组
def climbStairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# 方法二：空间优化（只需记两个数）
def climbStairs(n):
    if n <= 2:
        return n
    a, b = 1, 2  # a=dp[i-2], b=dp[i-1]
    for i in range(3, n + 1):
        a, b = b, a + b  # b 是新 dp[i], a 变成旧 b
    return b
```

### 复杂度

- **时间**: O(n)
- **空间**: O(1)（优化后）

---

## 2. Min Cost Climbing Stairs (Easy)

**LeetCode 746. Min Cost Climbing Stairs**

### 题目

每个台阶有一定花费，可以从第 0 或第 1 阶开始，每次爬 1 或 2 阶，求到顶的最小花费。

```
输入: cost = [10, 15, 20]
输出: 15
解释: 从第1阶开始(付15)，爬2阶直接到顶，总花费15
```

### 思路讲解

`dp[i]` = 到达第 i 阶的最小花费

到达 i 的方式：
- 从 i-1 来：`dp[i-1] + cost[i-1]`
- 从 i-2 来：`dp[i-2] + cost[i-2]`
- 取较小值

**注意**：离开第 i 阶时付 `cost[i]`。到达楼顶在第 n 阶之外（不需要付 cost[n]）。

```
cost = [10, 15, 20], n=3

dp[0] = 0  (起点，还没付)
dp[1] = 0  (起点，还没付)
dp[2] = min(dp[1]+15, dp[0]+10) = min(15, 10) = 10
dp[3] = min(dp[2]+20, dp[1]+15) = min(30, 15) = 15  ← 到达楼顶

答案 = 15
```

### 代码

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

**LeetCode 198. House Robber** | 🌟🌟🌟 高频

### 题目

沿街房屋，相邻房屋有警报不能同时偷，求最大偷窃金额。

```
输入: nums = [2, 7, 9, 3, 1]
输出: 12
解释: 偷 2 + 9 + 1 = 12
```

### 思路讲解

到第 i 家时，有两种选择：
- **偷 i**：得到 `nums[i] + dp[i-2]`（不能偷 i-1）
- **不偷 i**：得到 `dp[i-1]`
- 取最大值

```
dp[i] = max(dp[i-1], nums[i] + dp[i-2])

nums = [2, 7, 9, 3, 1]

dp[0] = 2
dp[1] = max(2, 7) = 7
dp[2] = max(7, 9+2) = 11
dp[3] = max(11, 3+7) = 11
dp[4] = max(11, 1+11) = 12

答案 = 12
```

### 代码

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

# 空间优化版
def rob(nums):
    prev2, prev1 = 0, 0  # dp[i-2], dp[i-1]
    for num in nums:
        prev2, prev1 = prev1, max(prev1, num + prev2)
    return prev1
```

---

## 4. Coin Change (Medium)

**LeetCode 322. Coin Change** | 🌟🌟🌟 经典 DP

### 题目

给定不同面额硬币和一个总额，用最少的硬币凑出总额。如果凑不出，返回 -1。

```
输入: coins = [1, 2, 5], amount = 11
输出: 3
解释: 5 + 5 + 1 = 11 (3枚)
```

### 思路讲解

**这是完全背包问题**。

`dp[i]` = 凑出金额 i 所需的最少硬币数

对于每个金额 i，尝试用每一种硬币：
```
dp[i] = min(dp[i], dp[i - coin] + 1)
```

**初始化**：`dp[0]=0`，其他为 `inf`（表示还没算出）。

```
coins = [1, 2, 5], amount = 11

dp[0] = 0
dp[1] = min(dp[1], dp[0]+1) = 1           (用1)
dp[2] = min(dp[2], dp[1]+1, dp[0]+1) = 1  (用2)
dp[3] = min(dp[3], dp[2]+1) = 2           (用1+2)
...
dp[11]: 考虑用1→dp[10]+1, 用2→dp[9]+1, 用5→dp[6]+1
       dp[11] = min(11, 5, 3) = 3        (5+5+1)
```

### 代码

```python
def coinChange(coins, amount):
    INF = amount + 1  # 用 amount+1 代替无穷大
    dp = [INF] * (amount + 1)
    dp[0] = 0

    for i in range(1, amount + 1):
        for coin in coins:
            if i >= coin:
                dp[i] = min(dp[i], dp[i - coin] + 1)

    return dp[amount] if dp[amount] != INF else -1
```

### 复杂度

- **时间**: O(n × k)，n = amount, k = 硬币种类
- **空间**: O(n)

---

## 5. Longest Increasing Subsequence (Medium)

**LeetCode 300. Longest Increasing Subsequence** | 🌟🌟🌟 高频

### 题目

找出未排序数组中最长递增子序列的长度（子序列不要求连续）。

```
输入: nums = [10, 9, 2, 5, 3, 7, 101, 18]
输出: 4
解释: [2, 3, 7, 101] 或 [2, 5, 7, 101]
```

### 思路讲解

**DP 法**：`dp[i]` = 以 `nums[i]` **结尾**的最长递增子序列长度。

对于每个 i，检查它前面所有比它小的数 j：
```
if nums[i] > nums[j]:
    dp[i] = max(dp[i], dp[j] + 1)
```

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]
        ↓   ↓  ↓  ↓  ↓  ↓   ↓   ↓
dp   = [1,  1, 1, 2, 2, 3,  4,  4]

以 nums[6]=101 为例: 检查前面所有比101小的
  j=0(10): dp[6]=max(1, dp[0]+1)=2
  j=2(2):  dp[6]=max(2, dp[2]+1)=2
  j=3(5):  dp[6]=max(2, dp[3]+1)=3
  j=5(7):  dp[6]=max(3, dp[5]+1)=4
  → dp[6]=4
```

### 代码

```python
def lengthOfLIS(nums):
    dp = [1] * len(nums)  # 每个元素自身是长度为1的子序列

    for i in range(len(nums)):
        for j in range(i):
            if nums[i] > nums[j]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)  # 注意返回的是 dp 中的最大值
```

### 复杂度

- **时间**: O(n²)
- **空间**: O(n)

**进阶**：可以用二分 + 贪心优化到 O(n log n)，但面试中先掌握 O(n²) 的 DP 思路。

---

## 6. Unique Paths (Medium)

**LeetCode 62. Unique Paths** | 🌟🌟 二维 DP 入门

### 题目

机器人从 m×n 网格的左上角走到右下角，只能向右或向下走，有多少条路径？

```
输入: m = 3, n = 7
输出: 28
```

### 思路讲解

`dp[i][j]` = 走到格子 (i, j) 的路径数。

到达 (i, j) 只能从上方 (i-1, j) 或左方 (i, j-1) 来：
```
dp[i][j] = dp[i-1][j] + dp[i][j-1]
```

初始化：第一行和第一列只有 1 种走法。

```
dp 表格 (3×7):
[1, 1, 1, 1,  1,  1,  1]
[1, 2, 3, 4,  5,  6,  7]
[1, 3, 6, 10, 15, 21, 28]  ← dp[2][6]=28
```

### 代码

```python
def uniquePaths(m, n):
    dp = [[1] * n for _ in range(m)]

    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]

    return dp[m-1][n-1]

# 空间优化：只用一行
def uniquePaths(m, n):
    dp = [1] * n
    for i in range(1, m):
        for j in range(1, n):
            dp[j] = dp[j] + dp[j-1]
    return dp[-1]
```

---

## 7. Word Break (Medium)

**LeetCode 139. Word Break** | 🌟🌟

### 题目

给定字符串 s 和单词字典，判断 s 能否拆分成字典中的单词。

```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: True
```

### 思路讲解

`dp[i]` = s 的前 i 个字符能否被拆分。

对于每个位置 i，检查是否存在 j < i 使得：
- `s[0:j]` 可以拆分（dp[j] = True）
- `s[j:i]` 在字典中

```
s = "leetcode", wordDict = {"leet", "code"}

dp[0] = True  (空字符串)

i=4: j=0, s[0:4]="leet" 在字典中, dp[0]=True → dp[4]=True
i=8: j=4, s[4:8]="code" 在字典中, dp[4]=True → dp[8]=True

答案: dp[8]=True
```

### 代码

```python
def wordBreak(s, wordDict):
    word_set = set(wordDict)
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True  # 空字符串

    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break  # 只要找到一个就行

    return dp[n]
```

### 复杂度

- **时间**: O(n²) — 两重循环
- **空间**: O(n)

---

## 8. Edit Distance (Hard)

**LeetCode 72. Edit Distance** | 🌟🌟🌟 经典二维 DP

### 题目

将 word1 转换为 word2 所需的最少操作数。操作：插入、删除、替换。

```
输入: word1 = "horse", word2 = "ros"
输出: 3
解释: horse → rorse (换h) → rose (删r) → ros (删e)
```

### 思路讲解

`dp[i][j]` = 将 word1 的前 i 个字符转换为 word2 的前 j 个字符的最小操作数。

**转移方程**：
- 如果 `word1[i-1] == word2[j-1]`：`dp[i][j] = dp[i-1][j-1]`（不用操作）
- 否则，取三种操作的最小值：
  - 替换：`dp[i-1][j-1] + 1`
  - 删除：`dp[i-1][j] + 1`（删 word1 的字符）
  - 插入：`dp[i][j-1] + 1`（在 word1 中插入 word2 的字符）

```
dp 表格 (word1="horse", word2="ros"):
      ""  r  o  s
  ""   0  1  2  3
  h    1  1  2  3
  o    2  2  1  2
  r    3  2  2  2
  s    4  3  3  2
  e    5  4  4  3  ← dp[5][3] = 3
```

### 代码

```python
def minDistance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    # 初始化
    for i in range(m + 1):
        dp[i][0] = i  # 删除 i 个字符
    for j in range(n + 1):
        dp[0][j] = j  # 插入 j 个字符

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]  # 字符相同，不需要操作
            else:
                dp[i][j] = min(
                    dp[i-1][j] + 1,      # 删除
                    dp[i][j-1] + 1,      # 插入
                    dp[i-1][j-1] + 1     # 替换
                )

    return dp[m][n]
```

### 一步步理解 Edit Distance

```
word1 = "horse", word2 = "ros"

初始化:
dp[0][j] = j: 空字符串变成 "ros" 需要 j 次插入
dp[i][0] = i: "horse" 变成空需要 i 次删除

dp[1][1]: h vs r
  不同 → min(替换:1, 删除:2, 插入:2) = 1
  意思是 "h"→"r" 需要1步（替换）

dp[2][2]: ho vs ro
  o vs o → 相同! dp[1][1] = 1
  意思是 "ho"→"ro" 只需把h换成r（1步）

dp[3][3]: hor vs ros
  r vs s → 不同 → min(替换:dp[2][2]+1=2, 删除:dp[2][3]+1=3, 插入:dp[3][2]+1=3) = 2
  意思是 "hor"→"ros" 需要2步
```

### 复杂度

- **时间**: O(m × n)
- **空间**: O(m × n)

---

## DP 总结

### 解题套路（五步法）

```
1. 定义 dp 含义 (dp[i] 或 dp[i][j] 代表什么？)
2. 写出状态转移方程 (如何从已知推未知？)
3. 初始化 (dp[0] 或 dp[i][0] 等)
4. 确定遍历顺序 (从小到大？从大到小？)
5. 返回结果 (dp[n]? max(dp)?)
```

### 题目分类速查

| 类型 | 特征 | 代表题 |
|------|------|--------|
| 线性 DP | 一维数组，dp[i] 依赖前面几个 | Climbing Stairs, House Robber |
| 背包问题 | 选或不选，有容量限制 | Coin Change |
| 二维 DP | 两字符串/矩阵 | Edit Distance, Unique Paths |
| 子序列 | 以 i 结尾的最优解 | LIS |
| 区间 DP | dp[i][j] = 区间的最优解 | (进阶) |

### DP 题目识别信号

- "有多少种方法/路径" → Climbing Stairs, Unique Paths
- "最大/最小" → House Robber, Coin Change
- "能否/是否" → Word Break
- "最长子序列" → LIS, Edit Distance

### 什么时候不是 DP？

- 求所有具体方案（不是数个数）→ 回溯
- 数据是树/图结构 → DFS/BFS
- 没有重叠子问题 → 贪心可能更优
