# Intervals

> **Day 24** | 3 problems | Difficulty: Medium
>
> Interval problems almost always start with one operation: **sort by start time**. After sorting, relationships between intervals become clear.

---

## Table of Contents

1. [Merge Intervals](#1-merge-intervals-medium) — Medium
2. [Insert Interval](#2-insert-interval-medium) — Medium
3. [Non-overlapping Intervals](#3-non-overlapping-intervals-medium) — Medium

---

## Checking Overlap

```python
# Intervals A=[a1,a2], B=[b1,b2]
# Overlap condition: a1 <= b2 and b1 <= a2
# No overlap: a2 < b1 (A before B) or b2 < a1 (B before A)
```

---

## 1. Merge Intervals (Medium)

**LeetCode 56. Merge Intervals** |  High frequency

### Problem

Merge all overlapping intervals.

```
Input: [[1,3], [2,6], [8,10], [15,18]]
Output: [[1,6], [8,10], [15,18]]
```

### Approach

Sort by start time, then iterate — if current overlaps with previous, merge them.

```python
def merge(intervals):
    if not intervals:
        return []
    intervals.sort(key=lambda x: x[0])
    result = [intervals[0]]
    for start, end in intervals[1:]:
        last_end = result[-1][1]
        if start <= last_end:
            result[-1][1] = max(last_end, end)  # merge
        else:
            result.append([start, end])
    return result
```

---

## 2. Insert Interval (Medium)

**LeetCode 57. Insert Interval** | 

### Problem

Given a **non-overlapping** interval list (sorted by start), insert a new interval and keep it non-overlapping.

```
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```

### Approach

Three phases: left (no overlap before), merge overlap, right (no overlap after).

```python
def insert(intervals, newInterval):
    result = []
    i = 0
    n = len(intervals)
    
    # Left: all intervals ending before newInterval starts
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # Merge: intervals overlapping with newInterval
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)
    
    # Right: all intervals starting after newInterval ends
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result
```

---

## 3. Non-overlapping Intervals (Medium)

**LeetCode 435. Non-overlapping Intervals** | 

### Problem

Find the minimum number of intervals to remove to make the rest non-overlapping.

```
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: remove [1,3]
```

### Approach

**Greedy**: sort by **end time** (not start!). Always keep the interval that ends earliest — this leaves maximum room for remaining intervals.

```python
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0
    intervals.sort(key=lambda x: x[1])  # sort by END!
    prev_end = float('-inf')
    remove_count = 0
    
    for start, end in intervals:
        if start >= prev_end:
            prev_end = end  # keep this interval
        else:
            remove_count += 1  # overlap → remove current
    
    return remove_count
```

---

## Intervals Summary

### Core Pattern

```
Interval problems = Sort + One pass

Sort choice:
- By start time → Merge Intervals, Insert Interval
- By end time → Non-overlapping Intervals (greedy: keep earliest ending)
```

| Problem | Sort By | Strategy |
|---------|---------|----------|
| Merge Intervals | Start | Merge overlapping |
| Insert Interval | Already sorted | Three: left → merge → right |
| Non-overlapping | End | Greedy: keep earliest ending |
