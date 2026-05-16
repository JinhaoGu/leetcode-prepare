# Intervals (区间问题)

> **Day 24** | 3 题 | 难度: Medium
>
> 区间问题几乎都有一个标准操作：**按起点排序**。排序后，区间之间的关系就变得清晰了。

---

## 目录

1. [Merge Intervals (合并区间)](#1-merge-intervals-medium) — Medium
2. [Insert Interval (插入区间)](#2-insert-interval-medium) — Medium
3. [Non-overlapping Intervals (无重叠区间)](#3-non-overlapping-intervals-medium) — Medium

---

## 1. Merge Intervals (Medium)

**LeetCode 56. Merge Intervals** | 🌟🌟🌟 高频

### 题目

合并所有重叠的区间。

```
输入: [[1,3], [2,6], [8,10], [15,18]]
输出: [[1,6], [8,10], [15,18]]
解释: [1,3]和[2,6]重叠 → 合并为[1,6]
```

### 思路讲解

**Step 1**：按起点排序。

**Step 2**：遍历，每次比较当前区间和上一个合并后的区间：
- 如果 `当前.start > 上一个.end` → 不重叠，加入结果
- 否则 → 重叠，更新上一个的 `end = max(上一个.end, 当前.end)`

```
排序后: [[1,3], [2,6], [8,10], [15,18]]

初始: result = [[1,3]]

[1,3] vs [2,6]: 2 ≤ 3 → 重叠! end = max(3,6) = 6
  result = [[1,6]]

[1,6] vs [8,10]: 8 > 6 → 不重叠! 加入
  result = [[1,6], [8,10]]

[8,10] vs [15,18]: 15 > 10 → 不重叠! 加入
  result = [[1,6], [8,10], [15,18]]
```

### 代码

```python
def merge(intervals):
    if not intervals:
        return []

    intervals.sort(key=lambda x: x[0])  # 按起点排序
    result = [intervals[0]]

    for start, end in intervals[1:]:
        last_end = result[-1][1]
        if start <= last_end:
            # 重叠：扩展上一个区间的终点
            result[-1][1] = max(last_end, end)
        else:
            # 不重叠：新区间
            result.append([start, end])

    return result
```

### 复杂度

- **时间**: O(n log n) — 排序
- **空间**: O(n) — 输出

### 易错点

- 合并时用 `max(last_end, end)` 而不是直接 `end`！因为可能 `[1,5]` 和 `[2,3]` 重叠，`[2,3]` 完全在 `[1,5]` 里面
- Python 中 `intervals.sort(key=lambda x: x[0])` 等同于 `intervals.sort()`，因为默认按第一个元素排序

---

## 2. Insert Interval (Medium)

**LeetCode 57. Insert Interval** | 🌟🌟

### 题目

给定一个**无重叠**的区间列表（已按起点排序），插入一个新区间，保持不重叠。

```
输入: intervals = [[1,3],[6,9]], newInterval = [2,5]
输出: [[1,5],[6,9]]
```

### 思路讲解

把区间分成三部分处理：

1. **左边不重叠的区间**：`当前.end < 新区间.start` → 直接加入
2. **重叠的区间**：合并到一起，更新 `newInterval` 的范围
3. **右边不重叠的区间**：`当前.start > 新区间.end` → 直接加入

```
intervals = [[1,3],[6,9]], newInterval = [2,5]

左部分: 没有 (没有区间的end < 2)

重叠部分:
  [1,3] 与 [2,5] 重叠: newInterval = [min(1,2), max(3,5)] = [1,5]
  后面的 [6,9]: 6 > 5 → 不重叠，属于右部分

输出: [[1,5], [6,9]]
```

### 代码

```python
def insert(intervals, newInterval):
    result = []
    i = 0
    n = len(intervals)

    # 1. 加入所有在新区间之前的区间
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1

    # 2. 合并所有重叠的区间
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)

    # 3. 加入所有在新区间之后的区间
    while i < n:
        result.append(intervals[i])
        i += 1

    return result
```

### 复杂度

- **时间**: O(n) — 一次遍历（输入已排序）
- **空间**: O(n) — 输出

---

## 3. Non-overlapping Intervals (Medium)

**LeetCode 435. Non-overlapping Intervals** | 🌟🌟

### 题目

找出最少需要移除多少个区间，使剩余区间互不重叠。

```
输入: intervals = [[1,2],[2,3],[3,4],[1,3]]
输出: 1
解释: 移除 [1,3] 后，剩下的互不重叠
```

### 思路讲解

**贪心策略**：按**终点**排序（不是起点！），遍历时保留结束时间最早的区间。

**为什么按终点排序？** 结束越早，留给后面区间的空间就越大。

每次遇到重叠，就移除当前这个（因为它结束得更晚）。

```
排序后(按终点): [[1,2], [2,3], [1,3], [3,4]]

维护 prev_end = -inf, remove_count = 0

[1,2]: 1 ≥ -inf → 不重叠, prev_end = 2
[2,3]: 2 ≥ 2 → 不重叠, prev_end = 3
[1,3]: 1 < 3 → 重叠! remove_count = 1
       (移除[1,3], 因为它的终点3比prev_end=3没有更早)
[3,4]: 3 ≥ 3 → 不重叠, prev_end = 4

答案 = 1
```

### 代码

```python
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0

    # 按终点排序（关键！）
    intervals.sort(key=lambda x: x[1])

    prev_end = float('-inf')
    remove_count = 0

    for start, end in intervals:
        if start >= prev_end:
            # 不重叠：保留（更新 prev_end）
            prev_end = end
        else:
            # 重叠：移除当前区间
            remove_count += 1

    return remove_count
```

### 复杂度

- **时间**: O(n log n) — 排序
- **空间**: O(1)

---

## 区间问题总结

### 核心套路

```
区间问题 = 排序 + 一次遍历

排序选择:
- 按起点排序 → Merge Intervals, Insert Interval
- 按终点排序 → Non-overlapping Intervals（贪心保留结束最早的）
```

### 判断两个区间是否重叠

```python
# 区间 A=[a1,a2], B=[b1,b2]
# 重叠条件: a1 <= b2 and b1 <= a2
# 不重叠: a2 < b1 (A完全在B左边) 或 b2 < a1 (B完全在A左边)
```

### 常见题型

| 题目 | 排序方式 | 策略 |
|------|---------|------|
| Merge Intervals | 按起点 | 合并重叠的区间 |
| Insert Interval | 已排序 | 三分：左→合并→右 |
| Non-overlapping | 按终点 | 贪心保留最早结束的 |
| Meeting Rooms | 按起点 | 检查是否有重叠 |
| Meeting Rooms II | 起点+终点分开 | 扫描线 |

---

## 完成整个计划的下一步

恭喜！你已经完成了 Day 1-24 的所有分类题目。接下来的 Day 25-30 是你的复习和模拟面试时间：

### Day 25-26：综合复习
- 重新做每个分类的错题
- 重点复习 DP、Graph、Tree 这些高频区域
- 确保每道 Easy 题 5-10 分钟内完成，Medium 15-25 分钟

### Day 27-28：模拟面试
随机组合以下题目，限时 45 分钟完成 2 Medium + 1 Hard：
- 从各分类中随机抽题 → 不允许看题解 → 自己写 → 跑测试

### Day 29：查漏补缺
- 回顾你的笔记，找到最薄弱的部分
- 用 LeetCode 的 "Related Topics" 找类似题目加练

### Day 30：最后回顾
- 浏览所有题解文件
- 重点看每道题的"易错点"
- 复习算法模板（回溯模板、DP 五步法、BFS 模板等）
- 睡个好觉，保持自信

---

> 所有题解文件列表：[arrays-hashing.md](arrays-hashing.md) | [two-pointers.md](two-pointers.md) | [sliding-window.md](sliding-window.md) | [stack.md](stack.md) | [binary-search.md](binary-search.md) | [linked-list.md](linked-list.md) | [trees.md](trees.md) | [recursion-backtracking.md](recursion-backtracking.md) | [dynamic-programming.md](dynamic-programming.md) | [greedy.md](greedy.md) | [graphs.md](graphs.md) | [heap.md](heap.md) | [intervals.md](intervals.md)
