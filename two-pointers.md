# Two Pointers (双指针)

> **Day 3-4** | 5 题 | 难度: Easy - Hard
>
> 双指针的核心：用两个指针在数组上移动，避免嵌套循环，把 O(n²) 降到 O(n)。

---

## 目录

1. [Valid Palindrome (验证回文串)](#1-valid-palindrome-easy) — Easy
2. [Two Sum II (两数之和 II)](#2-two-sum-ii-medium) — Medium
3. [3Sum (三数之和)](#3-3sum-medium) — Medium
4. [Container With Most Water (盛最多水的容器)](#4-container-with-most-water-medium) — Medium
5. [Trapping Rain Water (接雨水)](#5-trapping-rain-water-hard) — Hard

---

## 双指针的三种常见模式

```
1. 左右指针（相向而行）
   [2, 7, 11, 15]
    L →     ← R
   用于: 有序数组的两数之和、回文、盛水

2. 快慢指针（同向而行）
   1 → 2 → 3 → 4 → 5
   S     S     S
   F          F          F
   用于: 链表环检测、去重

3. 前后指针（中间扩展）
   ← L 中心 R →
   用于: 最长回文子串（中心扩展法）
```

---

## 1. Valid Palindrome (Easy)

**LeetCode 125. Valid Palindrome** | 🌟 基础必刷

### 题目

判断一个字符串是否是回文串。只考虑字母和数字字符，忽略大小写。

```
输入: "A man, a plan, a canal: Panama"
输出: True
解释: 去除非字母数字后为 "amanaplanacanalpanama"，正反读一样
```

### 思路讲解

用左右双指针，一个从左边出发，一个从右边出发：
1. 跳过非字母数字字符
2. 比较两个字符是否相同（忽略大小写）
3. 如果不同 → 不是回文
4. 如果指针相遇 → 是回文

```
"A man, a plan, a canal: Panama"
 ↑                             ↑
 L                             R

跳过非字母: L→'a', R→'a' → 相同，继续
L→'m', R→'m' → 相同，继续
...
最后 L >= R → 是回文
```

### 代码

```python
def isPalindrome(s):
    left, right = 0, len(s) - 1

    while left < right:
        # 跳过左边的非字母数字
        while left < right and not s[left].isalnum():
            left += 1
        # 跳过右边的非字母数字
        while left < right and not s[right].isalnum():
            right -= 1

        # 比较
        if s[left].lower() != s[right].lower():
            return False

        left += 1
        right -= 1

    return True
```

### 复杂度

- **时间**: O(n)
- **空间**: O(1)

### 易错点

- 跳过非字母数字时也要判断 `left < right`，否则可能越界
- `isalnum()` 判断是否是字母或数字；`lower()` 转小写

---

## 2. Two Sum II (Medium)

**LeetCode 167. Two Sum II - Input Array Is Sorted** | 🌟 双指针经典入门

### 题目

给定一个**已排序**的数组，找出两个数和为目标值的下标（下标从 1 开始）。

```
输入: numbers = [2, 7, 11, 15], target = 9
输出: [1, 2]
```

### 思路讲解

因为数组已经排好序，我们可以用左右指针：

- 如果 `nums[L] + nums[R] > target` → 大了，R 左移（让和变小）
- 如果 `nums[L] + nums[R] < target` → 小了，L 右移（让和变大）
- 如果相等 → 找到！

```
[2, 7, 11, 15], target=9
 ↑         ↑
 L         R
2+15=17 > 9 → 大了，R 左移

[2, 7, 11, 15]
 ↑      ↑
 L      R
2+11=13 > 9 → 大了，R 左移

[2, 7, 11, 15]
 ↑  ↑
 L  R
2+7=9 = target → 找到! [1, 2]
```

**为什么正确？** 对于排序数组，当前和太大只能通过减小大数（R左移），太小只能通过增大小数（L右移）。

### 代码

```python
def twoSum(numbers, target):
    left, right = 0, len(numbers) - 1

    while left < right:
        cur_sum = numbers[left] + numbers[right]
        if cur_sum == target:
            return [left + 1, right + 1]  # 下标从1开始
        elif cur_sum < target:
            left += 1   # 和太小，增大小的数
        else:
            right -= 1  # 和太大，减小大的数
```

### 复杂度

- **时间**: O(n)
- **空间**: O(1)

---

## 3. 3Sum (Medium)

**LeetCode 15. 3Sum** | 🌟🌟🌟 面试高频，必掌握

### 题目

找出数组中所有和为 0 的三元组（三个不同的数），结果不能重复。

```
输入: nums = [-1, 0, 1, 2, -1, -4]
输出: [[-1, -1, 2], [-1, 0, 1]]
```

### 思路讲解

### 方法一：排序 + 双指针 ⭐ 推荐

**关键思路：把 3Sum 转化为 2Sum！**

1. 先排序数组
2. 固定一个数 `nums[i]`，然后在 `i+1` 到末尾的区间里用双指针找两个数和为 `-nums[i]`
3. 关键难点：**去重**

```
排序后: [-4, -1, -1, 0, 1, 2]

i=0, nums[0]=-4:
  在[-1,-1,0,1,2]里找两数和为4
  没有 → 跳过

i=1, nums[1]=-1:
  在[-1,0,1,2]里找两数和为1
  L=-1, R=2: -1+2=1 ✓ → [-1,-1,2] 加入答案

i=2, nums[2]=-1:  ← 和i=1值相同, 跳过(去重)
  ...
```

**去重策略**：
- 固定数去重：`if i > 0 and nums[i] == nums[i-1]: continue`
- 找到解后去重：找到一组后，跳过所有重复的 L 和 R

```python
def threeSum(nums):
    nums.sort()
    result = []
    n = len(nums)

    for i in range(n - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue
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

- **时间**: O(n²)
- **空间**: O(1) — 不算输出

---

### 方法二：排序 + 哈希集 (HashSet)

**思路**：固定一个数后，用 HashSet 做 2Sum（类似 Two Sum 的哈希表法）。

```python
def threeSum(nums):
    nums.sort()
    result = set()  # 用 set 去重

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

- **时间**: O(n²)
- **空间**: O(n) — seen set
- **评价**: 比双指针法代码短，但去重不如双指针自然

### 复杂度

- **时间**: O(n²) — 外层 O(n)，内层双指针 O(n)
- **空间**: O(1) — 不算输出数组

### 易错点

- 去重是最容易出错的地方：固定数要去重，找到解后也要去重
- 排序是必须的（双指针的前提）

---

## 4. Container With Most Water (Medium)

**LeetCode 11. Container With Most Water** | 🌟🌟 高频，思路巧妙

### 题目

给定一个数组表示隔板高度，找出两条隔板之间能装最多水的面积。

```
输入: height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
输出: 49
解释: 选择 index=1 (高8) 和 index=8 (高7)，宽度=7，水量=7×min(8,7)=49
```

### 思路讲解

**关键公式**：`水量 = 宽度(右-左) × 最小高度(短板效应)`

**贪心思想**：从最宽的两端开始，每次移动**矮的那一侧**。

**为什么移动矮的那一侧？**
- 水量由短板决定
- 如果移动高的那一侧，宽度减少 + 短板不变或更矮 → 水量不可能增加
- 如果移动矮的那一侧，虽然宽度减少，但可能遇到更高的板 → 水量可能增加

```
[1, 8, 6, 2, 5, 4, 8, 3, 7]
 ↑                          ↑
 L                          R
min(1,7)=1, 面积=1×8=8, max=8
1<7 → L右移

[1, 8, 6, 2, 5, 4, 8, 3, 7]
    ↑                       ↑
    L                       R
min(8,7)=7, 面积=7×7=49, max=49
8>7 → R左移

...以此类推直到 L 和 R 相遇
```

### 代码

```python
def maxArea(height):
    left, right = 0, len(height) - 1
    max_water = 0

    while left < right:
        # 计算当前面积
        h = min(height[left], height[right])
        w = right - left
        max_water = max(max_water, h * w)

        # 移动较短的那一侧
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1

    return max_water
```

### 复杂度

- **时间**: O(n)
- **空间**: O(1)

---

## 5. Trapping Rain Water (Hard)

**LeetCode 42. Trapping Rain Water** | 🌟🌟🌟 高难度经典，大厂必问

### 题目

给定隔板高度数组，计算下雨后能接多少水。

```
输入: height = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]
输出: 6

可视化:
        █
    █***██*█
  █*██*██████
0 1 0 2 1 0 1 3 2 1 2 1
(*表示水)
```

### 思路讲解

**核心问题**：位置 i 能装多少水？

→ 取决于它**左边最高的**和**右边最高的**中的较小值，再减去自身高度。

```
位置 i 的水量 = min(左边最高, 右边最高) - height[i]
             （如果为负数，则装不了水，取0）
```

**方法一：双数组（最好理解）**

```
height = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]

left_max[i]  = i 及 i 左边最高的高度
right_max[i] = i 及 i 右边最高的高度

left_max  = [0, 1, 1, 2, 2, 2, 2, 3, 3, 3, 3, 3]
right_max = [3, 3, 3, 3, 3, 3, 3, 3, 2, 2, 2, 1]

water[i]  = min(left_max[i], right_max[i]) - height[i]
          = [0, 0, 1, 0, 1, 2, 1, 0, 0, 1, 0, 0]
总和 = 6
```

**方法二：双指针（最优空间）**

不需要两个完整数组，动态维护 `left_max` 和 `right_max`。

### 代码

```python
# 方法一：双数组（推荐初学者用这个，最容易理解）
def trap(height):
    n = len(height)
    if n == 0:
        return 0

    # left_max[i] = i 位置左侧（含 i）的最大值
    left_max = [0] * n
    left_max[0] = height[0]
    for i in range(1, n):
        left_max[i] = max(left_max[i-1], height[i])

    # right_max[i] = i 位置右侧（含 i）的最大值
    right_max = [0] * n
    right_max[n-1] = height[n-1]
    for i in range(n-2, -1, -1):
        right_max[i] = max(right_max[i+1], height[i])

    # 计算水量
    water = 0
    for i in range(n):
        water += min(left_max[i], right_max[i]) - height[i]

    return water


# 方法二：双指针（空间 O(1)，面试加分）
def trap(height):
    left, right = 0, len(height) - 1
    left_max, right_max = 0, 0
    water = 0

    while left < right:
        if height[left] < height[right]:
            # 左边矮，处理左边
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            # 右边矮，处理右边
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1

    return water
```

### 复杂度

- 方法一：时间 O(n)，空间 O(n)
- 方法二：时间 O(n)，空间 O(1)

### 一步步理解双指针法

```
思路关键：在双指针法中，
如果 height[left] < height[right]，
那么 left 位置的 left_max 一定小于 right_max。

因为 left_max 是从左往右随 left 更新的，
而右边至少有一个 height[right] 比 left_max 大。

所以 left 位置的水量只取决于 left_max，不需要知道精确的 right_max!
这就是为什么可以动态计算。
```

---

## 双指针总结

| 模式 | 适用场景 | 代表题目 |
|------|---------|---------|
| 左右指针 | 有序数组、回文 | Valid Palindrome, Two Sum II, 3Sum |
| 贪心双指针 | 最优面积/水量 | Container With Most Water, Trapping Rain Water |
| 快慢指针 | 链表问题 | Linked List Cycle (见链表章节) |

### 什么时候想到双指针？

- 数组**有序** → 几乎一定可以用
- 找**一对**元素满足某个条件 → 考虑排序后双指针
- 需要 O(n²) → O(n) 的优化 → 试试双指针
