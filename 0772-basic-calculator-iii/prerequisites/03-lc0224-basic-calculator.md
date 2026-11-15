# Step 03 · 224. Basic Calculator (Hard)

[← Previous: Basic Calculator II](02-lc0227-basic-calculator-ii.md) · [Roadmap](00-roadmap.md) · [Next: Two stacks and operator precedence →](04-concept-two-stacks-operator-precedence.md)

**Practise:** [LeetCode 224](https://leetcode.com/problems/basic-calculator/)
**Skill it adds:** handling parentheses by **pushing the current context** at `(` and popping it at `)`, so a stack replaces recursion.

## Problem in brief

The string holds non-negative integers, `+`, `−`, parentheses and spaces. It is always valid, and `−` may be unary (`"-1"`, `"-(2+3)"`). Evaluate it without `eval`.

## Key idea

With only `+` and `−`, the value is a running `result` plus `sign × number` for each number.
- digit → build `num`
- `+` / `−` → add `sign * num` to `result`, then set `sign` to `+1` or `−1`
- `(` → push `(result, sign)` and start fresh: `result = 0`, `sign = +1`
- `)` → finish the inner `result`, pop `(saved_result, saved_sign)`, and set `result = saved_result + saved_sign * result`

The stack holds exactly what recursion would keep on the call stack. Unary minus needs no special code: in `"-(2+3)"` the `−` just sets `sign = −1` before the `(`.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [expression evaluation](../../course/primers/04-stack.md#expression-evaluation)
3. [LeetCode Editorial](https://leetcode.com/problems/basic-calculator/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+basic+calculator)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def calculate(self, s: str) -> int:
        stack = []                        # saved (result, sign) for each open '('
        result, sign, num = 0, 1, 0
        for ch in s:
            if ch.isdigit():
                num = num * 10 + int(ch)
            elif ch in '+-':
                result += sign * num
                sign = 1 if ch == '+' else -1
                num = 0
            elif ch == '(':
                stack.append((result, sign))
                result, sign = 0, 1
            elif ch == ')':
                result += sign * num
                saved_result, saved_sign = stack.pop()
                result = saved_result + saved_sign * result
                num = 0
        return result + sign * num
```

</details>

**Time:** O(n) · **Space:** O(depth of nesting)

## Test cases

| Input | Expected |
| --- | --- |
| `" 2-1 + 2 "` | `3` |
| `"(1+(4+5+2)-3)+(6+8)"` | `23` |
| `"-(2+3)"` | `-5` (unary minus) |

## Pitfall

Reset `num = 0` after `)`. The inner value has already been folded into `result`, so a leftover `num` would be added a second time.

## What changes in the next step

Step 02 has precedence but no parentheses. Step 03 has parentheses but no precedence. Bolting `*` and `/` onto this code gets messy. Step 04 introduces the general method, **two stacks** (numbers and operators), on step 02's input first.
