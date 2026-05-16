# Binary Search (二分查找)

> **Day 8-9** | 5 题 | 难度: Easy - Medium
>
> 二分查找的核心：每次把搜索范围缩小一半。前提是**数据有某种单调性**（不一定是有序数组！）。

---

## 目录

1. [Binary Search (二分查找)](#1-binary-search-easy) — Easy
2. [Search in Rotated Sorted Array (搜索旋转排序数组)](#2-search-in-rotated-sorted-array-medium) — Medium
3. [Find Minimum in Rotated Sorted Array (寻找旋转排序数组中的最小值)](#3-find-minimum-in-rotated-sorted-array-medium) — Medium
4. [Search a 2D Matrix (搜索二维矩阵)](#4-search-a-2d-matrix-medium) — Medium
5. [Koko Eating Bananas (爱吃香蕉的珂珂)](#5-koko-eating-bananas-medium) — Medium

---

## 二分查找通用模板

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1

    while left <= right:         # 注意 <=
        mid = left + (right - left) // 2  # 防溢出写法
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1  # 没找到
```

**关键细节**：
- `left <= right`：当区间为空时退出。写成 `<` 会漏掉单元素的情况。
- `mid = left + (right - left) // 2`：等同 `(left+right)//2`，但防止整数溢出。
- `left = mid + 1` / `right = mid - 1`：排除 mid，否则可能死循环。

---

## 1. Binary Search (Easy)

**LeetCode 704. Binary Search**

直接套用上面的模板即可。

```python
def search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

---

## 2. Search in Rotated Sorted Array (Medium)

**LeetCode 33. Search in Rotated Sorted Array** | 🌟🌟🌟 高频

### 题目

在一个**旋转过的有序数组**中搜索 target。

```
原数组: [0, 1, 2, 4, 5, 6, 7]
旋转后: [4, 5, 6, 7, 0, 1, 2]
        ←前半递增→ ←后半递增→
```

### 思路讲解

**关键观察**：旋转后的数组**从中间切开，至少有一半是有序的**。

所以策略是：
1. 找到 mid
2. 判断**哪一半是有序的**（左侧还是右侧）
3. 判断 target 是否在有序那一半的范围内
4. 如果 target 在有序的一半 → 去那半找
5. 如果不在 → 去另一半找

```
[4, 5, 6, 7, 0, 1, 2], target=0

mid=3, nums[mid]=7
左半[4,5,6,7]有序, 右半[0,1,2]无序(旋转点)
target=0 不在 [4,7] 范围 → 去右半找

mid=5, nums[mid]=1
左半[0]有序, 右半[2]有序
target=0 在 [0,1] 范围 → 去左半找

mid=4, nums[mid]=0 → 找到了!
```

### 代码

```python
def search(nums, target):
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid

        # 判断哪半有序
        if nums[left] <= nums[mid]:  # 左半有序
            if nums[left] <= target < nums[mid]:
                right = mid - 1  # target 在左半
            else:
                left = mid + 1   # target 在右半
        else:  # 右半有序
            if nums[mid] < target <= nums[right]:
                left = mid + 1   # target 在右半
            else:
                right = mid - 1  # target 在左半

    return -1
```

**关键判断**：`nums[left] <= nums[mid]` 表示左半有序。
注意用 `<=`（mid 可能等于 left）。

---

## 3. Find Minimum in Rotated Sorted Array (Medium)

**LeetCode 153. Find Minimum in Rotated Sorted Array** | 🌟🌟

### 题目

在旋转有序数组中找最小值。

```
输入: [3, 4, 5, 1, 2]
输出: 1
```

### 思路讲解

**关键**：最小值一定在"无序"的那一半。

如果 `nums[mid] > nums[right]`，说明 mid 在旋转点左边（大值区），最小值在右侧。
否则，mid 已经在旋转点右边（小值区）或在正常有序区间，最小值在左侧（包含 mid）。

```
[3, 4, 5, 1, 2]
mid=2, nums[mid]=5 > nums[right]=2
→ 右侧无序，最小值在右: left=mid+1

[1, 2]
mid=0, nums[mid]=1 < nums[right]=2
→ 正常有序，最小值在左: right=mid

[1] → left=right → 返回1
```

### 代码

```python
def findMin(nums):
    left, right = 0, len(nums) - 1

    while left < right:  # 注意是 < 不是 <=
        mid = left + (right - left) // 2
        if nums[mid] > nums[right]:
            # 旋转点在右侧，最小值在右半
            left = mid + 1
        else:
            # 旋转点在左侧或已正常，最小值在左半（含mid）
            right = mid

    return nums[left]
```

**为什么用 `left < right` 而非 `<=`？**
因为当 `left == right` 时我们就找到了最小值，不需要额外检查。

---

## 4. Search a 2D Matrix (Medium)

**LeetCode 74. Search a 2D Matrix** | 🌟🌟

### 题目

在一个每行有序、且下一行所有元素都大于上一行的矩阵中搜索 target。

```
输入:
matrix = [
  [1,  3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 60]
], target = 3
输出: True
```

### 思路讲解

**关键洞察**：如果把矩阵按行展开，就是一个完全有序的一维数组。

```
[1, 3, 5, 7, 10, 11, 16, 20, 23, 30, 34, 60]
```

不用真展开，只需要用坐标转换：
- 一维索引 `mid` → 二维坐标：`row = mid // cols, col = mid % cols`

然后直接做标准的二分查找。

```
matrix 是 3×4 (rows=3, cols=4)
mid = 5
row = 5 // 4 = 1
col = 5 % 4 = 1
→ matrix[1][1] = 11
```

### 代码

```python
def searchMatrix(matrix, target):
    if not matrix or not matrix[0]:
        return False

    rows, cols = len(matrix), len(matrix[0])
    left, right = 0, rows * cols - 1

    while left <= right:
        mid = left + (right - left) // 2
        mid_val = matrix[mid // cols][mid % cols]

        if mid_val == target:
            return True
        elif mid_val < target:
            left = mid + 1
        else:
            right = mid - 1

    return False
```

---

## 5. Koko Eating Bananas (Medium)

**LeetCode 875. Koko Eating Bananas** | 🌟🌟 二分答案

### 题目

有 n 堆香蕉，每堆有 `piles[i]` 根。Koko 每小时可以选择一堆，吃掉 k 根。如果一堆少于 k 根，她吃完这堆后这小时不会吃其他堆。警卫 h 小时后回来。求 Koko 能在 h 小时内吃完所有香蕉的**最小速度 k**。

```
输入: piles = [3, 6, 7, 11], h = 8
输出: 4
解释:
  速度4: 3/4=1h, 6/4=2h, 7/4=2h, 11/4=3h → 1+2+2+3=8h ✓
  速度3: 3/3=1h, 6/3=2h, 7/3=3h, 11/3=4h → 1+2+3+4=10h > 8 ✗
```

### 思路讲解

这是 **"二分答案"** 题型：答案（速度 k）具有单调性。

**单调性**：如果速度 k 能在 h 小时内吃完，那 k+1 也能。如果 k 不行，k-1 也不行。

所以可以用二分查找答案的范围：
- 最小速度：1
- 最大速度：max(piles)（再大没意义，每堆最多一小时）

对每个候选速度，计算需要的小时数，判断是否 ≤ h。

```
piles=[3,6,7,11], h=8

left=1, right=11
mid=6: 时间=⌈3/6⌉+⌈6/6⌉+⌈7/6⌉+⌈11/6⌉=1+1+2+2=6h ≤ 8 ✓ → 可以更慢, right=6
mid=3: 时间=1+2+3+4=10h > 8 ✗ → 太慢了, left=4
mid=5: 时间=1+2+2+3=8h ≤ 8 ✓ → right=5
mid=4: 时间=1+2+2+3=8h ≤ 8 ✓ → right=4
left=right=4 → 答案=4
```

### 代码

```python
import math

def minEatingSpeed(piles, h):
    left, right = 1, max(piles)

    while left < right:
        mid = left + (right - left) // 2
        hours = sum(math.ceil(p / mid) for p in piles)

        if hours <= h:
            right = mid  # 可以更慢，但保留 mid
        else:
            left = mid + 1  # 太慢了，加速

    return left
```

**计算时间技巧**：`(p + mid - 1) // mid` 等价于 `ceil(p / mid)`，不用 math.ceil。

---

## 二分查找总结

### 三种变体

| 类型 | 条件模式 | 示例 |
|------|---------|------|
| 标准查找 | `==` 返回，`<` 去右，`>` 去左 | Binary Search |
| 旋转数组 | 判断哪半有序再缩小范围 | Search Rotated, Find Min |
| 二分答案 | 猜答案，验证是否可行，缩小范围 | Koko Eating Bananas |

### 二分答案的识别信号

看到以下关键词，思考二分答案：
- "最小值的最大化" 或 "最大值的最小化"
- 答案在一个连续的范围内
- 能写一个 `验证(mid) → bool` 的函数来判断答案是否可行

### 模板：二分答案

```python
def binary_answer(search_space):
    left, right = min_possible, max_possible
    while left < right:
        mid = left + (right - left) // 2
        if check(mid):  # mid 可行
            right = mid      # 尝试找更小的可行解
        else:
            left = mid + 1   # mid 不可行，找更大的
    return left
```
