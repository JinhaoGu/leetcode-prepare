# Graphs (图)

> **Day 19-21** | 6 题 | 难度: Medium - Hard
>
> 图算法是面试中最复杂的部分之一。核心：DFS 遍历、BFS 最短路径、拓扑排序。

---

## 图的表示

在 LeetCode 中，图通常以以下形式出现：

```python
# 1. 邻接矩阵 (二维网格)
grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]

# 2. 邻接表 (节点 → 邻居列表)
# LeetCode 133: Node 类，有 val 和 neighbors

# 3. 边列表 → 转邻接表
n = 5
edges = [[0,1], [1,2], [3,4]]
# 转成: graph = {0:[1], 1:[0,2], 2:[1], 3:[4], 4:[3]}
```

---

## 目录

1. [Number of Islands (岛屿数量)](#1-number-of-islands-medium) — Medium
2. [Clone Graph (克隆图)](#2-clone-graph-medium) — Medium
3. [Course Schedule (课程表)](#3-course-schedule-medium) — Medium
4. [Pacific Atlantic Water Flow (太平洋大西洋水流)](#4-pacific-atlantic-water-flow-medium) — Medium
5. [Rotting Oranges (腐烂的橘子)](#5-rotting-oranges-medium) — Medium
6. [Word Ladder (单词接龙)](#6-word-ladder-hard) — Hard

---

## 1. Number of Islands (Medium)

**LeetCode 200. Number of Islands** | 🌟🌟🌟 图 DFS/BFS 必刷

### 题目

给定二维网格，'1' 是陆地，'0' 是水。计算岛屿的数量（岛屿被水包围，由相邻陆地水平或垂直连接而成）。

```
输入:
[
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出: 3
```

### 思路讲解

**核心**：遍历每个格子，遇到陆地 '1'，岛屿数 +1，然后用 DFS（或 BFS）把这块岛屿的所有陆地都"淹没"掉（标记为已访问），避免重复计数。

```
找到第一个 '1' 在 (0,0):
  岛屿数 = 1
  DFS 淹没整个岛屿:
    标记 (0,0) → '0'
    探索四周: (0,1)是'1' → 递归标记
              (1,0)是'1' → 递归标记
              ...
    第一块岛屿全变成 '0'

继续扫描，找到 (2,2) '1':
  岛屿数 = 2
  淹没...

继续扫描，找到 (3,3) '1':
  岛屿数 = 3
  淹没...

最终: 3 个岛屿
```

### 代码

```python
# DFS 解法
def numIslands(grid):
    if not grid:
        return 0

    rows, cols = len(grid), len(grid[0])
    count = 0

    def dfs(r, c):
        # 边界检查 + 是否陆地
        if (r < 0 or r >= rows or
            c < 0 or c >= cols or
            grid[r][c] == '0'):
            return

        grid[r][c] = '0'  # 标记为已访问（淹没）

        # 四个方向
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)  # 淹没整个岛屿

    return count


# BFS 解法
from collections import deque

def numIslands(grid):
    if not grid:
        return 0

    rows, cols = len(grid), len(grid[0])
    count = 0

    def bfs(r, c):
        queue = deque([(r, c)])
        grid[r][c] = '0'
        while queue:
            cr, cc = queue.popleft()
            for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
                nr, nc = cr + dr, cc + dc
                if (0 <= nr < rows and 0 <= nc < cols and
                    grid[nr][nc] == '1'):
                    grid[nr][nc] = '0'
                    queue.append((nr, nc))

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                bfs(r, c)

    return count
```

### 复杂度

- **时间**: O(m × n) — 每个格子访问一次
- **空间**: O(m × n) — 最坏递归栈深度（DFS）或队列大小（BFS）

---

## 2. Clone Graph (Medium)

**LeetCode 133. Clone Graph** | 🌟🌟

### 题目

深拷贝一个无向连通图。每个节点包含 `val` 和 `neighbors` 列表。

### 思路讲解

用哈希表记录"原节点 → 克隆节点"的映射，防止重复克隆和循环引用。

DFS 或 BFS 遍历，对每个节点：
1. 如果已经克隆过 → 直接返回克隆节点
2. 如果没克隆过 → 创建新节点，然后递归/迭代克隆所有邻居

### 代码

```python
# DFS 解法
def cloneGraph(node):
    if not node:
        return None

    cloned = {}  # 原节点 → 克隆节点

    def dfs(original):
        if original in cloned:
            return cloned[original]

        # 创建克隆节点（先不处理邻居）
        copy = Node(original.val)
        cloned[original] = copy

        # 递归克隆所有邻居
        for neighbor in original.neighbors:
            copy.neighbors.append(dfs(neighbor))

        return copy

    return dfs(node)


# BFS 解法
from collections import deque

def cloneGraph(node):
    if not node:
        return None

    cloned = {node: Node(node.val)}
    queue = deque([node])

    while queue:
        original = queue.popleft()
        for neighbor in original.neighbors:
            if neighbor not in cloned:
                cloned[neighbor] = Node(neighbor.val)
                queue.append(neighbor)
            # 建立克隆节点之间的连接
            cloned[original].neighbors.append(cloned[neighbor])

    return cloned[node]
```

### 易错点

- 必须在 `cloned` 中一创建节点就记录，递归/迭代前要检查是否已克隆，否则会死循环
- DFS 中先创建节点（邻居先空着），再递归填充邻居

---

## 3. Course Schedule (Medium)

**LeetCode 207. Course Schedule** | 🌟🌟🌟 拓扑排序经典

### 题目

有 n 门课和一些先修要求。判断能否完成所有课程。

```
输入: numCourses = 2, prerequisites = [[1, 0]]
输出: True
解释: 先上 0 再上 1 即可

输入: numCourses = 2, prerequisites = [[1, 0], [0, 1]]
输出: False
解释: 0→1→0 形成循环依赖，不可能完成
```

### 思路讲解

这是经典的**环检测**问题（有向图是否有环）。

**方法一：拓扑排序（BFS / Kahn 算法）**

1. 统计每个节点的入度（指向它的边数）
2. 入度为 0 的节点入队（没有依赖，可以立即学）
3. 每次出队一个节点，把它指向的节点的入度 -1
4. 如果有节点入度变为 0，入队
5. 最后，如果所有节点都出队过（count == n），无环 ✓

```
n=4, edges = [[1,0], [2,0], [3,1], [3,2]]

入度: 0→度为0, 1→度为1, 2→度为1, 3→度为2

队列: [0]
出队0: 1入度-1=0→入队, 2入度-1=0→入队
队列: [1, 2]
出队1: 3入度-1=1
出队2: 3入度-1=0→入队
队列: [3]
出队3
全部出队 → True
```

### 代码

```python
from collections import deque

def canFinish(numCourses, prerequisites):
    # 建图
    graph = [[] for _ in range(numCourses)]
    indegree = [0] * numCourses

    for course, prereq in prerequisites:
        graph[prereq].append(course)  # prereq → course
        indegree[course] += 1

    # 入度为 0 的入队
    queue = deque([i for i in range(numCourses) if indegree[i] == 0])
    completed = 0

    while queue:
        course = queue.popleft()
        completed += 1
        for next_course in graph[course]:
            indegree[next_course] -= 1
            if indegree[next_course] == 0:
                queue.append(next_course)

    return completed == numCourses
```

**方法二：DFS 三色标记法**

- 0 (白色) = 未访问
- 1 (灰色) = 正在访问（在当前递归栈中）
- 2 (黑色) = 已完成

如果在 DFS 中遇到灰色节点 → 有环。

```python
def canFinish(numCourses, prerequisites):
    graph = [[] for _ in range(numCourses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)

    # 0=未访问, 1=访问中, 2=已完成
    state = [0] * numCourses

    def hasCycle(course):
        if state[course] == 1:  # 遇到正在访问的 → 有环
            return True
        if state[course] == 2:  # 已经检查过
            return False

        state[course] = 1  # 标记正在访问
        for next_course in graph[course]:
            if hasCycle(next_course):
                return True
        state[course] = 2  # 标记完成

        return False

    for i in range(numCourses):
        if hasCycle(i):
            return False
    return True
```

### 复杂度

- **时间**: O(V + E)
- **空间**: O(V + E)

---

## 4. Pacific Atlantic Water Flow (Medium)

**LeetCode 417. Pacific Atlantic Water Flow** | 🌟🌟

### 题目

给定一个 m×n 的矩阵表示大陆，左/上边缘靠太平洋，右/下边缘靠大西洋。水从高处往低处或等高处流。找出所有水既能流到太平洋又能流到大西洋的格子。

### 思路讲解

**反向思维**：不从每个格子出发检查，而是反过来——
1. 从太平洋边缘（左上）开始 DFS，标记所有能到太平洋的格子
2. 从大西洋边缘（右下）开始 DFS，标记所有能到大西洋的格子
3. 同时被两个标记的格子就是答案

### 代码

```python
def pacificAtlantic(heights):
    rows, cols = len(heights), len(heights[0])
    pacific = [[False] * cols for _ in range(rows)]
    atlantic = [[False] * cols for _ in range(rows)]

    def dfs(r, c, visited, prev_height):
        if (r < 0 or r >= rows or c < 0 or c >= cols or
            visited[r][c] or heights[r][c] < prev_height):
            return
        visited[r][c] = True
        for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
            dfs(r + dr, c + dc, visited, heights[r][c])

    # 从太平洋边缘（左上）出发
    for r in range(rows):
        dfs(r, 0, pacific, -1)      # 左边缘
    for c in range(cols):
        dfs(0, c, pacific, -1)      # 上边缘

    # 从大西洋边缘（右下）出发
    for r in range(rows):
        dfs(r, cols - 1, atlantic, -1)  # 右边缘
    for c in range(cols):
        dfs(rows - 1, c, atlantic, -1)   # 下边缘

    # 收集结果
    result = []
    for r in range(rows):
        for c in range(cols):
            if pacific[r][c] and atlantic[r][c]:
                result.append([r, c])
    return result
```

**为什么水从低往高流？** 因为我们是反向 DFS（从海洋回溯到大陆），所以条件变成 `heights[r][c] >= prev_height`。

---

## 5. Rotting Oranges (Medium)

**LeetCode 994. Rotting Oranges** | 🌟🌟 BFS 层数

### 题目

网格中 0=空，1=新鲜橘子，2=腐烂橘子。每分钟腐烂橘子会让上下左右的新鲜橘子腐烂。求所有橘子腐烂需要多少分钟，如果不可能全部腐烂返回 -1。

```
输入:
[
  [2, 1, 1],
  [1, 1, 0],
  [0, 1, 1]
]
输出: 4
```

### 思路讲解

**多源 BFS**：所有初始腐烂橘子是第 0 分钟的源头。

每一分钟是一个 BFS 层级。记录 BFS 的层数（分钟数）。

```
初始: 把所有腐烂橘子(2)入队, 记录新鲜橘子数量

第0分钟: 队列中有1个腐烂橘子 (0,0)
第1分钟: 感染(0,1)和(1,0), 新鲜-2, 分钟=1
第2分钟: 感染(0,2)和(1,1), 新鲜-2, 分钟=2
第3分钟: 感染(2,1), 新鲜-1, 分钟=3
第4分钟: 感染(2,2), 新鲜-1, 分钟=4

新鲜=0 → 返回4
```

### 代码

```python
from collections import deque

def orangesRotting(grid):
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh = 0

    # 收集初始腐烂橘子，统计新鲜橘子
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                queue.append((r, c))
            elif grid[r][c] == 1:
                fresh += 1

    if fresh == 0:
        return 0

    minutes = 0
    directions = [(1,0), (-1,0), (0,1), (0,-1)]

    while queue:
        for _ in range(len(queue)):  # 当前分钟的所有腐烂橘子
            r, c = queue.popleft()
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                if (0 <= nr < rows and 0 <= nc < cols and
                    grid[nr][nc] == 1):
                    grid[nr][nc] = 2  # 感染
                    fresh -= 1
                    queue.append((nr, nc))
        minutes += 1

    return minutes - 1 if fresh == 0 else -1
```

**注意**：`minutes - 1` 是因为最后一轮感染完计数 +1 后队列才为空，实际分钟数应该 -1。

---

## 6. Word Ladder (Hard)

**LeetCode 127. Word Ladder** | 🌟🌟🌟 BFS 最短路径

### 题目

从 beginWord 到 endWord，每次只能变一个字母，且中间单词必须在 wordList 中。求最短转换序列长度。

```
输入: beginWord = "hit", endWord = "cog",
     wordList = ["hot","dot","dog","lot","log","cog"]
输出: 5
解释: hit → hot → dot → dog → cog (5个单词)
```

### 思路讲解

这是**图的 BFS 最短路径**问题。每个单词是节点，能通过改变一个字母相互转换的单词之间有边。

**关键优化**：不能每次都对所有 wordList 检查（太慢）。应该生成当前单词的所有可能变换，检查是否在字典中。

```
hit 的所有可能变换:
ait, bit, cit, ..., zit (26种替换h)
hat, hbt, hct, ..., hzt (26种替换i)
hia, hib, hic, ..., hiz (26种替换t)

其中 hot 在 wordList 中 → 入队, 距离=2

hot 的变换中 dot, lot 在 wordList 中 → 入队, 距离=3
...
```

### 代码

```python
from collections import deque

def ladderLength(beginWord, endWord, wordList):
    word_set = set(wordList)
    if endWord not in word_set:
        return 0

    queue = deque([beginWord])
    visited = {beginWord}
    length = 1  # 序列长度（包括beginWord）

    while queue:
        for _ in range(len(queue)):
            word = queue.popleft()

            if word == endWord:
                return length

            # 生成所有可能的变换
            for i in range(len(word)):
                for ch in 'abcdefghijklmnopqrstuvwxyz':
                    next_word = word[:i] + ch + word[i+1:]
                    if next_word in word_set and next_word not in visited:
                        visited.add(next_word)
                        queue.append(next_word)

        length += 1

    return 0  # 无法到达
```

### 复杂度

- **时间**: O(n × 26 × L)，n 是 wordList 长度，L 是单词长度
- **空间**: O(n)

---

## 图总结

### DFS vs BFS 在图中的应用

| 场景 | 方法 | 原因 |
|------|------|------|
| 连通分量 / 岛屿 | DFS / BFS | 都行，DFS 代码更简洁 |
| 最短路径（无权图） | BFS | BFS 天然按距离分层 |
| 环检测（有向图） | DFS 三色 / 拓扑排序 | 两者都可以 |
| 拓扑排序 | BFS (Kahn) / DFS | Kahn 更直观 |
| 多源扩散 | BFS | 多起点同时 BFS |

### 图题通用套路

1. **建图**：确定用什么表示图（邻接表 / 邻接矩阵 / 隐式图）
2. **遍历**：DFS 或 BFS，需要 visited 防止重复
3. **处理**：在遍历过程中完成业务逻辑
