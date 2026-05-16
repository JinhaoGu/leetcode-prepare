# Heap / Priority Queue

> **Day 22-23** | 3 problems | Difficulty: Medium - Hard
>
> A heap is a complete binary tree enabling O(log n) insertion and min/max extraction. Python's `heapq` is a min-heap.

---

## Table of Contents

1. [Kth Largest Element in an Array](#1-kth-largest-element-in-an-array-medium) — Medium
2. [Find Median from Data Stream](#2-find-median-from-data-stream-hard) — Hard
3. [Merge K Sorted Lists](#3-merge-k-sorted-lists-hard) — Hard

---

## Python Heap Basics

```python
import heapq

heap = []                    # empty heap
heapq.heappush(heap, x)      # O(log n) insert
heapq.heappop(heap)          # O(log n) pop min

# Max heap: negate values
heapq.heappush(heap, -x)
max_val = -heapq.heappop(heap)

# With priority
heapq.heappush(heap, (priority, item))
```

---

## 1. Kth Largest Element in an Array (Medium)

**LeetCode 215. Kth Largest Element in an Array** |  High frequency

### Method 1: Sorting (Simplest)

```python
def findKthLargest(nums, k):
    nums.sort()
    return nums[-k]
```
- Time: O(n log n), Space: O(1)

### Method 2: Min Heap ⭐ Recommended

Maintain a min-heap of size k. The heap top is the answer.

```python
import heapq

def findKthLargest(nums, k):
    heap = []
    for num in nums:
        heapq.heappush(heap, num)
        if len(heap) > k:
            heapq.heappop(heap)
    return heap[0]
```
- Time: O(n log k), Space: O(k)

### Method 3: Quick Select — Average O(n)

Like quicksort partitioning — only recurse into the half containing the answer.

```python
import random

def findKthLargest(nums, k):
    target = len(nums) - k

    def quickSelect(left, right):
        pivot_idx = random.randint(left, right)
        nums[pivot_idx], nums[right] = nums[right], nums[pivot_idx]
        pivot = nums[right]
        p = left
        for i in range(left, right):
            if nums[i] <= pivot:
                nums[i], nums[p] = nums[p], nums[i]
                p += 1
        nums[p], nums[right] = nums[right], nums[p]
        if p == target:
            return nums[p]
        elif p < target:
            return quickSelect(p + 1, right)
        else:
            return quickSelect(left, p - 1)

    return quickSelect(0, len(nums) - 1)
```
- Time: O(n) average, O(n²) worst; Space: O(log n)

---

## 2. Find Median from Data Stream (Hard)

**LeetCode 295. Find Median from Data Stream** |  Classic design problem

### Approach: Two Heaps (Dual-Heap)

- Small half → max heap (store negatives)
- Large half → min heap
- Keep sizes balanced: `len(small) == len(large)` or `len(small) == len(large) + 1`

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []  # max heap (negated)
        self.large = []  # min heap

    def addNum(self, num):
        heapq.heappush(self.large, num)
        heapq.heappush(self.small, -heapq.heappop(self.large))
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))

    def findMedian(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        else:
            return (-self.small[0] + self.large[0]) / 2
```

---

## 3. Merge K Sorted Lists (Hard)

**LeetCode 23. Merge K Sorted Lists** |  High frequency

### Approach: Min Heap

Put all list heads into a min heap. Repeatedly pop the smallest and push its next node.

```python
import heapq

def mergeKLists(lists):
    heap = []
    for i, head in enumerate(lists):
        if head:
            heapq.heappush(heap, (head.val, i, head))

    dummy = ListNode(-1)
    curr = dummy
    while heap:
        val, i, node = heapq.heappop(heap)
        curr.next = node
        curr = curr.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

---

## Heap Summary

| Problem Type | Heap Type | Examples |
|-------------|-----------|----------|
| Top K | Min-heap (size k) | Kth Largest |
| Median | Dual-heap | Find Median |
| Merge K sorted | Min-heap | Merge K Lists |

### Heap Recognition Signals

- "K-th largest/smallest" → Top K → heap
- "Data stream / dynamically maintaining extremes" → heap
- "Merge K sorted sequences" → heap
