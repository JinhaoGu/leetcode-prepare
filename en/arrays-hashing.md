# Arrays & Hashing

> **Day 1-2** | 6 problems | Difficulty: Easy - Medium
>
> Hash Map / Hash Set are among the most commonly used data structures. Core insight: **trade space for time** — turn O(n²) lookups into O(1).

---

## Table of Contents

1. [Two Sum](#1-two-sum-easy) — Easy
2. [Contains Duplicate](#2-contains-duplicate-easy) — Easy
3. [Valid Anagram](#3-valid-anagram-easy) — Easy
4. [Group Anagrams](#4-group-anagrams-medium) — Medium
5. [Top K Frequent Elements](#5-top-k-frequent-elements-medium) — Medium
6. [Product of Array Except Self](#6-product-of-array-except-self-medium) — Medium

---

## 1. Two Sum (Easy)

**LeetCode 1. Two Sum** |  Must-know — extremely high interview frequency

### Problem

Given an integer array `nums` and a target value `target`, find the **indices** of two numbers that sum to `target`.

Assume exactly one solution exists, and you may not use the same element twice.

```
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
Explanation: nums[0] + nums[1] = 2 + 7 = 9
```

### Method 1: Brute Force

**Approach**: Try every possible pair — check if their sum equals target.

```
nums = [2, 7, 11, 15], target = 9

Outer loop i=0, num=2:
  Inner loop j=1, num=7: 2+7=9 ✓ → return [0, 1]
```

```python
def twoSum(nums, target):
    n = len(nums)
    for i in range(n):
        for j in range(i + 1, n):
            if nums[i] + nums[j] == target:
                return [i, j]
```

- **Time**: O(n²) — nested loops
- **Space**: O(1)

---

### Method 2: Hash Map ⭐ Recommended

**Optimization insight**: We want to find if `target - nums[i]` exists in the array. If we had a "memory" that instantly tells us whether a number has appeared before, we could solve this in one pass.

→ That's a hash map! Use a dictionary `{value: index}`. For each new number, check if its "other half" has already been seen.

**Step-by-step**:

```
nums = [2, 7, 11, 15], target = 9

i=0, num=2, need 9-2=7, dict={} → 7 not found, add {2:0}
i=1, num=7, need 9-7=2, dict={2:0} → found 2! return [0,1]
```

```python
def twoSum(nums, target):
    seen = {}  # {value: index}
    for i, num in enumerate(nums):
        complement = target - num  # the other half I need
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```

- **Time**: O(n) — single pass
- **Space**: O(n) — worst case, store all elements

### Common Pitfalls

- Check the hash map **before** adding the current element, otherwise an element might pair with itself
- Return **indices**, not values

---

## 2. Contains Duplicate (Easy)

**LeetCode 217. Contains Duplicate**

### Problem

Given an array, determine if any value appears at least twice.

```
Input: [1, 2, 3, 1]
Output: True
```

### Method 1: Hash Set ⭐ Recommended

**Approach**: Use a Set to track seen elements. If the current element is already in the Set, we found a duplicate.

```
[1, 2, 3, 1]
seen = {}
i=0: 1 not in seen → seen = {1}
i=1: 2 not in seen → seen = {1, 2}
i=2: 3 not in seen → seen = {1, 2, 3}
i=3: 1 in seen → return True!
```

```python
def containsDuplicate(nums):
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False

# Even more concise using Python set
def containsDuplicate(nums):
    return len(nums) != len(set(nums))
```

- **Time**: O(n)
- **Space**: O(n)

---

### Method 2: Sorting

**Approach**: Sort first — duplicates will be adjacent.

```
Sorted: [1, 1, 2, 3]
Compare adjacent: 1==1 ✓ → has duplicate
```

```python
def containsDuplicate(nums):
    nums.sort()
    for i in range(1, len(nums)):
        if nums[i] == nums[i - 1]:
            return True
    return False
```

- **Time**: O(n log n)
- **Space**: O(1) or O(n) depending on sort implementation

---

### Method 3: Brute Force (for understanding)

```python
def containsDuplicate(nums):
    n = len(nums)
    for i in range(n):
        for j in range(i + 1, n):
            if nums[i] == nums[j]:
                return True
    return False
```

- **Time**: O(n²)
- **Space**: O(1)

---

## 3. Valid Anagram (Easy)

**LeetCode 242. Valid Anagram** |  High frequency

### Problem

Given two strings `s` and `t`, return `True` if `t` is an anagram of `s`.

Anagram: formed by rearranging the same letters with the same frequency.

```
Input: s = "anagram", t = "nagaram"
Output: True
```

### Method 1: Frequency Counting ⭐ Recommended

**Core question**: How to verify two strings have exactly the same letters in the same counts?

Use a size-26 array (or dictionary) to count each letter:
- Iterate s: count +1
- Iterate t: count -1
- If all counts end up 0 → anagram!

```
s = "anagram", t = "nagaram"

After counting s: a=3, g=1, m=1, n=1, r=1
After subtracting t: a=0, g=0, m=0, n=0, r=0
All zero → True
```

```python
def isAnagram(s, t):
    if len(s) != len(t):
        return False

    count = [0] * 26  # 26 letters

    for i in range(len(s)):
        count[ord(s[i]) - ord('a')] += 1  # increment
        count[ord(t[i]) - ord('a')] -= 1  # decrement

    return all(c == 0 for c in count)
```

- **Time**: O(n)
- **Space**: O(1) — fixed 26

---

### Method 2: Sorting

**Approach**: Sort both strings — if they're anagrams, the sorted versions are identical.

```
"anagram" → sorted → "aaagmnr"
"nagaram" → sorted → "aaagmnr"
Equal → True
```

```python
def isAnagram(s, t):
    if len(s) != len(t):
        return False
    return sorted(s) == sorted(t)
```

- **Time**: O(n log n)
- **Space**: O(n)

---

## 4. Group Anagrams (Medium)

**LeetCode 49. Group Anagrams** |  High frequency

### Problem

Given an array of strings, group all anagrams together.

```
Input: ["eat", "tea", "tan", "ate", "nat", "bat"]
Output: [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```

### Approach

This is an upgrade of "Valid Anagram". The core question: **how to generate a unique key for each anagram group?**

Since anagrams become identical when sorted, use the sorted string as the key.

```
"eat" → sort → "aet" → {"aet": ["eat"]}
"tea" → sort → "aet" → {"aet": ["eat", "tea"]}
"tan" → sort → "ant" → {"aet": [...], "ant": ["tan"]}
...
```

### Code

```python
from collections import defaultdict

def groupAnagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    return list(groups.values())
```

**Advanced**: For O(n × k) time, use a character frequency tuple as the key instead of sorting:

```python
count = [0] * 26
for ch in s:
    count[ord(ch) - ord('a')] += 1
key = tuple(count)
```

### Complexity

- **Time**: O(n × k log k) — n words, k = max word length (sorting)
- **Space**: O(n × k)

---

## 5. Top K Frequent Elements (Medium)

**LeetCode 347. Top K Frequent Elements** |  High frequency

### Problem

Return the k most frequent elements.

```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1, 2]
```

### Two steps:
1. Count frequency of each number → hash map
2. Find the k highest frequencies → three methods below

---

### Method 1: Bucket Sort ⭐ Recommended — O(n)

**Approach**:
- Max possible frequency is n (array length)
- Create an array of buckets, `bucket[freq]` holds all numbers with that frequency
- Scan from high frequency to low, collecting until we have k elements

```
nums = [1,1,1,2,2,3], k = 2

Step 1: Count frequencies
freq = {1:3, 2:2, 3:1}

Step 2: Fill buckets
bucket[3] = [1]
bucket[2] = [2]
bucket[1] = [3]

Step 3: Collect from high to low
freq=3: take [1], have 1, need 1 more
freq=2: take [2], have 2, done! → [1, 2]
```

```python
def topKFrequent(nums, k):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    buckets = [[] for _ in range(len(nums) + 1)]
    for num, count in freq.items():
        buckets[count].append(num)

    result = []
    for count in range(len(nums), 0, -1):
        for num in buckets[count]:
            result.append(num)
            if len(result) == k:
                return result
```

- **Time**: O(n)
- **Space**: O(n)

---

### Method 2: Min Heap — O(n log k)

**Approach**: Maintain a min-heap of size k. The heap always holds the k elements with the highest frequencies.

```python
import heapq

def topKFrequent(nums, k):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    heap = []
    for num, count in freq.items():
        heapq.heappush(heap, (count, num))
        if len(heap) > k:
            heapq.heappop(heap)

    return [num for count, num in heap]
```

- **Time**: O(n log k)
- **Space**: O(n)

---

### Method 3: Sorting — O(n log n)

```python
def topKFrequent(nums, k):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    sorted_items = sorted(freq.items(), key=lambda x: x[1], reverse=True)
    return [num for num, count in sorted_items[:k]]
```

---

## 6. Product of Array Except Self (Medium)

**LeetCode 238. Product of Array Except Self** |  High frequency, requires a clever trick

### Problem

Return an array `answer` where `answer[i]` equals the product of all elements in `nums` except `nums[i]`.

**Constraint**: No division allowed, must be O(n).

```
Input: nums = [1, 2, 3, 4]
Output: [24, 12, 8, 6]
Explanation: 24=2×3×4, 12=1×3×4, 8=1×2×4, 6=1×2×3
```

### Key Insight

For position i, `answer[i] = (product of all elements to the left) × (product of all elements to the right)`

```
nums  = [ 1,  2,  3,  4]

For i=2 (value=3):
Left product = 1×2 = 2
Right product = 4
answer[2] = 2 × 4 = 8 ✓
```

---

### Method 1: Left & Right Arrays — Easiest to understand

**Approach**: Compute `left[i]` (product of all elements to the left of i) and `right[i]` (product of all elements to the right), then multiply.

```
nums  = [ 1,  2,  3,  4]
left  = [ 1,  1,  2,  6]  ← product of all elements to the left
right = [24, 12,  4,  1]  ← product of all elements to the right

answer[i] = left[i] × right[i]
answer = [24, 12, 8, 6]
```

```python
def productExceptSelf(nums):
    n = len(nums)
    left = [1] * n
    right = [1] * n
    answer = [0] * n

    for i in range(1, n):
        left[i] = left[i-1] * nums[i-1]

    for i in range(n-2, -1, -1):
        right[i] = right[i+1] * nums[i+1]

    for i in range(n):
        answer[i] = left[i] * right[i]

    return answer
```

- **Time**: O(n)
- **Space**: O(n)

---

### Method 2: O(1) Extra Space ⭐ Recommended

**Approach**: Reuse the answer array. First pass stores left products. Second pass multiplies by right products.

```
Pass 1 (left products):
prefix=1, i=0: answer[0]=1, prefix=1×1=1
          i=1: answer[1]=1, prefix=1×2=2
          i=2: answer[2]=2, prefix=2×3=6
          i=3: answer[3]=6
→ answer = [1, 1, 2, 6]

Pass 2 (multiply by right products):
suffix=1, i=3: answer[3]=6×1=6, suffix=1×4=4
          i=2: answer[2]=2×4=8, suffix=4×3=12
          i=1: answer[1]=1×12=12, suffix=12×2=24
          i=0: answer[0]=1×24=24
→ answer = [24, 12, 8, 6]
```

```python
def productExceptSelf(nums):
    n = len(nums)
    answer = [1] * n

    prefix = 1
    for i in range(n):
        answer[i] = prefix
        prefix *= nums[i]

    suffix = 1
    for i in range(n - 1, -1, -1):
        answer[i] *= suffix
        suffix *= nums[i]

    return answer
```

- **Time**: O(n)
- **Space**: O(1) — not counting the output array

### Common Pitfalls

- Initialize prefix and suffix to **1** (not 0) — the multiplicative identity
- The second pass must go **right to left**
- Division is forbidden by the problem constraints

---

## Hash Table Summary

| Problem Type | Data Structure | Core Technique |
|-------------|---------------|----------------|
| Pair-finding | Dict (HashMap) | `target - num` lookup |
| Dedup / existence | Set (HashSet) | O(1) membership check |
| Group / classify | Dict | Construct uniform key |
| Frequency stats | Dict + bucket/heap | Frequency → bucket index |
| Prefix/suffix products | Array | Two-pass, prefix × suffix |

### When to use a hash table?

- "Find a pair", "does X exist?", "remove duplicates" → first thought: hash table
- Any scenario where you want to reduce lookup from O(n) to O(1)
