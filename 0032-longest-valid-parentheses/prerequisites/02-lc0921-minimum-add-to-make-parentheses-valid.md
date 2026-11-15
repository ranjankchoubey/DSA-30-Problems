# Step 02 · 921. Minimum Add to Make Parentheses Valid (Medium)

[← Previous: Valid Parentheses](01-lc0020-valid-parentheses.md) · [Roadmap](00-roadmap.md) · [Next: Minimum Remove to Make Valid Parentheses →](03-lc1249-minimum-remove-to-make-valid-parentheses.md)

**Practise:** [LeetCode 921](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/)
**Skill it adds:** replacing the stack with a **balance counter** when there is only one bracket type, and spotting a `)` that can never be matched.

## Problem in brief

The string holds only `(` and `)`. You may insert brackets anywhere. Return the smallest number of insertions that makes the string valid.

## Key idea

With one bracket type, the stack from step 01 would only ever hold `(`, so its **size** is all that matters. Use a counter:
- `(` → `open_count += 1`
- `)` with `open_count > 0` → it matches one, so `open_count -= 1`
- `)` with `open_count == 0` → nothing before it can match it, and nothing after it can help. It needs an inserted `(`: `unmatched_close += 1`.

At the end, each leftover `(` needs an inserted `)`. The answer is `open_count + unmatched_close`.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [stack basics](../../course/primers/04-stack.md#stack-basics)
3. [LeetCode Editorial](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+minimum+add+to+make+parentheses+valid)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def minAddToMakeValid(self, s: str) -> int:
        open_count = 0                    # '(' still waiting for a ')'
        unmatched_close = 0               # ')' that nothing can match
        for ch in s:
            if ch == '(':
                open_count += 1
            elif open_count > 0:
                open_count -= 1
            else:
                unmatched_close += 1
        return open_count + unmatched_close
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `"())"` | `1` |
| `"((("` | `3` |
| `"()))(("` | `4` (two unmatched `)`, two leftover `(`) |

## Pitfall

A single `balance = opens − closes` checked only at the end is **not** enough: `"))(("` ends with balance 0 but needs 4 insertions. A `)` that would make the balance negative must be counted and **reset**, not carried.

## What changes in the next step

The counter tells you **how many** brackets are unmatched, not **where** they are. Step 03 has to delete them, so it needs their positions: the stack goes back, holding **indices**.

Keep the "reset when `)` can't be matched" rule in mind: it is the left-to-right pass of the O(1) method in the target.
