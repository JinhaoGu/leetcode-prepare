# Heap / Priority Queue (堆 / 优先队列)

> **Day 22-23** | 3 题 | 难度: Medium - Hard
>
> 堆是一种特殊的完全二叉树，能在 O(log n) 时间内插入和取出最值（最大/最小）。Python 的 `heapq` 是最小堆。

---

## Python 堆基础

```python
import heapq

# 最小堆（默认）
heap = []
heapq.heappush(heap, 3)   # 插入
heapq.heappush(heap, 1)
heapq.heappush(heap, 2)
heapq.heappop(heap)       # 弹出最小值 → 1

# 最大堆（取反）
heapq.heappush(heap, -num)  # 存负数
max_val = -heapq.heappop(heap)  # 取出时再取反

# 自定义排序（元组按第一个元素排序）
heapq.heappush(heap, (priority, item))
```

---

## 目录

1. [Kth Largest Element in an Array (数组中的第K个最大元素)](#1-kth-largest-element-in-an-array-medium) — Medium
2. [Find Median from Data Stream (数据流的中位数)](#2-find-median-from-data-stream-hard) — Hard
3. [Merge K Sorted Lists (合并K个升序链表)](#3-merge-k-sorted-lists-hard) — Hard

---

## 1. Kth Largest Element in an Array (Medium)

**LeetCode 215. Kth Largest Element in an Array** | 🌟🌟🌟 高频

### 题目

找到数组中第 k 大的元素（不是第 k 小的）。

```
输入: nums = [3, 2, 1, 5, 6, 4], k = 2
输出: 5
解释: 排序后 [6, 5, 4, 3, 2, 1]，第2大是5
```

所有解法：

---

### 方法一：直接排序（最简单）

**思路**：排序后直接取倒数第 k 个。

```python
def findKthLargest(nums, k):
    nums.sort()
    return nums[-k]  # 第 k 大 = 升序排序后的倒数第 k 个
```

- **时间**: O(n log n)
- **空间**: O(1) 或 O(n) — 取决于排序实现
- **评价**: 最简单但不是最优，面试官可能让你优化

---

### 方法二：最小堆 ⭐ 面试推荐

**思路**：维护一个大小为 k 的最小堆。堆顶始终是"k 个最大元素中最小的" = 第 k 大。

- 堆中元素 < k 个 → 直接加入
- 堆已有 k 个 → 如果当前数 > 堆顶，弹出堆顶，加入当前数
- 最后堆顶就是第 k 大

```
nums = [3, 2, 1, 5, 6, 4], k = 2

i=0, num=3: 堆=[3]
i=1, num=2: 堆=[2,3] (堆顶=2)
i=2, num=1: 堆已满2个, 1<堆顶2 → 忽略
i=3, num=5: 5>堆顶2 → 弹出2, 加入5 → 堆=[3,5]
i=4, num=6: 6>堆顶3 → 弹出3, 加入6 → 堆=[5,6]
i=5, num=4: 4<堆顶5 → 忽略

堆顶 = 5 → 第2大的元素 ✓
```

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

- **时间**: O(n log k) — 每个元素最多一次 push + 一次 pop
- **空间**: O(k)

---

### 方法三：快速选择 (Quick Select) — 平均 O(n)

**思路**：类似快速排序的分区思想。每次选一个 pivot，把比 pivot 小的放左边、大的放右边。如果 pivot 刚好在第 (n-k) 位 → 找到；如果 n-k 在 pivot 左边 → 递归左边；否则递归右边。

```
nums = [3,2,1,5,6,4], k=2, target=4 (len=6, 6-2=4)

分区后: [3,2,1,4,6,5], pivot位置=3
3 < 4 → 递归右半部分
右半: [6,5], pivot=5, 位置=4
4 == 4 → nums[4]=5 → 答案!
```

```python
import random

def findKthLargest(nums, k):
    target = len(nums) - k  # 第 k 大 = 排序后下标 target

    def quickSelect(left, right):
        # 随机选 pivot 防退化为 O(n²)
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

- **时间**: 平均 O(n)，最坏 O(n²) — 随机 pivot 可避免最坏
- **空间**: O(log n) — 递归栈

---

### 三种方法对比

| 方法 | 时间 | 空间 | 何时用 |
|------|------|------|--------|
| 排序 | O(n log n) | O(1) | 最简单，适合快速验证 |
| 最小堆 | O(n log k) | O(k) | k 很小时最优 |
| 快速选择 | O(n) 平均 | O(log n) | 追求最优时间复杂度 |

---

## 2. Find Median from Data Stream (Hard)

**LeetCode 295. Find Median from Data Stream** | 🌟🌟🌟 经典设计题

### 题目

设计一个数据结构，持续接收数字，并能随时返回当前所有数字的中位数。

```
输入: addNum(1), addNum(2), findMedian()→1.5, addNum(3), findMedian()→2
```

### 思路讲解

**核心思想**：用两个堆，把数据分成两半。

- 小的一半放在**最大堆** `small`（取反实现）
- 大的一半放在**最小堆** `large`

保持 `len(small) >= len(large)`，最多差 1。

```
中位数:
- 如果 small 比 large 多 1 个 → 中位数 = small 的堆顶
- 如果一样多 → 中位数 = (small顶 + large顶) / 2

存储示例: addNum(1) → addNum(2) → addNum(3)

addNum(1): small=[1], large=[] → 中位数=1
addNum(2): small=[1], large=[2] → 中位数=(1+2)/2=1.5
addNum(3): small=[1,2], large=[3] → 中位数=2
```

**插入流程**（稍微有点绕，但保证平衡）：
1. 先把新数插入 large（最小堆）
2. 从 large 弹出最小的 → 插入 small（最大堆）
3. 如果 small 比 large 多太多（差 > 1），从 small 弹出最大的 → 插入 large

这样保证了 small 存小数，large 存大数，且大小平衡。

### 代码

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []  # 最大堆（存较小的半数，用负数实现）
        self.large = []  # 最小堆（存较大的半数）

    def addNum(self, num):
        # 1. 先插入 large
        heapq.heappush(self.large, num)

        # 2. 把 large 的最小值转移到 small
        heapq.heappush(self.small, -heapq.heappop(self.large))

        # 3. 保持 small 不比 large 多超过 1
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))

    def findMedian(self):
        if len(self.small) > len(self.large):
            return -self.small[0]  # small 堆顶（最大值，取反回来）
        else:
            return (-self.small[0] + self.large[0]) / 2
```

### 一步步理解

```
addNum(5):
  large=[5] → 弹出5到small → small=[-5], large=[]
  len(small)=1, len(large)=0 → 平衡
  中位数 = 5

addNum(3):
  large=[3] → 弹出3到small → small=[-5,-3], large=[]
  len(small)=2 > len(large)+1=1 → 弹出small顶到large
  small=[-3], large=[5]  → 平衡
  中位数 = (-3+5)/2 = 4

addNum(7):
  large=[5,7] → 弹出5到small → small=[-5,-3], large=[7]
  len(small)=2, len(large)=1 → 平衡
  中位数 = 3 (small顶取反)
```

### 复杂度

- `addNum`: O(log n)
- `findMedian`: O(1)

---

## 3. Merge K Sorted Lists (Hard)

**LeetCode 23. Merge K Sorted Lists** | 🌟🌟🌟 高频

### 题目

合并 k 个已排序链表，返回一个新的排序链表。

```
输入: [1→4→5, 1→3→4, 2→6]
输出: 1→1→2→3→4→4→5→6
```

### 思路讲解

**方法：最小堆**

把 k 个链表的头节点放入最小堆。每次弹出最小的，接到结果链表上，然后把弹出节点的下一个节点加入堆。

```
初始堆（按节点值排序）:
lists = [1→4→5, 1→3→4, 2→6]
堆中有: 1(head1), 1(head2), 2(head3)

弹出 1(head1) → 结果: 1, 加入 head1.next=4
堆: [1(head2), 2(head3), 4]

弹出 1(head2) → 结果: 1→1, 加入 head2.next=3
堆: [2(head3), 3, 4]

弹出 2(head3) → 结果: 1→1→2, 加入 head3.next=6
堆: [3, 4, 6]

弹出 3 → 结果: 1→1→2→3, 加入 4
堆: [4, 4, 6]

...重复直到堆空
```

### 代码

```python
import heapq

def mergeKLists(lists):
    heap = []

    # 把每个链表的头节点存入堆（注意处理重复值的问题）
    for i, head in enumerate(lists):
        if head:
            # 用 (值, 索引, 节点) 避免比较节点对象
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

**为什么要用 `(val, i, node)` 三元组？**
- 堆比较元组时按顺序比较：先比 val，再比 i，再比 node
- 如果两个节点值相同，直接比较 node 对象会报错（Python 3 不能比较 ListNode）
- i 是用来避免比较 node 对象的

### 分治法（备选方案）

也可以两两合并（类似归并排序）：

```python
def mergeKLists(lists):
    if not lists:
        return None
    if len(lists) == 1:
        return lists[0]

    mid = len(lists) // 2
    left = mergeKLists(lists[:mid])
    right = mergeKLists(lists[mid:])
    return mergeTwoLists(left, right)  # 复用 Day 10 的合并函数
```

### 复杂度

- 堆法：时间 O(N log k)，空间 O(k)，N 是所有节点总数
- 分治：时间 O(N log k)，空间 O(log k)（递归栈）

---

## 堆总结

### Python heapq 速查

```python
import heapq

heap = []                     # 创建空堆
heapq.heappush(heap, x)       # O(log n) 插入
heapq.heappop(heap)           # O(log n) 弹出最小值
heapq.heapify(arr)            # O(n) 将数组转为堆
heapq.heappushpop(heap, x)    # 插入并弹出（比先push再pop快）
heapq.nlargest(k, arr)        # 最大的k个
heapq.nsmallest(k, arr)       # 最小的k个
```

### 常见题型

| 题型 | 堆类型 | 代表题 |
|------|--------|--------|
| Top K | 最小堆(大小为k) | Kth Largest |
| 中位数 | 双堆(对顶堆) | Find Median |
| 合并K个有序 | 最小堆 | Merge K Lists |
| 调度/任务 | 最小堆(按时间排序) | 进阶题 |

### 堆的识别信号

- "第 K 大/小" → Top K 问题 → 堆
- "数据流 / 动态维护最值" → 堆
- "合并 K 个有序序列" → 堆
