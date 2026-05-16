# Recursion & Backtracking

> **Day 14** | 4 problems | Difficulty: Medium
>
> Backtracking = try all possibilities; when you hit a dead end, "backtrack" to the previous step and try a different choice. In essence, it's a **DFS on a multi-way tree**.

---

## Table of Contents

1. [Subsets](#1-subsets-medium) — Medium
2. [Combination Sum](#2-combination-sum-medium) — Medium
3. [Permutations](#3-permutations-medium) — Medium
4. [Word Search](#4-word-search-medium) — Medium

---

## Generic Backtracking Template

```python
def backtrack(path, choices):
    if terminal_condition:
        result.append(path.copy())
        return

    for choice in choices:
        make_choice()
        backtrack(path, new_choices)
        undo_choice()  # ← this is the "backtracking" key!
```

---

## 1. Subsets (Medium)

**LeetCode 78. Subsets** |  Best backtracking intro

### Problem

Return all possible subsets (the power set).

```
Input: nums = [1, 2, 3]
Output: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

### Method 1: Backtracking

Each element has two choices: "include" or "exclude".

```python
def subsets(nums):
    result = []

    def backtrack(start, current):
        result.append(current[:])  # every node is a valid subset
        for i in range(start, len(nums)):
            current.append(nums[i])      # choose nums[i]
            backtrack(i + 1, current)    # consider remaining
            current.pop()                # undo (backtrack)

    backtrack(0, [])
    return result
```

### Method 2: Iterative (Cascading)

```python
def subsets(nums):
    result = [[]]
    for num in nums:
        result += [curr + [num] for curr in result]
    return result
```

---

## 2. Combination Sum (Medium)

**LeetCode 39. Combination Sum** | 

### Problem

Find all unique combinations that sum to `target`. Each number can be used **unlimited times**.

```
Input: candidates = [2, 3, 6, 7], target = 7
Output: [[2, 2, 3], [7]]
```

### Code

```python
def combinationSum(candidates, target):
    result = []

    def backtrack(start, current, remaining):
        if remaining == 0:
            result.append(current[:])
            return
        if remaining < 0:
            return  # prune: exceeded target

        for i in range(start, len(candidates)):
            current.append(candidates[i])
            # start stays at i (not i+1) because we can reuse
            backtrack(i, current, remaining - candidates[i])
            current.pop()

    backtrack(0, [], target)
    return result
```

---

## 3. Permutations (Medium)

**LeetCode 46. Permutations** | 

### Problem

Return all permutations. Order matters!

```
Input: nums = [1, 2, 3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

### Approach

Unlike subsets, order matters and each element is used exactly once → need a `used` array.

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
                continue
            used[i] = True
            current.append(nums[i])
            backtrack(current)
            current.pop()
            used[i] = False

    backtrack([])
    return result
```

### Permutation vs Combination vs Subset

| Problem | Order matters? | Reuse? | start param | used array |
|---------|---------------|--------|-------------|------------|
| Subsets / Combo | No | No | start (i+1) | Not needed |
| Combination Sum | No | Yes | start (i) | Not needed |
| Permutations | Yes | No | Not needed | Needed |

---

## 4. Word Search (Medium)

**LeetCode 79. Word Search** |  2D backtracking

### Problem

Given a letter grid, determine if a word exists (adjacent horizontally/vertically, each cell used once).

### Code

```python
def exist(board, word):
    rows, cols = len(board), len(board[0])

    def dfs(r, c, i):
        if i == len(word):
            return True
        if (r < 0 or r >= rows or c < 0 or c >= cols or
            board[r][c] != word[i]):
            return False

        temp = board[r][c]
        board[r][c] = '#'  # mark visited

        found = (dfs(r+1, c, i+1) or
                 dfs(r-1, c, i+1) or
                 dfs(r, c+1, i+1) or
                 dfs(r, c-1, i+1))

        board[r][c] = temp  # restore (backtrack)
        return found

    for r in range(rows):
        for c in range(cols):
            if board[r][c] == word[0] and dfs(r, c, 0):
                return True
    return False
```

---

## Backtracking Summary

| Problem Type | Key Parameter | Dedup Method |
|-------------|---------------|--------------|
| Subset / Combination | start parameter | `backtrack(i+1)` — don't go back |
| Reusable elements | start = i | `backtrack(i)` — allow reuse |
| Permutations | used array | Skip `used[i] == True` |
| 2D search | coords + directions | Mark in place, then restore |
