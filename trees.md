# Trees (树)

> **Day 12-13** | 7 题 | 难度: Easy - Medium
>
> 树是面试中最核心的数据结构之一。重点掌握：DFS（递归）、BFS（层序遍历）、BST（二叉搜索树的特性）。

---

## 树的节点定义

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

## DFS vs BFS

```
       1
      / \
     2   3
    / \   \
   4   5   6

DFS (深度优先): 1→2→4→5→3→6  (一条路走到黑)
 - 前序: 根→左→右 (1,2,4,5,3,6)
 - 中序: 左→根→右 (4,2,5,1,3,6)
 - 后序: 左→右→根 (4,5,2,6,3,1)

BFS (广度优先): 1→2→3→4→5→6  (一层一层来)
 - 层序遍历 (Level Order)
```

---

## 目录

1. [Maximum Depth of Binary Tree (二叉树的最大深度)](#1-maximum-depth-of-binary-tree-easy) — Easy
2. [Invert Binary Tree (翻转二叉树)](#2-invert-binary-tree-easy) — Easy
3. [Same Tree (相同的树)](#3-same-tree-easy) — Easy
4. [Subtree of Another Tree (另一棵树的子树)](#4-subtree-of-another-tree-easy) — Easy
5. [Binary Tree Level Order Traversal (二叉树的层序遍历)](#5-binary-tree-level-order-traversal-medium) — Medium
6. [Validate Binary Search Tree (验证二叉搜索树)](#6-validate-binary-search-tree-medium) — Medium
7. [Lowest Common Ancestor of BST (二叉搜索树的最近公共祖先)](#7-lowest-common-ancestor-of-bst-medium) — Medium

---

## 1. Maximum Depth of Binary Tree (Easy)

**LeetCode 104. Maximum Depth of Binary Tree** | 🌟 树的最基础题

### 题目

求二叉树的最大深度（从根到最远叶子的节点数）。

```
     3
    / \
   9  20
      / \
     15  7

最大深度 = 3
```

### 思路讲解

**方法一：DFS 递归（最常用）**

树的深度 = 1 + max(左子树深度, 右子树深度)，递归到底部。

```
maxDepth(3)
= 1 + max(maxDepth(9), maxDepth(20))
= 1 + max(1, 1 + max(maxDepth(15), maxDepth(7)))
= 1 + max(1, 1 + max(1, 1))
= 1 + max(1, 2)
= 3
```

**方法二：BFS 层序遍历**
逐层遍历，每层深度+1。

### 代码

```python
# DFS 递归
def maxDepth(root):
    if not root:
        return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))

# BFS 迭代
from collections import deque

def maxDepth(root):
    if not root:
        return 0
    queue = deque([root])
    depth = 0
    while queue:
        depth += 1
        for _ in range(len(queue)):  # 处理当前层的所有节点
            node = queue.popleft()
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return depth
```

### 递归理解三步法

1. **终止条件**：`if not root: return 0`
2. **递归调用**：分别求左右子树的深度
3. **合并结果**：`1 + max(左深度, 右深度)`

---

## 2. Invert Binary Tree (Easy)

**LeetCode 226. Invert Binary Tree** | 🌟 翻转二叉树（Max Howell 因为没写出这题被 Google 拒了）

### 题目

翻转二叉树（交换每个节点的左右子树）。

```
     4                  4
    / \                / \
   2   7      →       7   2
  / \ / \            / \ / \
 1  3 6  9          9  6 3  1
```

### 思路讲解

对每个节点，交换它的左右孩子，然后递归处理左右子树。

```
invertTree(4):
  交换 4.left 和 4.right: 左=7, 右=2
  invertTree(7): 交换 6 和 9
  invertTree(2): 交换 1 和 3
完成
```

### 代码

```python
def invertTree(root):
    if not root:
        return None

    # 交换左右子树
    root.left, root.right = root.right, root.left

    # 递归翻转子树
    invertTree(root.left)
    invertTree(root.right)

    return root
```

---

## 3. Same Tree (Easy)

**LeetCode 100. Same Tree**

### 题目

判断两棵二叉树是否完全相同。

### 思路讲解

两棵树相同，必须满足：
1. 根节点值相同
2. 左子树相同
3. 右子树相同

递归比较。

### 代码

```python
def isSameTree(p, q):
    # 都为空 → 相同
    if not p and not q:
        return True
    # 一个空一个不空 → 不同
    if not p or not q:
        return False
    # 值不同 → 不同
    if p.val != q.val:
        return False
    # 递归比较左右子树
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

---

## 4. Subtree of Another Tree (Easy)

**LeetCode 572. Subtree of Another Tree**

### 题目

判断二叉树 `subRoot` 是不是 `root` 的子树。

```
root:      3        subRoot:   4
          / \                  / \
         4   5                1   2
        / \
       1   2

输出: True (subRoot 是 root 的左子树)
```

### 思路讲解

在 `root` 中遍历每个节点，以它为根和 `subRoot` 比较是否相同。

1. 如果 `isSameTree(root, subRoot)` → 直接返回 True
2. 否则递归判断 `subRoot` 是不是左子树的子树 或 是不是右子树的子树

### 代码

```python
def isSubtree(root, subRoot):
    if not root:
        return False
    if isSameTree(root, subRoot):
        return True
    return isSubtree(root.left, subRoot) or isSubtree(root.right, subRoot)

# 复用 Same Tree 的 isSameTree 函数
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

## 5. Binary Tree Level Order Traversal (Medium)

**LeetCode 102. Binary Tree Level Order Traversal** | 🌟🌟 高频 BFS

### 题目

层序遍历二叉树（按层输出）。

```
     3
    / \
   9  20
      / \
     15  7

输出: [[3], [9, 20], [15, 7]]
```

### 思路讲解

用**队列**实现 BFS。关键：**记录当前层的节点数**，一次性处理一层。

```
队列初始: [3]

第0层: len=1 → 处理3 → 加入3的左右孩子 → [[3]]
队列: [9, 20]

第1层: len=2 → 处理9 → 加null → 处理20 → 加15,7 → [[3],[9,20]]
队列: [15, 7]

第2层: len=2 → 处理15,7 → [[3],[9,20],[15,7]]
```

### 代码

```python
from collections import deque

def levelOrder(root):
    if not root:
        return []

    result = []
    queue = deque([root])

    while queue:
        level = []
        for _ in range(len(queue)):  # 关键：当前层的节点数
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)

    return result
```

### 复杂度

- **时间**: O(n) — 每个节点访问一次
- **空间**: O(n) — 队列中最多放一层节点（最坏 n/2）

---

## 6. Validate Binary Search Tree (Medium)

**LeetCode 98. Validate Binary Search Tree** | 🌟🌟🌟 高频

### 题目

判断一棵树是否是有效的**二叉搜索树（BST）**。

BST 的定义：
- 左子树**所有节点**的值 < 根节点的值
- 右子树**所有节点**的值 > 根节点的值
- 左右子树也是 BST

```
    5
   / \
  1   4
      / \
     3   6

输出: False
解释: 4 的右子树中有 3 < 4，但 3 在 5 的右子树中且 3 < 5，不满足 BST
```

### 思路讲解

**易错点**：只比较根和左右孩子是不够的！需要保证整个左子树的所有节点都小于根。

```
     5
    / \
   1   8
      / \
     3   9   ← 3 < 5，不是 BST! 只检查 3<8 会漏判
```

**正确方法**：传递一个**合法的值范围** (min_val, max_val)。

```
validate(root, min_val, max_val):
  如果 node.val <= min_val 或 >= max_val → False
  递归左子树: validate(left, min_val, node.val)
  递归右子树: validate(right, node.val, max_val)
```

```
validate(5, -∞, +∞):
  5 在(-∞,+∞) ✓
  validate(1, -∞, 5): 1在(-∞,5) ✓
  validate(8, 5, +∞): 8在(5,+∞) ✓
    validate(3, 5, 8): 3在(5,8) ✗ → False!
```

### 代码

```python
def isValidBST(root):
    def validate(node, min_val, max_val):
        if not node:
            return True

        if node.val <= min_val or node.val >= max_val:
            return False

        # 左子树：范围 (min_val, node.val)
        # 右子树：范围 (node.val, max_val)
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))

    return validate(root, float('-inf'), float('inf'))
```

**中序遍历法（另一种思路）**：BST 的中序遍历结果一定是递增的。

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

**LeetCode 235. Lowest Common Ancestor of a Binary Search Tree** | 🌟🌟

### 题目

在 BST 中找两个节点的**最近公共祖先（LCA）**。

LCA 定义：深度最深的、同时是 p 和 q 的祖先的节点。

```
      6
     / \
    2   8
   /|   |\
  0 4   7 9
    /|
   3 5

p=2, q=8 → LCA = 6
p=2, q=4 → LCA = 2 (2 自己是自己的祖先)
```

### 思路讲解

利用 BST 的特性，不需要遍历整棵树：

- 如果 p 和 q 都小于 root → LCA 一定在左子树
- 如果 p 和 q 都大于 root → LCA 一定在右子树
- 否则（一个在左一个在右，或其中一个等于 root）→ root 就是 LCA

```
p=0, q=5

root=6: 0<6, 5<6 → 都在左，LCA在左子树(2)
root=2: 0<2, 5>2 → 一分左右! root=2 就是 LCA
```

### 代码

```python
def lowestCommonAncestor(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left   # 都在左边
        elif p.val > root.val and q.val > root.val:
            root = root.right  # 都在右边
        else:
            return root  # 分开了，当前就是LCA
```

### 如果是普通二叉树（非 BST）

```python
# LeetCode 236. 普通二叉树的 LCA
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root

    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)

    if left and right:
        return root  # p 和 q 分别在左右子树
    return left or right  # 哪边有返回哪边
```

---

## 树的总结

### DFS 递归模板（最重要！）

```python
def dfs(root):
    if not root:           # 终止条件
        return ...

    left_result = dfs(root.left)   # 处理左子树
    right_result = dfs(root.right) # 处理右子树

    return ... # 合并左右结果
```

### BFS 层序遍历模板

```python
from collections import deque

def bfs(root):
    if not root:
        return
    queue = deque([root])
    while queue:
        for _ in range(len(queue)):  # 层隔离
            node = queue.popleft()
            # 处理 node
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
```

### 什么时候用 DFS vs BFS？

| 场景 | 方法 |
|------|------|
| 求深度、路径、判断性质 | DFS（递归更自然） |
| 层序遍历、最短路径 | BFS |
| BST 问题 | DFS + 利用 BST 有序性 |
| 每层分开处理 | BFS（`for _ in range(len(queue))` 隔离层） |
