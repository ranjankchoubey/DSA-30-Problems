# Step 01 · 150. Evaluate Reverse Polish Notation (Medium)

[← Roadmap](00-roadmap.md) · [Next: Basic Calculator II →](02-lc0227-basic-calculator-ii.md)

**Practise:** [LeetCode 150](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
**Skill it adds:** evaluating an expression with a stack of numbers, and dividing with truncation toward zero.

## Problem in brief

You get the tokens of an expression in **postfix** order, where each operator comes after its two operands (`["2","1","+"]` means `2 + 1`). Operators are `+ - * /`, and division truncates toward zero. The expression is always valid. Return its value.

## Key idea

Numbers wait on a stack. An operator pops the top two, combines them, and pushes the result. The **first** pop is the **right** operand.

Postfix needs no precedence rules or parentheses: the order of operations is already fixed by the token order. That's why compilers turn infix into postfix.

| Division | Python | Result for `-7` and `2` |
| --- | --- | --- |
| floor | `a // b` | −4 ✗ |
| truncate toward zero | `int(a / b)` | **−3** ✓ |

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [expression evaluation](../../course/primers/04-stack.md#expression-evaluation)
3. [LeetCode Editorial](https://leetcode.com/problems/evaluate-reverse-polish-notation/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+evaluate+reverse+polish+notation)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        stack = []
        for t in tokens:
            if t in {'+', '-', '*', '/'}:
                b = stack.pop()           # right operand (pushed last)
                a = stack.pop()
                if t == '+':
                    stack.append(a + b)
                elif t == '-':
                    stack.append(a - b)
                elif t == '*':
                    stack.append(a * b)
                else:
                    stack.append(int(a / b))   # truncate toward zero
            else:
                stack.append(int(t))
        return stack[-1]
```

</details>

**Time:** O(n) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `["2","1","+","3","*"]` | `9` ((2 + 1) × 3) |
| `["4","13","5","/","+"]` | `6` (4 + 13 / 5) |
| `["10","6","9","3","+","-11","*","/","*","17","+","5","+"]` | `22` |

## Pitfall

- `"-11"` is a number, not an operator. Test the whole token against the set of operators, not its first character.
- Swapping `a` and `b` breaks `-` and `/`.
- `int(a / b)` goes through a float, which is exact for 32-bit values. For huge integers, see `trunc_div` in the primer.

## What changes in the next step

Real input is **infix**, like `3+2*2`. The operator now sits **between** its operands, and `*` must happen before `+` even though `+` comes first. Step 02 handles that precedence with a single stack.
