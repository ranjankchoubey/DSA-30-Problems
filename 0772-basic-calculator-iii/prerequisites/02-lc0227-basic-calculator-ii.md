# Step 02 · 227. Basic Calculator II (Medium)

[← Previous: Evaluate Reverse Polish Notation](01-lc0150-evaluate-reverse-polish-notation.md) · [Roadmap](00-roadmap.md) · [Next: Basic Calculator →](03-lc0224-basic-calculator.md)

**Practise:** [LeetCode 227](https://leetcode.com/problems/basic-calculator-ii/)
**Skill it adds:** handling `* /` before `+ -` with one stack, and truncating toward zero when the left side is negative.

## Problem in brief

The string holds non-negative integers, `+ - * /` and spaces, but **no parentheses**. It is always valid. Evaluate it without `eval`; integer division truncates toward zero.

## Key idea

Think of the expression as **terms** joined by `+` and `−`: `3 + 2*2 − 8/4` is `3`, `+4` and `−2`.

Keep `op` = the operator **before** the current number. When a number ends (at the next operator or the end of the string):

| `op` | Stack action |
| --- | --- |
| `+` | push `num` |
| `-` | push `-num` |
| `*` | push `stack.pop() * num` |
| `/` | push `int(stack.pop() / num)` |

`*` and `/` combine with the previous term **at once**, so they happen before any `+` or `−`: that's precedence. The answer is `sum(stack)`.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [expression evaluation](../../course/primers/04-stack.md#expression-evaluation)
3. [LeetCode Editorial](https://leetcode.com/problems/basic-calculator-ii/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+basic+calculator+ii)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def calculate(self, s: str) -> int:
        stack = []                        # signed terms
        num = 0
        op = '+'                          # the operator before the current number
        for i, ch in enumerate(s):
            if ch.isdigit():
                num = num * 10 + int(ch)
            if ch in '+-*/' or i == len(s) - 1:
                if op == '+':
                    stack.append(num)
                elif op == '-':
                    stack.append(-num)
                elif op == '*':
                    stack.append(stack.pop() * num)
                else:
                    stack.append(int(stack.pop() / num))
                op = ch
                num = 0
        return sum(stack)
```

</details>

**Time:** O(n) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `"3+2*2"` | `7` |
| `" 3/2 "` | `1` |
| `"1-7/2"` | `-2` (terms `1` and `−7/2 = −3`) |

## Pitfall

In `"1-7/2"` the stack holds `−7` when the division happens. `-7 // 2` is `−4`, which gives the wrong answer `−3`. Truncation `int(-7 / 2) = −3` gives the right answer `−2`.

## What changes in the next step

There are no parentheses yet. Step 03 adds parentheses (with only `+` and `−`) by **saving the current state on a stack** when `(` opens and restoring it when `)` closes.
