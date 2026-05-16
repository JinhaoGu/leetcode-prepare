# Arrays & Hashing (数组与哈希表)

> **Day 1-2** | 6 题 | 难度: Easy - Medium
>
> 哈希表（Hash Map / Hash Set）是最常用的数据结构之一。核心思想：**用空间换时间**，把 O(n²) 的查找降为 O(1)。

---

## 目录

1. [Two Sum (两数之和)](#1-two-sum-easy) — Easy
2. [Contains Duplicate (存在重复元素)](#2-contains-duplicate-easy) — Easy
3. [Valid Anagram (有效的字母异位词)](#3-valid-anagram-easy) — Easy
4. [Group Anagrams (字母异位词分组)](#4-group-anagrams-medium) — Medium
5. [Top K Frequent Elements (前 K 个高频元素)](#5-top-k-frequent-elements-medium) — Medium
6. [Product of Array Except Self (除自身以外数组的乘积)](#6-product-of-array-except-self-medium) — Medium

---

## 1. Two Sum (Easy)

**LeetCode 1. Two Sum** | 🌟 必刷，面试出现率极高

### 题目

给定一个整数数组 `nums` 和一个目标值 `target`，找出数组中和为 `target` 的两个数的**下标**。

假设每组输入恰好只有一个解，且同一个元素不能使用两次。

```
输入: nums = [2, 7, 11, 15], target = 9
输出: [0, 1]
解释: nums[0] + nums[1] = 2 + 7 = 9
```

### 思路讲解

**暴力法（你首先想到的）**：两层 for 循环，尝试所有组合。
```
for i from 0 to n-1:
    for j from i+1 to n-1:
        if nums[i] + nums[j] == target:
            return [i, j]
```
时间复杂度 O(n²)，太慢了。

**优化思路**：
我们想要找 `target - nums[i]` 是否在数组中。如果有一个"记忆"能瞬间告诉你某个数是否出现过，就能一次遍历解决问题。

→ 这就是哈希表！用字典记录 `{值: 下标}`，每遇到一个新数，先检查它的"另一半"是否已经出现过。

**一步步推导**：

```
nums = [2, 7, 11, 15], target = 9

i=0, num=2, 需要找 9-2=7, 字典={} → 找不到7，把{2:0}加入字典
i=1, num=7, 需要找 9-7=2, 字典={2:0} → 找到2! 返回[0,1]
```

### 代码

```python
def twoSum(nums, target):
    seen = {}  # {值: 下标}
    for i, num in enumerate(nums):
        complement = target - num  # 我需要的另一半
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```

### 复杂度

- **时间**: O(n) — 只遍历一遍
- **空间**: O(n) — 最坏情况存所有元素到哈希表

### 易错点

- 先检查再添加到字典，否则元素可能和自己配对
- 返回的是**下标**，不是值

---

## 2. Contains Duplicate (Easy)

**LeetCode 217. Contains Duplicate**

### 题目

给定数组，判断是否存在重复元素。

```
输入: [1, 2, 3, 1]
输出: True
```

### 思路讲解

**暴力法**：两层循环比较 → O(n²)

**排序法**：排序后相邻比较 → O(n log n)

**最优法（哈希集）**：用一个 Set 记录见过的元素。遍历时如果发现当前元素已经在 Set 中，说明重复。

一步推导：
```
[1, 2, 3, 1]
seen = {}
i=0: 1 不在 seen → seen = {1}
i=1: 2 不在 seen → seen = {1, 2}
i=2: 3 不在 seen → seen = {1, 2, 3}
i=3: 1 在 seen → 返回 True!
```

### 代码

```python
def containsDuplicate(nums):
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False

# 更简洁写法
def containsDuplicate(nums):
    return len(nums) != len(set(nums))
```

### 复杂度

- **时间**: O(n)
- **空间**: O(n)

---

## 3. Valid Anagram (Easy)

**LeetCode 242. Valid Anagram** | 🌟 高频

### 题目

给定两个字符串 `s` 和 `t`，判断 `t` 是不是 `s` 的字母异位词（Anagram）。

字母异位词：由相同的字母以相同的次数组成的单词，只是顺序不同。

```
输入: s = "anagram", t = "nagaram"
输出: True
```

### 思路讲解

**核心问题**：如何判断两个字符串包含完全相同的字母且次数一致？

**解法1：排序比较**
把两个字符串排序，排完后完全一样就是 anagram。
```
"anagram" → "aaagmnr"
"nagaram" → "aaagmnr"
相同 → True
```
时间 O(n log n)，空间 O(1)（语言相关）。

**解法2：哈希统计（更优）**
用一个大小为 26 的数组（或用字典）统计每个字母出现的次数。
- 遍历 s，计数 +1
- 遍历 t，计数 -1
- 如果最后所有计数都是 0，就是 anagram

```
s = "anagram", t = "nagaram"

统计 s 后: a=3, g=1, m=1, n=1, r=1
减去 t 后: a=0, g=0, m=0, n=0, r=0
全部为0 → True
```

### 代码

```python
def isAnagram(s, t):
    if len(s) != len(t):
        return False

    count = [0] * 26  # 26 个字母

    for i in range(len(s)):
        count[ord(s[i]) - ord('a')] += 1  # 对应字母计数+1
        count[ord(t[i]) - ord('a')] -= 1  # 对应字母计数-1

    # 如果全是0，说明完全匹配
    return all(c == 0 for c in count)
```

### 易错点

- 先判断长度是否相等，不相等直接返回 False
- `ord(char) - ord('a')` 把 a-z 映射到 0-25

---

## 4. Group Anagrams (Medium)

**LeetCode 49. Group Anagrams** | 🌟 高频

### 题目

给定一个字符串数组，把所有的字母异位词分到同一组。

```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```

### 思路讲解

这道题是 "Valid Anagram" 的升级版。核心问题：**如何为每个 anagram 生成唯一标识？**

既然同一组 anagram 排序后一样，我们可以把排序后的字符串作为 key，然后把原字符串归类到这个 key 下。

```
"eat" → 排序 → "aet" → {"aet": ["eat"]}
"tea" → 排序 → "aet" → {"aet": ["eat", "tea"]}
"tan" → 排序 → "ant" → {"aet": [...], "ant": ["tan"]}
...
```

### 代码

```python
def groupAnagrams(strs):
    groups = {}
    for s in strs:
        key = ''.join(sorted(s))  # 排序后作为key
        if key not in groups:
            groups[key] = []
        groups[key].append(s)
    return list(groups.values())

# 优化：用 defaultdict
from collections import defaultdict
def groupAnagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    return list(groups.values())
```

### 复杂度

- **时间**: O(n × k log k)，n 是单词数，k 是最长单词长度（排序开销）
- **空间**: O(n × k)

### 进阶思考

如果不想排序（因为 k log k 的排序），可以统计字母频率作为 key：
```python
# key 变成 "1,0,0,...,0" 形式（每个位置代表一个字母的计数）
count = [0] * 26
for ch in s:
    count[ord(ch) - ord('a')] += 1
key = tuple(count)
```
这样 key 的生成是 O(k)，总时间 O(n × k)。

---

## 5. Top K Frequent Elements (Medium)

**LeetCode 347. Top K Frequent Elements** | 🌟 高频

### 题目

找出数组中出现频率最高的 k 个数字。

```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1, 2]
```

### 思路讲解

**分两步走**：
1. 统计每个数字的频率 → 用哈希表
2. 找出频率最高的 k 个 → 有三种方法

**方法一：排序（简单但不是最优）**
统计完频率后，按频率排序，取前 k 个。
时间 O(n log n)，排序是所有元素的排序。

**方法二：堆（最优）**
用最小堆维护前 k 大的频率。
时间 O(n log k)，当 k 远小于 n 时很快。

**方法三：桶排序（巧妙的 O(n) 解法）**
- 频率最大不超过 n
- 建一个大小为 n+1 的"桶"数组，`bucket[freq]` 存放频率为 freq 的所有数
- 从高频往低频遍历，取满 k 个即停

我们这里展示方法三，因为它是纯 O(n) 的巧妙解法。

```
nums = [1,1,1,2,2,3], k = 2

Step 1: 统计频率
freq = {1:3, 2:2, 3:1}

Step 2: 放入桶
bucket[3] = [1]
bucket[2] = [2]
bucket[1] = [3]

Step 3: 从高往低取
从频率3开始: 取 [1], 现在有了1个, 还需要1个
从频率2: 取 [2], 现在有了2个, 够了! → [1, 2]
```

### 代码

```python
def topKFrequent(nums, k):
    # Step 1: 统计频率
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    # Step 2: 桶排序 (频率作为索引)
    # 频率最大为 len(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    for num, count in freq.items():
        buckets[count].append(num)

    # Step 3: 从高频往低频收集
    result = []
    for count in range(len(nums), 0, -1):  # 从高往低
        for num in buckets[count]:
            result.append(num)
            if len(result) == k:
                return result
```

### 复杂度

- **时间**: O(n) — 桶排序
- **空间**: O(n)

---

## 6. Product of Array Except Self (Medium)

**LeetCode 238. Product of Array Except Self** | 🌟 高频，技巧性强

### 题目

给定数组 `nums`，返回数组 `answer`，其中 `answer[i]` 等于 `nums` 中除了 `nums[i]` 之外所有元素的乘积。

**约束**：不能用除法，且要在 O(n) 内完成。

```
输入: nums = [1, 2, 3, 4]
输出: [24, 12, 8, 6]
解释: 24 = 2×3×4, 12 = 1×3×4, 8 = 1×2×4, 6 = 1×2×3
```

### 思路讲解

**如果可以用除法**：算出总乘积，然后 `answer[i] = total / nums[i]`。但不能用。

**关键洞察**：对于位置 i，`answer[i] = (i 左边所有元素的乘积) × (i 右边所有元素的乘积)`

```
nums  = [ 1,  2,  3,  4]

对 i=2 (值=3):
左边乘积 = 1×2 = 2
右边乘积 = 4
answer[2] = 2 × 4 = 8 ✓
```

**实现技巧 —— 两次遍历**：

第一次（从左到右）：计算每个位置**左边**的乘积
第二次（从右到左）：计算每个位置**右边**的乘积，再和左边的乘积相乘

```
nums    = [ 1,  2,  3,  4]

第一遍（算左边乘积）:
prefix = 1 (初始)
i=0: answer[0] = prefix = 1; prefix = 1×1 = 1
i=1: answer[1] = prefix = 1; prefix = 1×2 = 2
i=2: answer[2] = prefix = 2; prefix = 2×3 = 6
i=3: answer[3] = prefix = 6; prefix = 6×4 = 24

answer = [1, 1, 2, 6]  ← 这是每个位置左边元素的乘积

第二遍（算右边乘积，同时乘进去）:
suffix = 1 (初始)
i=3: answer[3] = 6 × 1 = 6;  suffix = 1×4 = 4
i=2: answer[2] = 2 × 4 = 8;  suffix = 4×3 = 12
i=1: answer[1] = 1 × 12 = 12; suffix = 12×2 = 24
i=0: answer[0] = 1 × 24 = 24; suffix = 24×1 = 24

answer = [24, 12, 8, 6]  ← 完成!
```

### 代码

```python
def productExceptSelf(nums):
    n = len(nums)
    answer = [1] * n

    # 第一遍：计算每个位置左边的乘积
    prefix = 1
    for i in range(n):
        answer[i] = prefix       # 当前存的是左边乘积
        prefix = prefix * nums[i]  # 为下一个位置更新 prefix

    # 第二遍：乘上每个位置右边的乘积
    suffix = 1
    for i in range(n - 1, -1, -1):
        answer[i] = answer[i] * suffix  # 左边×右边 = 最终结果
        suffix = suffix * nums[i]       # 为上一个位置更新 suffix

    return answer
```

### 复杂度

- **时间**: O(n) — 两次遍历
- **空间**: O(1) — 不算输出数组（answer 不计入额外空间）

### 易错点

- prefix 初始值是 1（不是 0），因为乘积单位元是 1
- 第二遍必须**从右往左**遍历

---

## 哈希表总结

| 问题类型 | 用到什么 | 核心技巧 |
|---------|---------|---------|
| 查找配对 | Dict (HashMap) | `target - num` 查表 |
| 去重/存在性 | Set (HashSet) | O(1) 查找 |
| 分组归类 | Dict | 构造统一 key |
| 频率统计 | Dict + 桶/堆 | 频率→桶索引 |
| 前后缀乘积 | 数组 | 两次遍历，prefix × suffix |

### 什么时候用哈希表？

- "找配对"、"是否存在"、"去重" → 第一反应就应该是哈希表
- 任何想把查找从 O(n) 降到 O(1) 的场景
