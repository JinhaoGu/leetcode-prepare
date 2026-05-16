# Graphs

> **Day 19-21** | 6 problems | Difficulty: Medium - Hard
>
> Graph algorithms are among the most complex interview topics. Core: DFS traversal, BFS shortest path, topological sort.

---

## Table of Contents

1. [Number of Islands](#1-number-of-islands-medium) — Medium
2. [Clone Graph](#2-clone-graph-medium) — Medium
3. [Course Schedule](#3-course-schedule-medium) — Medium
4. [Pacific Atlantic Water Flow](#4-pacific-atlantic-water-flow-medium) — Medium
5. [Rotting Oranges](#5-rotting-oranges-medium) — Medium
6. [Word Ladder](#6-word-ladder-hard) — Hard

---

## Graph Representations

```python
# 1. Adjacency matrix (2D grid)
grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
]

# 2. Adjacency list
graph = {0: [1], 1: [0, 2], 2: [1], 3: [4], 4: [3]}

# 3. Edge list → convert to adjacency list
edges = [[0,1], [1,2], [3,4]]
```

---

## 1. Number of Islands (Medium)

**LeetCode 200. Number of Islands** |  Must-know graph DFS/BFS

### Problem

Count islands in a 2D grid ('1' = land, '0' = water). Land connected horizontally/vertically forms one island.

### DFS Solution

```python
def numIslands(grid):
    if not grid:
        return 0
    rows, cols = len(grid), len(grid[0])
    count = 0

    def dfs(r, c):
        if (r < 0 or r >= rows or c < 0 or c >= cols or
            grid[r][c] == '0'):
            return
        grid[r][c] = '0'  # sink the island
        dfs(r+1, c); dfs(r-1, c); dfs(r, c+1); dfs(r, c-1)

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)
    return count
```

### BFS Solution

```python
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
            for dr, dc in [(1,0),(-1,0),(0,1),(0,-1)]:
                nr, nc = cr+dr, cc+dc
                if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == '1':
                    grid[nr][nc] = '0'
                    queue.append((nr, nc))

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                bfs(r, c)
    return count
```

---

## 2. Clone Graph (Medium)

**LeetCode 133. Clone Graph** | 

### Problem

Deep-copy a connected undirected graph.

### DFS Solution

```python
def cloneGraph(node):
    if not node:
        return None
    cloned = {}
    
    def dfs(original):
        if original in cloned:
            return cloned[original]
        copy = Node(original.val)
        cloned[original] = copy
        for neighbor in original.neighbors:
            copy.neighbors.append(dfs(neighbor))
        return copy
    
    return dfs(node)
```

---

## 3. Course Schedule (Medium)

**LeetCode 207. Course Schedule** |  Topological sort classic

### Problem

Given n courses and prerequisite pairs, determine if all courses can be finished (no circular dependencies).

### Method 1: BFS (Kahn's Algorithm)

```python
from collections import deque

def canFinish(numCourses, prerequisites):
    graph = [[] for _ in range(numCourses)]
    indegree = [0] * numCourses
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        indegree[course] += 1

    queue = deque([i for i in range(numCourses) if indegree[i] == 0])
    completed = 0
    while queue:
        course = queue.popleft()
        completed += 1
        for nxt in graph[course]:
            indegree[nxt] -= 1
            if indegree[nxt] == 0:
                queue.append(nxt)
    return completed == numCourses
```

### Method 2: DFS (3-Color)

```python
def canFinish(numCourses, prerequisites):
    graph = [[] for _ in range(numCourses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)
    
    state = [0] * numCourses  # 0=unvisited, 1=visiting, 2=done

    def hasCycle(course):
        if state[course] == 1:
            return True
        if state[course] == 2:
            return False
        state[course] = 1
        for nxt in graph[course]:
            if hasCycle(nxt):
                return True
        state[course] = 2
        return False

    for i in range(numCourses):
        if hasCycle(i):
            return False
    return True
```

---

## 4. Pacific Atlantic Water Flow (Medium)

**LeetCode 417. Pacific Atlantic Water Flow** | 

### Problem

Water flows downhill (to equal or lower heights). Pacific = top/left edges, Atlantic = bottom/right edges. Find cells that can reach both oceans.

### Approach

Reverse DFS: start from ocean edges and flow uphill.

```python
def pacificAtlantic(heights):
    rows, cols = len(heights), len(heights[0])
    pacific = [[False]*cols for _ in range(rows)]
    atlantic = [[False]*cols for _ in range(rows)]

    def dfs(r, c, visited, prev_h):
        if (r<0 or r>=rows or c<0 or c>=cols or
            visited[r][c] or heights[r][c] < prev_h):
            return
        visited[r][c] = True
        for dr, dc in [(1,0),(-1,0),(0,1),(0,-1)]:
            dfs(r+dr, c+dc, visited, heights[r][c])

    for r in range(rows):
        dfs(r, 0, pacific, -1)
        dfs(r, cols-1, atlantic, -1)
    for c in range(cols):
        dfs(0, c, pacific, -1)
        dfs(rows-1, c, atlantic, -1)

    result = []
    for r in range(rows):
        for c in range(cols):
            if pacific[r][c] and atlantic[r][c]:
                result.append([r, c])
    return result
```

---

## 5. Rotting Oranges (Medium)

**LeetCode 994. Rotting Oranges** |  Multi-source BFS

### Problem

Grid: 0=empty, 1=fresh, 2=rotten. Each minute, rotten oranges infect adjacent fresh ones. Return minutes until all fresh rot, or -1 if impossible.

```python
from collections import deque

def orangesRotting(grid):
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                queue.append((r, c))
            elif grid[r][c] == 1:
                fresh += 1

    if fresh == 0:
        return 0

    minutes = 0
    dirs = [(1,0),(-1,0),(0,1),(0,-1)]
    while queue:
        for _ in range(len(queue)):
            r, c = queue.popleft()
            for dr, dc in dirs:
                nr, nc = r+dr, c+dc
                if 0<=nr<rows and 0<=nc<cols and grid[nr][nc] == 1:
                    grid[nr][nc] = 2
                    fresh -= 1
                    queue.append((nr, nc))
        minutes += 1

    return minutes - 1 if fresh == 0 else -1
```

---

## 6. Word Ladder (Hard)

**LeetCode 127. Word Ladder** |  BFS shortest path

### Problem

From beginWord to endWord, change one letter at a time. Each intermediate word must be in wordList. Find the shortest transformation sequence length.

```python
from collections import deque

def ladderLength(beginWord, endWord, wordList):
    word_set = set(wordList)
    if endWord not in word_set:
        return 0

    queue = deque([beginWord])
    visited = {beginWord}
    length = 1

    while queue:
        for _ in range(len(queue)):
            word = queue.popleft()
            if word == endWord:
                return length
            for i in range(len(word)):
                for ch in 'abcdefghijklmnopqrstuvwxyz':
                    nxt = word[:i] + ch + word[i+1:]
                    if nxt in word_set and nxt not in visited:
                        visited.add(nxt)
                        queue.append(nxt)
        length += 1
    return 0
```

---

## Graph Summary

| Scenario | Method | Reason |
|----------|--------|--------|
| Connected components / Islands | DFS or BFS | Both work; DFS code is more concise |
| Shortest path (unweighted) | BFS | BFS naturally visits by distance layers |
| Cycle detection (directed) | DFS (3-color) / Topo sort | Either works |
| Topological sort | BFS (Kahn) / DFS | Kahn is more intuitive |
| Multi-source spread | BFS | Multiple starting points simultaneously |
