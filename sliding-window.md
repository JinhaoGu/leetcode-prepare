# Sliding Window (滑动窗口)

> **Day 5-6** | 4 题 | 难度: Easy - Hard
>
> 滑动窗口是双指针的一种特殊形式，用于处理**连续子数组/子串**的问题。核心：维护一个"窗口"，右指针扩展，左指针收缩。

---

## 目录

1. [Best Time to Buy and Sell Stock (买卖股票最佳时机)](#1-best-time-to-buy-and-sell-stock-easy) — Easy
2. [Longest Substring Without Repeating Characters (无重复字符的最长子串)](#2-longest-substring-without-repeating-characters-medium) — Medium
3. [Longest Repeating Character Replacement (最长重复字符替换)](#3-longest-repeating-character-replacement-medium) — Medium
4. [Minimum Window Substring (最小覆盖子串)](#4-minimum-window-substring-hard) — Hard

---

## 滑动窗口的本质

```
窗口 = [left, right] 区间
- right 扩展窗口（寻找可行解）
- left 收缩窗口（优化解）

套路模板：
left = 0
for right in range(len(arr)):
    # 处理 arr[right]（添加新元素到窗口）
    while 窗口不满足条件:
        # 处理 arr[left]（移除元素）
        left += 1
    # 更新答案
```

---

## 1. Best Time to Buy and Sell Stock (Easy)

**LeetCode 121. Best Time to Buy and Sell Stock** | 🌟 面试必问

### 题目

给定股票每天的价格，只能买卖一次（先买后卖），求最大利润。

```
输入: prices = [7, 1, 5, 3, 6, 4]
输出: 5
解释: 第2天买入(价格1)，第5天卖出(价格6)，利润=5
```

### 思路讲解

**关键**：在某天卖出，那买入一定是**这天之前的最低价格**。

所以遍历每一天：
- 维护一个 `min_price`（到目前为止的最低价格）
- 计算当天卖出的利润：`prices[i] - min_price`
- 更新最大利润

```
prices = [7, 1, 5, 3, 6, 4]

i=0: price=7, min_price=7, profit=0,  max=0
i=1: price=1, min_price=1, profit=0,  max=0
i=2: price=5, min_price=1, profit=4,  max=4
i=3: price=3, min_price=1, profit=2,  max=4
i=4: price=6, min_price=1, profit=5,  max=5  ← 最佳
i=5: price=4, min_price=1, profit=3,  max=5
```

### 代码

```python
def maxProfit(prices):
    min_price = float('inf')
    max_profit = 0

    for price in prices:
        if price < min_price:
            min_price = price
        else:
            max_profit = max(max_profit, price - min_price)

    return max_profit
```

### 复杂度

- **时间**: O(n)
- **空间**: O(1)

### 易错点

- 不能先卖后买！所以只需维护到当前天的最低价格
- `min_price` 初始化为无穷大（或第一个元素）

---

## 2. Longest Substring Without Repeating Characters (Medium)

**LeetCode 3. Longest Substring Without Repeating Characters** | 🌟🌟🌟 高频

### 题目

找出不包含重复字符的最长子串的长度。

```
输入: s = "abcabcbb"
输出: 3
解释: 最长无重复子串是 "abc"，长度 3
```

### 思路讲解

滑动窗口标准题！维护一个窗口，里面所有字符都不重复。

- 右指针不断扩展，加入新字符
- 如果新字符导致窗口有重复 → 左指针收缩直到重复消失
- 每次窗口合法时，更新最长长度

用**哈希集**记录当前窗口里有哪些字符。

```
s = "a b c a b c b b"
     ↑     ↑
     L     R

R=0: "a",    set={a},     max=1
R=1: "ab",   set={a,b},   max=2
R=2: "abc",  set={a,b,c}, max=3
R=3: "abca"  ← a 重复了!
     L 右移到重复字符之后 → "bca", max=3
R=4: "bcab"  ← b 重复了!
     L 右移 → "cab", max=3
R=5: "cabc"  ← c 重复了!
     L 右移 → "abc", max=3
R=6: "abcb"  ← b 重复了!
     L 右移 → "cb", max=3
R=7: "cbb"   ← b 重复了!
     L 右移 → "b", max=3

输出: 3
```

### 代码

```python
def lengthOfLongestSubstring(s):
    seen = set()
    left = 0
    max_len = 0

    for right in range(len(s)):
        # 收缩窗口直到没有重复
        while s[right] in seen:
            seen.remove(s[left])
            left += 1

        # 加入新字符
        seen.add(s[right])
        max_len = max(max_len, right - left + 1)

    return max_len
```

**优化（直接用字典记录位置）**：当重复发生时，left 可以直接跳到重复字符的下一个位置。

```python
def lengthOfLongestSubstring(s):
    last_pos = {}  # {字符: 最后出现的位置}
    left = 0
    max_len = 0

    for right, ch in enumerate(s):
        if ch in last_pos and last_pos[ch] >= left:
            # 字符在当前窗口内重复了，左指针跳到它后面
            left = last_pos[ch] + 1
        last_pos[ch] = right
        max_len = max(max_len, right - left + 1)

    return max_len
```

### 复杂度

- **时间**: O(n)
- **空间**: O(min(n, 26)) — 最多 26 个字母或 n 个字符

---

## 3. Longest Repeating Character Replacement (Medium)

**LeetCode 424. Longest Repeating Character Replacement** | 🌟🌟 需要一点技巧

### 题目

给定一个全大写字母的字符串，你最多可以替换 k 个字符。找出替换后能得到的最长相同字符子串的长度。

```
输入: s = "ABAB", k = 2
输出: 4
解释: 把两个 A 换成 B，或者两个 B 换成 A → 得到 "BBBB" 或 "AAAA"，长度 4
```

### 思路讲解

**核心转换**：问题等价于 → 找一个最长子串，使得 `子串长度 - 子串中出现最多的字符的次数 <= k`。

为什么？因为我们可以保留出现最多的字符，把其他 k 个字符替换成它。

**窗口维护**：
- 维护窗口内每个字符的频率
- 维护窗口内最高频字符的频数 `max_freq`
- 窗口合法的条件：`(窗口长度) - max_freq <= k`
- 如果不合法，左指针右移

```
s = "AABABBA", k = 1

R=0 (A): 窗口 "A",     freq={A:1},         max_freq=1, 1-1=0≤1 ✓, max=1
R=1 (A): 窗口 "AA",    freq={A:2},         max_freq=2, 2-2=0≤1 ✓, max=2
R=2 (B): 窗口 "AAB",   freq={A:2,B:1},     max_freq=2, 3-2=1≤1 ✓, max=3
R=3 (A): 窗口 "AABA",  freq={A:3,B:1},     max_freq=3, 4-3=1≤1 ✓, max=4
R=4 (B): 窗口 "AABAB", freq={A:3,B:2},     max_freq=3, 5-3=2>1 ✗!
         L 右移: "ABAB", freq 更新, 4-3=1≤1 ✓, max=4
...
最终: max=4 ("AABA")
```

**关键优化**：不需要精确维护 max_freq！因为 max_freq 只会变大，如果 left 右移变小了也没关系，我们想要更大的窗口。

### 代码

```python
def characterReplacement(s, k):
    freq = {}
    max_freq = 0
    left = 0
    max_len = 0

    for right, ch in enumerate(s):
        freq[ch] = freq.get(ch, 0) + 1
        max_freq = max(max_freq, freq[ch])

        # 窗口长度 - 最多字符数 > k → 不合法
        window_len = right - left + 1
        if window_len - max_freq > k:
            # 移除左边字符
            freq[s[left]] -= 1
            left += 1

        # 此时窗口一定合法
        max_len = max(max_len, right - left + 1)

    return max_len
```

### 复杂度

- **时间**: O(n)
- **空间**: O(26) = O(1)

---

## 4. Minimum Window Substring (Hard)

**LeetCode 76. Minimum Window Substring** | 🌟🌟🌟 滑动窗口终极测试

### 题目

给定字符串 `s` 和 `t`，找出 `s` 中包含 `t` 所有字符（包括重复）的最小子串。

```
输入: s = "ADOBECODEBANC", t = "ABC"
输出: "BANC"
```

### 思路讲解

这是滑动窗口最完整的模板题。

**步骤**：
1. 统计 t 中每个字符需要的数量 → `need`
2. 用 `have` 记录当前窗口已经满足了 t 中多少个字符
3. 右指针扩展，获取新字符
4. 当窗口包含所有 t 的字符时（`have == required`），尝试收缩左指针找更小的窗口
5. 记录最小窗口

```
s = "ADOBECODEBANC", t = "ABC"
need = {A:1, B:1, C:1}, required=3

右扩直到包含所有必需字符:
"ADOBEC" → have={A:1, B:1, C:1}, have_count=3 ✓

然后收缩左边:
"DOBEC" → 丢失 A, have_count=2 ✗
→ 不能收缩了

继续右扩:
"ADOBECODEBA" → 又集齐了
收缩: "CODEBA" → 再收缩: "ODEBA" → "DEBA" ✗
→ "ODEBA" 不够, 最小是 "CODEBA"

继续右扩到末尾找 "BANC"...
最终: "BANC" (长度4, 最小)
```

### 代码

```python
from collections import Counter

def minWindow(s, t):
    if not t or not s:
        return ""

    need = Counter(t)  # t 中每个字符需要的数量
    window = {}        # 当前窗口中字符的数量
    required = len(need)       # 需要满足的不同字符种类
    have = 0                   # 已经满足的字符种类数

    left = 0
    min_len = float('inf')
    min_start = 0

    for right, ch in enumerate(s):
        # 扩展窗口
        window[ch] = window.get(ch, 0) + 1

        # 检查当前字符是否满足要求
        if ch in need and window[ch] == need[ch]:
            have += 1  # 这个字符的数量够了

        # 当所有字符都满足要求时，尝试收缩
        while have == required:
            # 更新最小窗口
            cur_len = right - left + 1
            if cur_len < min_len:
                min_len = cur_len
                min_start = left

            # 收缩：移除 left 位置的字符
            left_ch = s[left]
            window[left_ch] -= 1
            if left_ch in need and window[left_ch] < need[left_ch]:
                have -= 1  # 这个字符不再满足要求
            left += 1

    if min_len == float('inf'):
        return ""
    return s[min_start:min_start + min_len]
```

### 复杂度

- **时间**: O(m + n)，m = len(s), n = len(t)
- **空间**: O(n) — Counter 的大小

### 一步步理解

```
1. need = {A:1, B:1, C:1}
   这是我们需要满足的条件：至少1个A、1个B、1个C

2. have 记录的是"满足了的字符种类数"
   比如窗口中有2个A、1个B、0个C → have=2（A和B满足了，C不够）

3. 当 have == required (=3) 时，说明窗口包含了所有需要的字符
   此时可以尝试收缩左边界找更优解

4. 收缩时，如果移除了一个"刚好够"的字符，have 会减少
   比如需要1个A，窗口中正好1个A，移除了 → have -= 1
```

### 易错点

- 使用 `== need[ch]`（等于）而非 `>=` 来判断满足，避免重复计数
- 收缩时，判断条件类似：`window[left_ch] < need[left_ch]` 说明从"刚好满足"变成"不够了"
- 初始化 `min_len = inf`，最后检查是否找到过

---

## 滑动窗口总结

### 三步走模板

```python
def sliding_window_template(s):
    left = 0
    window_state = ...  # 窗口状态
    result = ...        # 答案

    for right in range(len(s)):
        # 1. 将 s[right] 加入窗口
        add_to_window(s[right])

        # 2. 收缩窗口（当不满足条件时）
        while window_invalid():
            remove_from_window(s[left])
            left += 1

        # 3. 更新答案（窗口合法时）
        update_result()

    return result
```

### 题型识别

看到 **"子串"、"子数组"、"连续"** + **"最大/最小"**，联想滑动窗口：

| 题目 | 窗口维护内容 | 合法条件 |
|------|------------|---------|
| 无重复字符子串 | 字符集合 | 无重复 |
| 替换后相同字符 | 字符频率 | len - max_freq ≤ k |
| 最小覆盖子串 | 字符频率 vs need | have == required |
