# Step 01 · 20. Valid Parentheses (Easy)

[← Roadmap](00-roadmap.md) · [Next: Baseball Game →](02-lc0682-baseball-game.md)

**Practise:** [LeetCode 20](https://leetcode.com/problems/valid-parentheses/)
**Skill it adds:** a stack remembers the **most recent unmatched** item, and LIFO order matches things up in reverse.

## Problem in brief

The string holds only the six characters `( ) [ ] { }`. Return `True` if every opening bracket is closed by a bracket of the same type, in the right order, and every closing bracket has an opener. Otherwise return `False`.

## Key idea

| Approach | Time | Space |
| --- | --- | --- |
| Keep deleting `()`, `[]`, `{}` until nothing changes | O(n²) | O(n) |
| **Stack of openers** | **O(n)** | **O(n)** |

A closer must match the **most recent** opener that is still open, and that is exactly the top of a stack:
- opener → push it
- closer → the stack must be non-empty **and** its top must be the partner. Pop it.
- end → the stack must be empty (no opener left waiting)

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [stack basics](../../course/primers/04-stack.md#stack-basics)
3. [LeetCode Editorial](https://leetcode.com/problems/valid-parentheses/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+valid+parentheses)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def isValid(self, s: str) -> bool:
        partner = {')': '(', ']': '[', '}': '{'}
        stack = []                        # openers still waiting for a closer
        for ch in s:
            if ch in partner:             # a closer
                if not stack or stack.pop() != partner[ch]:
                    return False
            else:                         # an opener
                stack.append(ch)
        return not stack
```

</details>

**Time:** O(n) · **Space:** O(n)

## Walk-through: `"([)]"`

| ch | action | stack after |
| --- | --- | --- |
| `(` | push | `(` |
| `[` | push | `( [` |
| `)` | top is `[`, but `)` needs `(` | → **False** |

The brackets are balanced in count, but `)` tries to close `(` while `[` is still open. Only the stack's "most recent first" order catches that.

## Test cases

| Input | Expected |
| --- | --- |
| `"()[]{}"` | `True` |
| `"([)]"` | `False` (wrong order) |
| `"(("` | `False` (openers left on the stack) |

## Pitfall

Two checks are easy to forget: a closer arriving when the stack is **empty** (`")"`), and openers **left over** at the end (`"(("`). The `not stack` checks handle both.

## What changes in the next step

Here the stack holds characters and we only compare against the top. In Baseball Game the stack holds scores, and new values are **computed from the top one or two items** before being pushed. That is a stack that carries extra state.
