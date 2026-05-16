# Recursion & Backtracking (递归与回溯)

> **Day 14** | 4 题 | 难度: Medium
>
> 回溯法 = 尝试所有可能性，遇到死路就"回溯"到上一步换个选择。本质是**多叉树的 DFS**。

---

## 回溯法通用模板

```python
def backtrack(路径, 选择列表):
    if 满足终止条件:
        result.append(路径.copy())
        return

    for 选择 in 选择列表:
        做选择
        backtrack(路径, 新的选择列表)
        撤销选择  # ← 这是"回溯"的关键！
```

---

## 目录

1. [Subsets (子集)](#1-subsets-medium) — Medium
2. [Combination Sum (组合总和)](#2-combination-sum-medium) — Medium
3. [Permutations (全排列)](#3-permutations-medium) — Medium
4. [Word Search (单词搜索)](#4-word-search-medium) — Medium

---

## 1. Subsets (Medium)

**LeetCode 78. Subsets** | 🌟🌟 回溯入门必刷

### 题目

返回数组所有可能的子集（幂集）。

```
输入: nums = [1, 2, 3]
输出: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

### 思路讲解

**每个元素有"选"和"不选"两种选择**。画出决策树：

```
                     []
            /                \
         选1                  不选1
        [1]                   []
      /     \               /     \
    选2    不选2          选2    不选2
  [1,2]    [1]          [2]      []
  /   \    /  \         /  \    /  \
选3 不选3 选3 不选3   选3 不选3 选3 不选3
[1,2,3][1,2][1,3][1] [2,3][2] [3] []
```

### 代码

```python
def subsets(nums):
    result = []

    def backtrack(start, current):
        result.append(current[:])  # 每个节点都是一个有效子集

        for i in range(start, len(nums)):
            current.append(nums[i])     # 选 nums[i]
            backtrack(i + 1, current)   # 继续考虑后面的元素
            current.pop()               # 撤销选择（回溯）

    backtrack(0, [])
    return result
```

### 一步步走

```
backtrack(0, []):
  result = [[]]
  i=0: 选1 → backtrack(1, [1]):
    result = [[], [1]]
    i=1: 选2 → backtrack(2, [1,2]):
      result = [[], [1], [1,2]]
      i=2: 选3 → backtrack(3, [1,2,3]):
        result = [[], [1], [1,2], [1,2,3]]
        返回
      撤销3: [1,2]
    返回
    撤销2: [1]
    i=2: 选3 → backtrack(3, [1,3]):
      result = [[], [1], [1,2], [1,2,3], [1,3]]
      返回
    撤销3: [1]
  返回
  撤销1: []
  i=1: 选2 → backtrack(2, [2]):
    ...
```

### 方法二：迭代法（Cascading）

**思路**：从一个空子集开始，每次把新元素加到已有所有子集中。

```
初始: [[]]

加入1: [[], [1]]
加入2: [[], [1], [2], [1,2]]
加入3: [[], [1], [2], [1,2], [3], [1,3], [2,3], [1,2,3]]
```

```python
def subsets(nums):
    result = [[]]
    for num in nums:
        # 把当前数字加入已有子集中
        result += [curr + [num] for curr in result]
    return result
```

---

### 复杂度

- **时间**: O(n × 2ⁿ)
- **空间**: O(n) — 递归深度 (回溯法) 或 O(2ⁿ) — 输出 (迭代法)

---

## 2. Combination Sum (Medium)

**LeetCode 39. Combination Sum** | 🌟🌟

### 题目

找出数组中所有和为 `target` 的组合，每个数可以**重复使用**。

```
输入: candidates = [2, 3, 6, 7], target = 7
输出: [[2, 2, 3], [7]]
```

### 思路讲解

和 Subsets 类似，但**终止条件**变了：和为 target 时保存，超过 target 时剪枝。

**关键**：因为可以重复用同一个数，递归时 `start` 不 +1（还是 i）。

```
决策树（简化）:

                    []
              /     |    \    \
             2      3     6    7
           / | \    | \    \    ✓=[7]
          2  3  6   3  6    6
         /|   |       |
        2 3   3       ...
       /     ✓=[2,2,3]
      ...
```

### 代码

```python
def combinationSum(candidates, target):
    result = []

    def backtrack(start, current, remaining):
        if remaining == 0:
            result.append(current[:])
            return
        if remaining < 0:
            return  # 剪枝：超过目标值

        for i in range(start, len(candidates)):
            current.append(candidates[i])
            # 注意: start 还是 i（不是 i+1），因为可以重复用
            backtrack(i, current, remaining - candidates[i])
            current.pop()

    backtrack(0, [], target)
    return result
```

### 复杂度

- **时间**: 最坏 O(n^(target/min)) — 组合数
- **空间**: O(target/min) — 递归深度

---

## 3. Permutations (Medium)

**LeetCode 46. Permutations** | 🌟🌟

### 题目

返回数组的所有排列（顺序重要的全排列）。

```
输入: nums = [1, 2, 3]
输出: [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]
```

### 思路讲解

**与 Subsets 的区别**：排列关心顺序！所以不能限制 `start`，但每个元素只能用一次，需要一个 `used` 数组来标记。

```
                     []
          /           |           \
         1            2            3
      /     \      /     \      /     \
     2       3    1       3    1       2
    /         \  /         \  /         \
   3           2 3           1 2          1
[1,2,3]  [1,3,2] [2,1,3] [2,3,1] [3,1,2] [3,2,1]
```

### 代码

```python
def permute(nums):
    result = []
    used = [False] * len(nums)

    def backtrack(current):
        if len(current) == len(nums):
            result.append(current[:])
            return

        for i in range(len(nums)):
            if used[i]:
                continue  # 跳过已使用的元素

            # 做选择
            used[i] = True
            current.append(nums[i])

            backtrack(current)

            # 撤销选择
            current.pop()
            used[i] = False

    backtrack([])
    return result
```

### 排列 vs 组合 vs 子集

| 问题 | 关心顺序？ | 元素可重复？ | start 参数 | used 数组 |
|------|----------|------------|-----------|----------|
| Subsets / 组合 | 不关心 | 不可重复 | start (i+1) | 不需要 |
| Combination Sum | 不关心 | 可重复 | start (i) | 不需要 |
| Permutations | 关心 | 不可重复 | 不需要 | 需要 |

---

## 4. Word Search (Medium)

**LeetCode 79. Word Search** | 🌟🌟 二维回溯

### 题目

给定一个字母网格，判断单词是否存在于网格中（相邻字母上下左右连接，每个格子只能用一次）。

```
board = [
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
word = "ABCCED" → True
word = "ABCB"   → False
```

### 思路讲解

对网格中每个格子进行 DFS 搜索，匹配单词的每个字符。

1. 遍历每个格子作为起点
2. DFS：检查当前格子是否匹配目标字符
3. 匹配后继续在四个方向搜索下一个字符
4. 标记已访问的格子（避免重复使用），搜索完后恢复

```
搜索 "ABCCED":

找到 A(0,0) → 匹配 'A' ✓
  上: 出界
  下: S ≠ B
  左: 出界
  右: B(0,1) → 匹配 'B' ✓
    上: 出界
    下: F ≠ C
    左: A 已访问
    右: C(0,2) → 匹配 'C' ✓
      ...
```

### 代码

```python
def exist(board, word):
    rows, cols = len(board), len(board[0])

    def dfs(r, c, i):
        # 所有字符都匹配完毕
        if i == len(word):
            return True

        # 边界检查 & 字符匹配
        if (r < 0 or r >= rows or
            c < 0 or c >= cols or
            board[r][c] != word[i]):
            return False

        # 标记已访问
        temp = board[r][c]
        board[r][c] = '#'

        # 搜索四个方向
        found = (dfs(r + 1, c, i + 1) or
                 dfs(r - 1, c, i + 1) or
                 dfs(r, c + 1, i + 1) or
                 dfs(r, c - 1, i + 1))

        # 恢复（回溯）
        board[r][c] = temp

        return found

    # 每个格子都可能作为起点
    for r in range(rows):
        for c in range(cols):
            if board[r][c] == word[0] and dfs(r, c, 0):
                return True

    return False
```

### 复杂度

- **时间**: O(m × n × 3^L) — 每个格子是起点，每个方向再探索
- **空间**: O(L) — 递归栈深度 = 单词长度

---

## 回溯法总结

### 刷题套路

回溯题八成都是这个模板的变体：

```python
def solve(problem):
    result = []

    def backtrack(状态参数):
        if 终止条件:
            result.append(当前状态.copy())
            return

        for 可选项 in 可选列表:
            if 剪枝条件:
                continue
            做选择
            backtrack(新状态)
            撤销选择

    backtrack(初始状态)
    return result
```

### 回溯 vs 动态规划

| 特征 | 回溯 | 动态规划 |
|------|------|---------|
| 求解目标 | 所有解 | 最优解/计数 |
| 状态 | 路径 | 子问题结果 |
| 剪枝 | 可以 | 天然（无后效性） |
| 典型题 | Subsets, Permutations | Climbing Stairs, Coin Change |

### 常见题型速查

| 题型 | 关键参数 | 去重方式 |
|------|---------|---------|
| 子集/组合 | start 参数 | `backtrack(i+1)` 不回头选 |
| 可重复选择 | start=i | `backtrack(i)` 允许重复 |
| 排列 | used 数组 | 跳过 `used[i]==True` |
| 二维搜索 | 坐标+方向 | 原地标记再恢复 |
