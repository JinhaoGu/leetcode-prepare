# Two Pointers

> **Day 3-4** | 5 problems | Difficulty: Easy - Hard
>
> The core idea: use two pointers moving on an array to avoid nested loops, reducing O(n²) to O(n).

---

## Table of Contents

1. [Valid Palindrome](#1-valid-palindrome-easy) — Easy
2. [Two Sum II](#2-two-sum-ii-medium) — Medium
3. [3Sum](#3-3sum-medium) — Medium
4. [Container With Most Water](#4-container-with-most-water-medium) — Medium
5. [Trapping Rain Water](#5-trapping-rain-water-hard) — Hard

---

## Three Common Two-Pointer Patterns

```
1. Opposite-direction pointers (converging)
   [2, 7, 11, 15]
    L →      ← R
   Used for: sorted array two-sum, palindrome, water container

2. Fast-slow pointers (same direction)
   1 → 2 → 3 → 4 → 5
   S     S     S
   F          F          F
   Used for: cycle detection, dedup

3. Center-expanding pointers
   ← L  center  R →
   Used for: longest palindromic substring (center expansion)
```

---

## 1. Valid Palindrome (Easy)

**LeetCode 125. Valid Palindrome** |  Fundamental

### Problem

Determine if a string is a palindrome. Consider only alphanumeric characters; ignore case.

```
Input: "A man, a plan, a canal: Panama"
Output: True
```

### Approach

Use left and right pointers:
1. Skip non-alphanumeric characters
2. Compare characters (case-insensitive)
3. If different → not a palindrome
4. If pointers meet → it's a palindrome

### Code

```python
def isPalindrome(s):
    left, right = 0, len(s) - 1

    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1

        if s[left].lower() != s[right].lower():
            return False

        left += 1
        right -= 1

    return True
```

- **Time**: O(n)
- **Space**: O(1)

---

## 2. Two Sum II (Medium)

**LeetCode 167. Two Sum II - Input Array Is Sorted** |  Classic two-pointer intro

### Problem

Given a **sorted** array, find two numbers that sum to target (1-indexed).

```
Input: numbers = [2, 7, 11, 15], target = 9
Output: [1, 2]
```

### Approach

Because the array is sorted, use left and right pointers:
- If `nums[L] + nums[R] > target` → too large, move R left (decrease sum)
- If sum < target → too small, move L right (increase sum)

```
[2, 7, 11, 15], target=9
 L         R
2+15=17>9 → move R left

[2, 7, 11, 15]
 L      R
2+11=13>9 → move R left

[2, 7, 11, 15]
 L  R
2+7=9=target → found! [1, 2]
```

### Code

```python
def twoSum(numbers, target):
    left, right = 0, len(numbers) - 1
    while left < right:
        cur_sum = numbers[left] + numbers[right]
        if cur_sum == target:
            return [left + 1, right + 1]
        elif cur_sum < target:
            left += 1
        else:
            right -= 1
```

---

## 3. 3Sum (Medium)

**LeetCode 15. 3Sum** |  Must master — extremely high frequency

### Problem

Find all unique triplets that sum to 0.

```
Input: nums = [-1, 0, 1, 2, -1, -4]
Output: [[-1, -1, 2], [-1, 0, 1]]
```

### Method 1: Sort + Two Pointers ⭐ Recommended

**Core insight: convert 3Sum to 2Sum!**

1. Sort the array
2. Fix one number `nums[i]`, then use two pointers on `[i+1, end]` to find two numbers summing to `-nums[i]`
3. Key challenge: **deduplication**

```python
def threeSum(nums):
    nums.sort()
    result = []
    n = len(nums)

    for i in range(n - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue  # skip duplicate fixed numbers
        if nums[i] + nums[i+1] + nums[i+2] > 0:
            break
        if nums[i] + nums[n-2] + nums[n-1] < 0:
            continue

        left, right = i + 1, n - 1
        target = -nums[i]
        while left < right:
            cur_sum = nums[left] + nums[right]
            if cur_sum == target:
                result.append([nums[i], nums[left], nums[right]])
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
            elif cur_sum < target:
                left += 1
            else:
                right -= 1
    return result
```

### Method 2: Sort + HashSet

```python
def threeSum(nums):
    nums.sort()
    result = set()

    for i, a in enumerate(nums):
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        seen = set()
        for j in range(i + 1, len(nums)):
            b = nums[j]
            complement = -(a + b)
            if complement in seen:
                result.add((a, b, complement))
            seen.add(b)

    return [list(triplet) for triplet in result]
```

- Method 1: Time O(n²), Space O(1)
- Method 2: Time O(n²), Space O(n)

---

## 4. Container With Most Water (Medium)

**LeetCode 11. Container With Most Water** |  Clever greedy idea

### Problem

Given an array of wall heights, find two walls that hold the most water.

```
Input: height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
Output: 49
Explanation: wall at index=1 (h=8) and index=8 (h=7), width=7, water=7×min(8,7)=49
```

### Approach

**Formula**: `water = width(right-left) × min(height[left], height[right])` (short board effect)

Start from the widest pair. Always move the **shorter** side inward.
- Moving the taller side can never increase area (width decreases + short board unchanged or shorter)
- Moving the shorter side may encounter a taller board → potentially larger area

### Code

```python
def maxArea(height):
    left, right = 0, len(height) - 1
    max_water = 0
    while left < right:
        h = min(height[left], height[right])
        w = right - left
        max_water = max(max_water, h * w)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return max_water
```

---

## 5. Trapping Rain Water (Hard)

**LeetCode 42. Trapping Rain Water** |  Classic hard problem — a must for top companies

### Problem

Given wall heights, calculate how much rainwater can be trapped.

```
Input: height = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]
Output: 6
```

### Approach

**Core question**: How much water can position i hold?

→ `water[i] = min(left_max, right_max) - height[i]` (0 if negative)

### Method 1: Two Arrays (easiest to understand)

```python
def trap(height):
    n = len(height)
    if n == 0:
        return 0

    left_max = [0] * n
    left_max[0] = height[0]
    for i in range(1, n):
        left_max[i] = max(left_max[i-1], height[i])

    right_max = [0] * n
    right_max[n-1] = height[n-1]
    for i in range(n-2, -1, -1):
        right_max[i] = max(right_max[i+1], height[i])

    water = 0
    for i in range(n):
        water += min(left_max[i], right_max[i]) - height[i]
    return water
```

### Method 2: Two Pointers — O(1) space

```python
def trap(height):
    left, right = 0, len(height) - 1
    left_max, right_max = 0, 0
    water = 0

    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    return water
```

- Method 1: Time O(n), Space O(n)
- Method 2: Time O(n), Space O(1)

---

## Two Pointers Summary

| Pattern | Use Case | Example Problems |
|---------|----------|------------------|
| Opposite-direction | Sorted arrays, palindrome | Valid Palindrome, Two Sum II, 3Sum |
| Greedy two-pointer | Optimal area/volume | Container With Most Water, Trapping Rain Water |
| Fast-slow | Linked list problems | Linked List Cycle (see Linked List chapter) |

### When to think of two pointers?

- Array is **sorted** → almost always applicable
- Finding a **pair** of elements → consider sorting + two pointers
- O(n²) → O(n) optimization needed → try two pointers
