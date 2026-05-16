# Sliding Window

> **Day 5-6** | 4 problems | Difficulty: Easy - Hard
>
> Sliding Window is a special form of two pointers for **contiguous subarray / substring** problems. Core: maintain a "window", expand right pointer to find solutions, contract left to optimize.

---

## Table of Contents

1. [Best Time to Buy and Sell Stock](#1-best-time-to-buy-and-sell-stock-easy) — Easy
2. [Longest Substring Without Repeating Characters](#2-longest-substring-without-repeating-characters-medium) — Medium
3. [Longest Repeating Character Replacement](#3-longest-repeating-character-replacement-medium) — Medium
4. [Minimum Window Substring](#4-minimum-window-substring-hard) — Hard

---

## The Essence of Sliding Window

```
Window = [left, right] interval
- right expands window (finding feasible solutions)
- left contracts window (optimizing solutions)

Template:
left = 0
for right in range(len(arr)):
    # Process arr[right] (add new element to window)
    while window_invalid():
        # Handle arr[left] (remove element)
        left += 1
    # Update answer
```

---

## 1. Best Time to Buy and Sell Stock (Easy)

**LeetCode 121. Best Time to Buy and Sell Stock** |  Must-know

### Problem

Given daily stock prices, buy once then sell once. Find max profit.

```
Input: prices = [7, 1, 5, 3, 6, 4]
Output: 5
Explanation: buy at day 2 (price=1), sell at day 5 (price=6), profit=5
```

### Approach

If you sell on day i, you must have bought at the **lowest price before day i**. Track `min_price` as we go.

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

- **Time**: O(n)
- **Space**: O(1)

---

## 2. Longest Substring Without Repeating Characters (Medium)

**LeetCode 3. Longest Substring Without Repeating Characters** |  High frequency

### Problem

Find the length of the longest substring without repeating characters.

```
Input: s = "abcabcbb"
Output: 3 ("abc")
```

### Approach

Maintain a window where all characters are unique. Use a set to track characters in the current window.

```python
# Using a set
def lengthOfLongestSubstring(s):
    seen = set()
    left = 0
    max_len = 0
    for right, ch in enumerate(s):
        while ch in seen:
            seen.remove(s[left])
            left += 1
        seen.add(ch)
        max_len = max(max_len, right - left + 1)
    return max_len

# Optimized: use dict to jump left directly
def lengthOfLongestSubstring(s):
    last_pos = {}
    left = 0
    max_len = 0
    for right, ch in enumerate(s):
        if ch in last_pos and last_pos[ch] >= left:
            left = last_pos[ch] + 1
        last_pos[ch] = right
        max_len = max(max_len, right - left + 1)
    return max_len
```

---

## 3. Longest Repeating Character Replacement (Medium)

**LeetCode 424. Longest Repeating Character Replacement** |  Requires a clever insight

### Problem

Given a string of uppercase letters, you can replace at most k characters. Find the longest substring of the same character after replacements.

```
Input: s = "ABAB", k = 2
Output: 4
Explanation: replace two 'A's with 'B' or vice versa → "BBBB" or "AAAA"
```

### Approach

**Core transformation**: Find the longest substring where `window_length - max_freq(char in window) ≤ k`.

Why? We keep the most frequent character, replace the other (≤ k) characters.

```python
def characterReplacement(s, k):
    freq = {}
    max_freq = 0
    left = 0
    max_len = 0

    for right, ch in enumerate(s):
        freq[ch] = freq.get(ch, 0) + 1
        max_freq = max(max_freq, freq[ch])

        if (right - left + 1) - max_freq > k:
            freq[s[left]] -= 1
            left += 1

        max_len = max(max_len, right - left + 1)
    return max_len
```

---

## 4. Minimum Window Substring (Hard)

**LeetCode 76. Minimum Window Substring** |  Ultimate sliding window test

### Problem

Given strings `s` and `t`, find the smallest substring of `s` that contains all characters of `t` (including duplicates).

```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
```

### Approach

1. Count required characters from t → `need`
2. Track `have` = how many character types are satisfied in the current window
3. Expand right, contract left when all required chars are satisfied

```python
from collections import Counter

def minWindow(s, t):
    if not t or not s:
        return ""

    need = Counter(t)
    window = {}
    required = len(need)
    have = 0
    left = 0
    min_len = float('inf')
    min_start = 0

    for right, ch in enumerate(s):
        window[ch] = window.get(ch, 0) + 1
        if ch in need and window[ch] == need[ch]:
            have += 1

        while have == required:
            cur_len = right - left + 1
            if cur_len < min_len:
                min_len = cur_len
                min_start = left

            left_ch = s[left]
            window[left_ch] -= 1
            if left_ch in need and window[left_ch] < need[left_ch]:
                have -= 1
            left += 1

    if min_len == float('inf'):
        return ""
    return s[min_start:min_start + min_len]
```

### Common Pitfalls

- Use `== need[ch]` (equals) not `>=` to avoid double-counting
- When contracting, check if `window[left_ch] < need[left_ch]` — it went from "exactly enough" to "not enough"

---

## Sliding Window Summary

### Three-Step Template

```python
def sliding_window(s):
    left = 0
    window_state = ...
    result = ...

    for right in range(len(s)):
        # 1. Add s[right] to window
        # 2. Contract while invalid
        while window_invalid():
            # remove s[left]
            left += 1
        # 3. Update result

    return result
```

### Problem Recognition

| Problem | What to Maintain | Valid Condition |
|---------|-----------------|-----------------|
| No repeat substring | Character set | No duplicates |
| Replace to same char | Character frequencies | len - max_freq ≤ k |
| Minimum window substring | Frequencies vs need | have == required |
