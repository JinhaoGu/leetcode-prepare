# Binary Search

> **Day 8-9** | 5 problems | Difficulty: Easy - Medium
>
> The core of binary search: halve the search space each iteration. The prerequisite is that the data has some form of **monotonicity** (not necessarily a sorted array!).

---

## Table of Contents

1. [Binary Search](#1-binary-search-easy) — Easy
2. [Search in Rotated Sorted Array](#2-search-in-rotated-sorted-array-medium) — Medium
3. [Find Minimum in Rotated Sorted Array](#3-find-minimum-in-rotated-sorted-array-medium) — Medium
4. [Search a 2D Matrix](#4-search-a-2d-matrix-medium) — Medium
5. [Koko Eating Bananas](#5-koko-eating-bananas-medium) — Medium

---

## Generic Binary Search Template

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2  # overflow-safe
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

---

## 1. Binary Search (Easy)

**LeetCode 704. Binary Search**

Directly apply the template above.

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

**LeetCode 33. Search in Rotated Sorted Array** |  High frequency

### Problem

Search a target in a rotated sorted array.

```
Original: [0, 1, 2, 4, 5, 6, 7]
Rotated:  [4, 5, 6, 7, 0, 1, 2]
```

### Approach

**Key observation**: After rotation, at least one half of the array is always sorted.

1. Find mid
2. Determine which half is sorted
3. If target is in the sorted half → search there, else search the other half

```python
def search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid
        if nums[left] <= nums[mid]:  # left half sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:  # right half sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1
```

---

## 3. Find Minimum in Rotated Sorted Array (Medium)

**LeetCode 153. Find Minimum in Rotated Sorted Array** | 

### Problem

Find the minimum element in a rotated sorted array.

```
Input: [3, 4, 5, 1, 2]
Output: 1
```

### Approach

If `nums[mid] > nums[right]`, the rotation point is on the right → minimum is there. Otherwise minimum is on the left (including mid).

```python
def findMin(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] > nums[right]:
            left = mid + 1
        else:
            right = mid
    return nums[left]
```

---

## 4. Search a 2D Matrix (Medium)

**LeetCode 74. Search a 2D Matrix** | 

### Problem

Search in a matrix where each row is sorted and the first element of each row is greater than the last of the previous row.

### Approach

Treat the 2D matrix as a flattened 1D sorted array using coordinate conversion:
- 1D index `mid` → 2D: `row = mid // cols, col = mid % cols`

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

**LeetCode 875. Koko Eating Bananas** |  Classic "binary search the answer"

### Problem

There are n piles of bananas. Koko can eat k bananas per hour from one pile. Guards return after h hours. Find the minimum k to finish all bananas.

```
Input: piles = [3, 6, 7, 11], h = 8
Output: 4
```

### Approach

**"Binary search the answer"**: The answer k has monotonicity — if speed k works, k+1 also works; if k fails, k-1 also fails.

- Search range: [1, max(piles)]
- For each candidate speed, compute total hours needed
- If hours ≤ h → try slower (right = mid)
- If hours > h → must speed up (left = mid + 1)

```python
def minEatingSpeed(piles, h):
    left, right = 1, max(piles)
    while left < right:
        mid = left + (right - left) // 2
        hours = sum((p + mid - 1) // mid for p in piles)  # ceil division
        if hours <= h:
            right = mid
        else:
            left = mid + 1
    return left
```

---

## Binary Search Summary

### Three Variants

| Type | Pattern | Example |
|------|---------|---------|
| Standard | `==` return, `<` go right, `>` go left | Binary Search |
| Rotated array | Determine sorted half first | Search Rotated, Find Min |
| Binary answer | Guess answer, verify feasibility | Koko Eating Bananas |

### Binary Answer Recognition

Look for these keywords:
- "Minimum of maximum" or "Maximum of minimum"
- Answer lies in a continuous range
- Can write a `check(mid) → bool` function

### Template: Binary Answer

```python
def binary_answer():
    left, right = min_possible, max_possible
    while left < right:
        mid = left + (right - left) // 2
        if check(mid):
            right = mid      # try smaller
        else:
            left = mid + 1   # need larger
    return left
```
