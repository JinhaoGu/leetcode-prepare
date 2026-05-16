# Stack

> **Day 7** | 4 problems | Difficulty: Easy - Medium
>
> A stack is a **Last-In-First-Out (LIFO)** data structure. Whenever a problem involves "matching brackets", "recently related elements", or "undo", think stack first.

---

## Table of Contents

1. [Valid Parentheses](#1-valid-parentheses-easy) — Easy
2. [Min Stack](#2-min-stack-medium) — Medium
3. [Evaluate Reverse Polish Notation](#3-evaluate-reverse-polish-notation-medium) — Medium
4. [Daily Temperatures](#4-daily-temperatures-medium) — Medium

---

## 1. Valid Parentheses (Easy)

**LeetCode 20. Valid Parentheses** |  Must-know

### Problem

Given a string containing only `(){}[]`, determine if the brackets are valid.

```
Input: "()[]{}"  → True
Input: "([)]"    → False
Input: "{[]}"    → True
```

### Approach

Push left brackets, match right brackets against the stack top.
The stack guarantees the nesting order: "last opened must close first".

### Code

```python
def isValid(s):
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in pairs.values():  # left bracket
            stack.append(ch)
        else:  # right bracket
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()
    return len(stack) == 0  # must be empty at the end
```

- **Time**: O(n)
- **Space**: O(n)

---

## 2. Min Stack (Medium)

**LeetCode 155. Min Stack** |  Design problem with a clever trick

### Problem

Design a stack supporting `push, pop, top, getMin` — all in O(1).

### Approach

Maintain two stacks: the main stack and a `min_stack` tracking the minimum value up to each point.

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val):
        self.stack.append(val)
        if not self.min_stack:
            self.min_stack.append(val)
        else:
            self.min_stack.append(min(val, self.min_stack[-1]))

    def pop(self):
        self.stack.pop()
        self.min_stack.pop()

    def top(self):
        return self.stack[-1]

    def getMin(self):
        return self.min_stack[-1]
```

---

## 3. Evaluate Reverse Polish Notation (Medium)

**LeetCode 150. Evaluate Reverse Polish Notation** | 

### Problem

Evaluate an expression in Reverse Polish Notation (operators follow operands).

```
Input: ["2", "1", "+", "3", "*"]
Output: 9
Explanation: (2 + 1) * 3 = 9
```

### Approach

- Number → push to stack
- Operator → pop top two, compute, push result back

### Code

```python
def evalRPN(tokens):
    stack = []
    ops = {
        '+': lambda a, b: a + b,
        '-': lambda a, b: a - b,
        '*': lambda a, b: a * b,
        '/': lambda a, b: int(a / b),  # truncate toward zero
    }
    for token in tokens:
        if token in ops:
            b = stack.pop()
            a = stack.pop()
            stack.append(ops[token](a, b))
        else:
            stack.append(int(token))
    return stack[0]
```

**Pitfall**: Use `int(a / b)` not `a // b` — Python's `//` floors toward negative infinity, but the problem requires truncation toward zero.

---

## 4. Daily Temperatures (Medium)

**LeetCode 739. Daily Temperatures** |  Monotonic stack classic

### Problem

Given daily temperatures, return how many days to wait for a warmer day.

```
Input: [73, 74, 75, 71, 69, 72, 76, 73]
Output: [1,  1,  4,  2,  1,  1,  0,  0]
```

### Approach

**Monotonic stack**: maintain a decreasing-temperature stack (stores indices). When current temp is higher than the stack's top, those indices have found their answer.

```python
def dailyTemperatures(temperatures):
    n = len(temperatures)
    answer = [0] * n
    stack = []  # indices, corresponding temps are decreasing

    for i, temp in enumerate(temperatures):
        while stack and temp > temperatures[stack[-1]]:
            prev_day = stack.pop()
            answer[prev_day] = i - prev_day
        stack.append(i)
    return answer
```

- **Time**: O(n) — each element pushed & popped once
- **Space**: O(n)

### Monotonic Stack Insight

- Find "next greater" → decreasing monotonic stack (stack top is smallest)
- Find "next smaller" → increasing monotonic stack (stack top is largest)

---

## Stack Summary

| Problem Type | Technique | Example |
|-------------|-----------|---------|
| Bracket matching | Push left, match right | Valid Parentheses |
| Multi-state stack | Extra stack for history | Min Stack |
| Expression evaluation | Push numbers, compute on operator | Evaluate RPN |
| Monotonic stack | Maintain monotonic order | Daily Temperatures |
