# Linked List

> **Day 10-11** | 5 problems | Difficulty: Easy - Medium
>
> Linked lists are a fundamental data structure. Each node contains a value and a pointer to the next node. Key techniques: dummy node, fast-slow pointers, reversing in place.

---

## Table of Contents

1. [Reverse Linked List](#1-reverse-linked-list-easy) — Easy
2. [Merge Two Sorted Lists](#2-merge-two-sorted-lists-easy) — Easy
3. [Linked List Cycle](#3-linked-list-cycle-easy) — Easy
4. [Remove Nth Node From End of List](#4-remove-nth-node-from-end-of-list-medium) — Medium
5. [Reorder List](#5-reorder-list-medium) — Medium

---

## 1. Reverse Linked List (Easy)

**LeetCode 206. Reverse Linked List** |  Must-know

### Problem

Reverse a singly linked list.

```
Input:  1 → 2 → 3 → 4 → 5 → NULL
Output: 5 → 4 → 3 → 2 → 1 → NULL
```

### Method 1: Iterative ⭐ Recommended

Use three pointers `prev, curr, next_temp`. At each step, make the current node point to its predecessor.

```python
def reverseList(head):
    prev = None
    curr = head
    while curr:
        next_temp = curr.next  # save next
        curr.next = prev       # reverse
        prev = curr            # advance prev
        curr = next_temp       # advance curr
    return prev
```

### Method 2: Recursive

```python
def reverseList(head):
    if not head or not head.next:
        return head
    new_head = reverseList(head.next)
    head.next.next = head  # next node points back to me
    head.next = None       # my next becomes None
    return new_head
```

---

## 2. Merge Two Sorted Lists (Easy)

**LeetCode 21. Merge Two Sorted Lists** |  Fundamental

### Problem

Merge two sorted linked lists into one sorted list.

```
Input: 1→2→4, 1→3→4
Output: 1→1→2→3→4→4
```

### Approach

Use a **dummy node** to simplify edge cases. Compare `l1.val` vs `l2.val`, take the smaller one, advance.

```python
def mergeTwoLists(l1, l2):
    dummy = ListNode(-1)
    curr = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    curr.next = l1 if l1 else l2  # append remainder
    return dummy.next
```

---

## 3. Linked List Cycle (Easy)

**LeetCode 141. Linked List Cycle** |  Fast-slow pointer classic

### Problem

Determine if a linked list has a cycle.

### Approach

**Floyd's Cycle Detection (tortoise and hare)**: slow moves 1 step, fast moves 2 steps. If there's a cycle, they will eventually meet.

```python
def hasCycle(head):
    slow = head
    fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

---

## 4. Remove Nth Node From End of List (Medium)

**LeetCode 19. Remove Nth Node From End of List** | 

### Problem

Remove the n-th node from the end of the list.

```
Input: 1→2→3→4→5, n=2
Output: 1→2→3→5
```

### Approach

Use two pointers spaced n apart. When fast reaches the end, slow is just before the node to delete.

```python
def removeNthFromEnd(head, n):
    dummy = ListNode(-1, head)
    fast = slow = dummy
    for _ in range(n + 1):
        fast = fast.next
    while fast:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next  # delete
    return dummy.next
```

---

## 5. Reorder List (Medium)

**LeetCode 143. Reorder List** |  Combines three techniques

### Problem

Reorder `L0→L1→...→Ln-1→Ln` to `L0→Ln→L1→Ln-1→L2→Ln-2→...`

```
Input: 1→2→3→4→5
Output: 1→5→2→4→3
```

### Approach (Three Steps)

1. Find middle (fast-slow pointers)
2. Reverse second half
3. Merge two halves alternately

```python
def reorderList(head):
    if not head or not head.next:
        return

    # Step 1: Find middle
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next

    # Step 2: Reverse second half
    prev, curr = None, slow.next
    slow.next = None  # split
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt

    # Step 3: Merge alternately
    first, second = head, prev
    while second:
        tmp1, tmp2 = first.next, second.next
        first.next = second
        second.next = tmp1
        first, second = tmp1, tmp2
```

---

## Linked List Summary

| Technique | Use Case | Example |
|-----------|----------|---------|
| Dummy node | Simplify head operations | Merge, Remove Nth |
| Fast-slow pointers | Find middle, detect cycle | Cycle, Reorder List |
| Three-pointer reverse | Reverse list | Reverse List |
| Spaced pointers | Find k-th from end | Remove Nth |
