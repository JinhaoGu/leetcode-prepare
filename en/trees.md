# Trees

> **Day 12-13** | 7 problems | Difficulty: Easy - Medium
>
> Trees are one of the most central interview data structures. Focus: DFS (recursion), BFS (level-order), BST (binary search tree properties).

---

## Table of Contents

1. [Maximum Depth of Binary Tree](#1-maximum-depth-of-binary-tree-easy) — Easy
2. [Invert Binary Tree](#2-invert-binary-tree-easy) — Easy
3. [Same Tree](#3-same-tree-easy) — Easy
4. [Subtree of Another Tree](#4-subtree-of-another-tree-easy) — Easy
5. [Binary Tree Level Order Traversal](#5-binary-tree-level-order-traversal-medium) — Medium
6. [Validate Binary Search Tree](#6-validate-binary-search-tree-medium) — Medium
7. [Lowest Common Ancestor of BST](#7-lowest-common-ancestor-of-bst-medium) — Medium

---

## DFS vs BFS

```
       1
      / \
     2   3
    / \   \
   4   5   6

DFS (depth-first): 1→2→4→5→3→6
 - Preorder: root→left→right (1,2,4,5,3,6)
 - Inorder: left→root→right (4,2,5,1,3,6)
 - Postorder: left→right→root (4,5,2,6,3,1)

BFS (breadth-first): 1→2→3→4→5→6 (level by level)
```

---

## 1. Maximum Depth of Binary Tree (Easy)

**LeetCode 104. Maximum Depth of Binary Tree** |  Most fundamental tree problem

### Problem

Find the maximum depth (nodes from root to farthest leaf).

### Method 1: DFS Recursion ⭐

```python
def maxDepth(root):
    if not root:
        return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```

### Method 2: BFS Level-Order

```python
from collections import deque

def maxDepth(root):
    if not root:
        return 0
    queue = deque([root])
    depth = 0
    while queue:
        depth += 1
        for _ in range(len(queue)):
            node = queue.popleft()
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return depth
```

---

## 2. Invert Binary Tree (Easy)

**LeetCode 226. Invert Binary Tree** | 

### Problem

Invert a binary tree (swap left and right children at every node).

### Code

```python
def invertTree(root):
    if not root:
        return None
    root.left, root.right = root.right, root.left
    invertTree(root.left)
    invertTree(root.right)
    return root
```

---

## 3. Same Tree (Easy)

**LeetCode 100. Same Tree**

```python
def isSameTree(p, q):
    if not p and not q:
        return True
    if not p or not q:
        return False
    if p.val != q.val:
        return False
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

---

## 4. Subtree of Another Tree (Easy)

**LeetCode 572. Subtree of Another Tree**

### Problem

Check if `subRoot` is a subtree of `root`.

### Code

```python
def isSubtree(root, subRoot):
    if not root:
        return False
    if isSameTree(root, subRoot):  # reuse Same Tree logic
        return True
    return isSubtree(root.left, subRoot) or isSubtree(root.right, subRoot)
```

---

## 5. Binary Tree Level Order Traversal (Medium)

**LeetCode 102. Binary Tree Level Order Traversal** |  High-frequency BFS

### Problem

Return the level-by-level traversal of a binary tree.

### Code

```python
from collections import deque

def levelOrder(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):  # process one level at a time
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

---

## 6. Validate Binary Search Tree (Medium)

**LeetCode 98. Validate Binary Search Tree** |  High frequency

### Method 1: Range Validation ⭐

```python
def isValidBST(root):
    def validate(node, min_val, max_val):
        if not node:
            return True
        if node.val <= min_val or node.val >= max_val:
            return False
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))

    return validate(root, float('-inf'), float('inf'))
```

### Method 2: Inorder Traversal

BST inorder traversal must be strictly increasing.

```python
def isValidBST(root):
    prev = float('-inf')

    def inorder(node):
        nonlocal prev
        if not node:
            return True
        if not inorder(node.left):
            return False
        if node.val <= prev:
            return False
        prev = node.val
        return inorder(node.right)

    return inorder(root)
```

---

## 7. Lowest Common Ancestor of BST (Medium)

**LeetCode 235. LCA of BST** | 

### Problem

Find the lowest common ancestor (LCA) of two nodes in a BST.

### Method 1: BST Property

```python
def lowestCommonAncestor(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left
        elif p.val > root.val and q.val > root.val:
            root = root.right
        else:
            return root  # split point → this is the LCA
```

### Method 2: General Binary Tree LCA

```python
# LeetCode 236 — for any binary tree (not just BST)
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root
    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)
    if left and right:
        return root
    return left or right
```

---

## Tree Summary

### DFS Recursive Template

```python
def dfs(root):
    if not root:
        return ...
    left = dfs(root.left)
    right = dfs(root.right)
    return ...  # combine results
```

### BFS Level-Order Template

```python
from collections import deque

def bfs(root):
    if not root:
        return
    queue = deque([root])
    while queue:
        for _ in range(len(queue)):  # level isolation
            node = queue.popleft()
            # process node
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
```

### DFS vs BFS Decision

| Scenario | Method |
|----------|--------|
| Depth, paths, property checks | DFS (more natural recursively) |
| Level-order, shortest path | BFS |
| BST problems | DFS + leverage BST ordering |
| Separate processing per level | BFS (`for _ in range(len(queue))`) |
