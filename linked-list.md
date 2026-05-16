# Linked List (链表)

> **Day 10-11** | 5 题 | 难度: Easy - Medium
>
> 链表是最基础的数据结构之一，每个节点包含值和指向下一个节点的指针。重点掌握：哨兵节点 (dummy node)、快慢指针、反转链表。

---

## 链表节点定义（Python）

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

---

## 目录

1. [Reverse Linked List (反转链表)](#1-reverse-linked-list-easy) — Easy
2. [Merge Two Sorted Lists (合并两个有序链表)](#2-merge-two-sorted-lists-easy) — Easy
3. [Linked List Cycle (环形链表)](#3-linked-list-cycle-easy) — Easy
4. [Remove Nth Node From End of List (删除链表的倒数第 N 个节点)](#4-remove-nth-node-from-end-of-list-medium) — Medium
5. [Reorder List (重排链表)](#5-reorder-list-medium) — Medium

---

## 1. Reverse Linked List (Easy)

**LeetCode 206. Reverse Linked List** | 🌟🌟🌟 必刷，面试高频

### 题目

反转一个单链表。

```
输入:  1 → 2 → 3 → 4 → 5 → NULL
输出:  5 → 4 → 3 → 2 → 1 → NULL
```

### 思路讲解

**迭代法（最常用）**：用三个指针 `prev, curr, next`。

核心操作：每次让当前节点指向它的前一个节点。

```
初始: NULL  1 → 2 → 3 → 4 → 5
       ↑    ↑
      prev  curr

Step 1: 保存 next_temp = 2
       让 1 → NULL
       移动 prev, curr 各前进一步

       NULL ← 1    2 → 3 → 4 → 5
               ↑    ↑
              prev  curr

Step 2: 保存 next_temp = 3
       让 2 → 1
       移动

       NULL ← 1 ← 2    3 → 4 → 5
                    ↑    ↑
                   prev  curr

...重复直到 curr = NULL，返回 prev（新头节点）
```

一步步来：
```python
# 初始状态
prev = None  # 前一个节点
curr = head  # 当前节点

# 循环中
next_temp = curr.next  # 1. 先保存下一个节点，防止断链
curr.next = prev       # 2. 反转：当前节点指向前一个
prev = curr            # 3. prev 前进
curr = next_temp       # 4. curr 前进
```

### 代码

```python
def reverseList(head):
    prev = None
    curr = head

    while curr:
        next_temp = curr.next  # 保存下一个
        curr.next = prev       # 反转指针
        prev = curr            # prev 前移
        curr = next_temp       # curr 前移

    return prev  # prev 就是新的头节点
```

### 递归法（理解即可）

```python
def reverseList(head):
    # 递归终止：空链表或只有一个节点
    if not head or not head.next:
        return head

    # 递归反转后面的链表
    new_head = reverseList(head.next)

    # 把当前节点接到后面
    head.next.next = head  # 让下一个节点指向自己
    head.next = None       # 自己的 next 置空

    return new_head
```

递归法图解：
```
reverseList(1→2→3→4→5)
  ├─ reverseList(2→3→4→5)
  │    ├─ reverseList(3→4→5)
  │    │    └─ ...最终返回 new_head = 5
  │    └─ head=2, head.next=3, 3.next=2, 2.next=None → 5→4→3→2
  └─ head=1, head.next=2, 2.next=1, 1.next=None → 5→4→3→2→1
```

### 易错点

- 一定要先保存 `next_temp`，否则改了 `curr.next` 之后找不到下一个节点
- 迭代法返回的是 `prev` 不是 `curr`（循环结束时 curr = None）

---

## 2. Merge Two Sorted Lists (Easy)

**LeetCode 21. Merge Two Sorted Lists** | 🌟 基础

### 题目

合并两个已排序的链表，返回一个新的排序链表。

```
输入: 1→2→4, 1→3→4
输出: 1→1→2→3→4→4
```

### 思路讲解

**核心技巧：哨兵节点 (dummy node)**

创建一个假的头节点 `dummy`，然后用一个 `curr` 指针构建新链表。

```
l1: 1 → 2 → 4
l2: 1 → 3 → 4

dummy → ?

比较 l1.val=1 vs l2.val=1 → 任选一个, curr→l1, l1前进
dummy → 1

比较 l1.val=2 vs l2.val=1 → l2更小
dummy → 1 → 1

比较 l1.val=2 vs l2.val=3 → l1更小
dummy → 1 → 1 → 2

...继续
dummy → 1 → 1 → 2 → 3 → 4 → 4

返回 dummy.next
```

### 代码

```python
def mergeTwoLists(l1, l2):
    dummy = ListNode(-1)  # 哨兵节点
    curr = dummy

    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next

    # 连接剩余链表
    curr.next = l1 if l1 else l2

    return dummy.next  # 真正的头节点
```

### 哨兵节点 (Dummy Node) 的作用

哨兵节点是链表题中最常用的技巧：
- 简化头节点的特殊处理
- 避免讨论"链表为空"的边界情况
- 最终返回 `dummy.next`

---

## 3. Linked List Cycle (Easy)

**LeetCode 141. Linked List Cycle** | 🌟🌟 快慢指针经典

### 题目

判断链表中是否有环。

```
输入: 3→2→0→-4
         ↑_____↓
输出: True (2→0→-4→2 形成环)
```

### 思路讲解

**经典解法：Floyd 判圈算法（龟兔赛跑）**

- 慢指针（乌龟）每次走 1 步
- 快指针（兔子）每次走 2 步
- 如果有环，两指针一定会相遇
- 如果没环，快指针会先到达 NULL

```
3 → 2 → 0 → -4
    ↑_________↓

初始: slow=3, fast=3
step1: slow=2, fast=0
step2: slow=0, fast=2
step3: slow=-4, fast=-4 → 相遇! → 有环
```

**直觉理解**：快指针每次比慢指针多走一步，如果有环，在环里快指针每步追1步，一定能追上。

### 代码

```python
def hasCycle(head):
    slow = head
    fast = head

    while fast and fast.next:
        slow = slow.next        # 走1步
        fast = fast.next.next   # 走2步
        if slow == fast:
            return True

    return False
```

---

## 4. Remove Nth Node From End of List (Medium)

**LeetCode 19. Remove Nth Node From End of List** | 🌟🌟

### 题目

删除链表的倒数第 n 个节点。

```
输入: 1→2→3→4→5, n=2
输出: 1→2→3→5  (删除了倒数第2个节点4)
```

### 思路讲解

**技巧：双指针间隔 n 步**

1. 快指针先走 n 步（或 n+1 步）
2. 然后快慢指针一起走，快指针到 NULL 时，慢指针正好在**要删除节点的前一个**
3. 删除操作：`slow.next = slow.next.next`

```
1 → 2 → 3 → 4 → 5 → NULL, n=2

Step 1: fast 先走 n+1=3 步
slow=1, fast=4

Step 2: 一起走
slow=1, fast=4
slow=2, fast=5
slow=3, fast=NULL → 停止

slow 在3 (要删除的节点4的前一个)
删除: 3.next = 3.next.next (3→4→5 变成 3→5)
```

**为什么用 dummy 节点？** 如果 n = 链表长度（删除头节点），没有 dummy 就不好处理。

### 代码

```python
def removeNthFromEnd(head, n):
    dummy = ListNode(-1, head)  # dummy.next = head
    fast = dummy
    slow = dummy

    # fast 先走 n+1 步
    for _ in range(n + 1):
        fast = fast.next

    # 一起走直到 fast 到末尾
    while fast:
        slow = slow.next
        fast = fast.next

    # 删除 slow.next
    slow.next = slow.next.next

    return dummy.next
```

### 易错点

- `fast` 先走 `n+1` 步，这样 `slow` 停在要删节点的**前一个**
- 用 dummy 处理边界（删除头节点）

---

## 5. Reorder List (Medium)

**LeetCode 143. Reorder List** | 🌟🌟 综合题

### 题目

将链表 `L0→L1→...→Ln-1→Ln` 重排为 `L0→Ln→L1→Ln-1→L2→Ln-2→...`

```
输入: 1→2→3→4
输出: 1→4→2→3

输入: 1→2→3→4→5
输出: 1→5→2→4→3
```

### 思路讲解

这道题综合了三个链表技巧，面试官特别喜欢：

**三步走**：
1. **找中点**（快慢指针）
2. **反转后半部分**
3. **交替合并**前后两部分

```
1→2→3→4→5

Step 1: 找中点
slow=3 (中点), 链表分为: 前半 [1,2,3], 后半 [4,5]

Step 2: 反转后半
[4,5] → [5,4]
前半: 1→2→3
后半: 5→4

Step 3: 交替合并
1→5→2→4→3
```

### 代码

```python
def reorderList(head):
    if not head or not head.next:
        return

    # Step 1: 找中点（快慢指针）
    slow, fast = head, head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    # slow 现在在中点（或中点偏左）

    # Step 2: 反转 slow 之后的链表
    prev, curr = None, slow.next
    slow.next = None  # 断开前后
    while curr:
        next_temp = curr.next
        curr.next = prev
        prev = curr
        curr = next_temp
    # prev 现在是反转后的头

    # Step 3: 交替合并
    first, second = head, prev
    while second:
        temp1, temp2 = first.next, second.next
        first.next = second
        second.next = temp1
        first, second = temp1, temp2
```

### 步步图解

```
原链表: 1 → 2 → 3 → 4 → 5

Step 1 找中点:
slow 最终指向 3 (前半的最后一个)
断开: 前半=1→2→3, 后半=4→5

Step 2 反转后半:
后半 4→5 反转为 5→4

Step 3 合并:
first=1, second=5
1→5, 然后 first=2, second=4
2→4, 然后 first=3, second=None
最终: 1→5→2→4→3
```

---

## 链表总结

### 核心技巧

| 技巧 | 用途 | 代表题 |
|------|------|--------|
| 哨兵节点 (dummy) | 简化头节点操作 | Merge, Remove Nth |
| 快慢指针 | 找中点、判环 | Cycle, Reorder List |
| 三指针反转 | 反转链表 | Reverse List |
| 双指针间隔 | 找倒数第 k 个 | Remove Nth |

### 链表题检查清单

写链表题，完成代码后检查：
- [ ] 空链表/单节点链表是否处理？
- [ ] 头节点的处理是否正确？（用 dummy 可避免此问题）
- [ ] 尾节点的 `next` 是否为 `None`？
- [ ] 是否有断链（丢失对剩余链表的引用）？
