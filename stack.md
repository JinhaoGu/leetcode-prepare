# Stack (栈)

> **Day 7** | 4 题 | 难度: Easy - Medium
>
> 栈是一种**后进先出（LIFO）**的数据结构。只要问题涉及"括号匹配"、"最近相关"、"撤销"等概念，首先想到栈。

---

## 目录

1. [Valid Parentheses (有效的括号)](#1-valid-parentheses-easy) — Easy
2. [Min Stack (最小栈)](#2-min-stack-medium) — Medium
3. [Evaluate Reverse Polish Notation (逆波兰表达式求值)](#3-evaluate-reverse-polish-notation-medium) — Medium
4. [Daily Temperatures (每日温度)](#4-daily-temperatures-medium) — Medium

---

## 1. Valid Parentheses (Easy)

**LeetCode 20. Valid Parentheses** | 🌟 面试必问

### 题目

给定只包含 `(){}[]` 的字符串，判断括号是否有效。

```
输入: "()[]{}"  → True
输入: "([)]"    → False
输入: "{[]}"    → True
```

### 思路讲解

**核心**：遇到左括号就入栈，遇到右括号就检查栈顶是否匹配。

栈正好保证了"后遇到的左括号必须先闭合"这个嵌套关系。

```
"{ [ ] }"

{ → 入栈: [{]
[ → 入栈: [{, []
] → 右括号，栈顶是[，匹配 ✓，弹出: [{]
} → 右括号，栈顶是{，匹配 ✓，弹出: []
栈空 → 有效 ✓
```

**不合法的情况**：
```
"([)]"
( → [{(}]
[ → [{(}, {[]
) → 右圆括号，但栈顶是[，不匹配! → False
```

### 代码

```python
def isValid(s):
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}

    for ch in s:
        if ch in pairs.values():  # 左括号
            stack.append(ch)
        else:  # 右括号
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()

    return len(stack) == 0  # 栈必须为空
```

### 复杂度

- **时间**: O(n)
- **空间**: O(n)

### 易错点

- 最后要检查栈是否为空（多余的左括号）
- 右括号时栈不能为空（多余的右括号）

---

## 2. Min Stack (Medium)

**LeetCode 155. Min Stack** | 🌟🌟 设计题，技巧性强

### 题目

设计一个栈，支持 `push, pop, top, getMin` 四个操作，且 `getMin` 必须在 O(1) 时间内完成。

### 思路讲解

**难点**：普通的栈无法在 O(1) 获取最小值。pop 之后最小值可能会变！

**技巧**：用两个栈。
- `main_stack`：正常存元素
- `min_stack`：存**到当前为止的最小值**

```
push(5): main=[5],  min=[5]       ← 最小值是5
push(3): main=[5,3], min=[5,3]    ← 最小值变成3
push(7): main=[5,3,7], min=[5,3,3] ← 最小值还是3
push(1): main=[5,3,7,1], min=[5,3,3,1] ← 最小值变成1

pop():   main=[5,3,7], min=[5,3,3]   ← 最小值回到3
getMin(): return min_stack.top() = 3 ✓
```

**关键**：push 时，如果新元素 ≤ 当前 min_stack 栈顶，压入新元素；否则重复压入当前的 min。

### 代码

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
            # 取较小值压入 min_stack
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

**LeetCode 150. Evaluate Reverse Polish Notation** | 🌟🌟

### 题目

计算逆波兰表达式（Reverse Polish Notation / RPN）的值。

逆波兰表达式：运算符写在操作数之后。例如 `2 + 3` 写作 `["2", "3", "+"]`。

```
输入: ["2", "1", "+", "3", "*"]
输出: 9
解释: (2 + 1) * 3 = 9
```

### 思路讲解

**计算规则**：
- 遇到数字 → 入栈
- 遇到运算符 → 弹出栈顶两个数，计算，结果入栈

```
["2", "1", "+", "3", "*"]

push 2:  [2]
push 1:  [2, 1]
遇到 '+': pop 1, pop 2 → 2+1=3 → push 3: [3]
push 3:  [3, 3]
遇到 '*': pop 3, pop 3 → 3×3=9 → push 9: [9]

最终栈顶 = 9
```

### 代码

```python
def evalRPN(tokens):
    stack = []
    ops = {
        '+': lambda a, b: a + b,
        '-': lambda a, b: a - b,
        '*': lambda a, b: a * b,
        '/': lambda a, b: int(a / b),  # 向零截断
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

**易错**：除法这里用 `int(a / b)` 而不是 `a // b`。Python 的 `//` 向负无穷取整，而题目要求向零截断。
```
-3 // 2 = -2  (Python)
int(-3 / 2) = -1  (正确)
```

---

## 4. Daily Temperatures (Medium)

**LeetCode 739. Daily Temperatures** | 🌟🌟 单调栈经典

### 题目

给定每天的温度，返回需要等多少天才能遇到更高的温度。

```
输入: [73, 74, 75, 71, 69, 72, 76, 73]
输出: [1,  1,  4,  2,  1,  1,  0,  0 ]
解释:
  第0天(73) → 第1天(74)更高，等1天
  第2天(75) → 第6天(76)更高，等4天
  第6天(76) → 后面没有更高，结果0
```

### 思路讲解

**暴力法**：对每天，向后搜索第一个更高温度 → O(n²)

**单调栈法**：维护一个从栈底到栈顶**递减**的栈，存放"还没找到答案的日子"。

遍历每一天，当前温度比栈顶温度高时，说明栈顶那些天找到了答案。

```
temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
栈中存下标

day0(73): 栈空 → 入栈: [0]
day1(74): 74 > 73(栈顶) → 0号出栈, answer[0]=1-0=1 → 入栈: [1]
day2(75): 75 > 74(栈顶) → 1号出栈, answer[1]=2-1=1 → 入栈: [2]
day3(71): 71 < 75(栈顶) → 入栈: [2,3]
day4(69): 69 < 71(栈顶) → 入栈: [2,3,4]
day5(72): 72 > 69(栈顶) → 4号出栈, answer[4]=5-4=1
          72 > 71(栈顶) → 3号出栈, answer[3]=5-3=2
          72 < 75(栈顶) → 入栈: [2,5]
day6(76): 76 > 72(栈顶) → 5号出栈, answer[5]=6-5=1
          76 > 75(栈顶) → 2号出栈, answer[2]=6-2=4
          栈空 → 入栈: [6]
day7(73): 73 < 76(栈顶) → 入栈: [6,7]

最后栈中 [6,7] 表示没有更高温度的日子, answer[6]=0, answer[7]=0

结果: [1, 1, 4, 2, 1, 1, 0, 0]
```

### 代码

```python
def dailyTemperatures(temperatures):
    n = len(temperatures)
    answer = [0] * n
    stack = []  # 存下标，对应温度递减

    for i, temp in enumerate(temperatures):
        # 当前温度比栈顶温度高 → 找到了栈顶天的答案
        while stack and temp > temperatures[stack[-1]]:
            prev_day = stack.pop()
            answer[prev_day] = i - prev_day
        stack.append(i)

    return answer
```

### 复杂度

- **时间**: O(n) — 每个元素入栈出栈各一次
- **空间**: O(n)

### 单调栈的核心思想

**单调栈** = 栈中元素保持某种单调性（递增/递减），当新元素打破单调性时，弹出栈顶来处理。

```
本题：找"下一个更大的"
→ 单调递减栈（栈顶最小）
→ 新元素更大时，不断弹出栈顶（它们找到了答案）

类似题：找"下一个更小的"
→ 单调递增栈（栈顶最大）
→ 新元素更小时，不断弹出栈顶
```

---

## 栈总结

| 题型 | 技巧 | 代表题 |
|------|------|--------|
| 括号匹配 | 左括号入栈，右括号匹配 | Valid Parentheses |
| 多状态栈 | 额外栈存历史最值 | Min Stack |
| 表达式求值 | 数字入栈，遇操作符计算 | Evaluate RPN |
| 单调栈 | 维护递增/递减，找"下一个更大/更小" | Daily Temperatures |

### 什么时候用栈？

- 遇到"匹配"、"嵌套"、"成对出现" → 栈
- 需要"撤销"或"回到之前状态" → 栈
- 找"下一个更大/更小的元素" → 单调栈
