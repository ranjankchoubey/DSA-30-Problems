# Step 03 · 1249. Minimum Remove to Make Valid Parentheses (Medium)

[← Previous: Minimum Add to Make Parentheses Valid](02-lc0921-minimum-add-to-make-parentheses-valid.md) · [Roadmap](00-roadmap.md) · [Target: Longest Valid Parentheses →](../NOTES.md)

**Practise:** [LeetCode 1249](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/)
**Skill it adds:** a stack of **indices** that pinpoints the brackets that can never be matched, which are exactly where valid runs break.

## Problem in brief

The string holds lowercase letters, `(` and `)`. Delete as few brackets as possible so the result is valid, and return any such result.

## Key idea

Run step 01's matching, but push the **index** of each `(`:
- `(` → push `i`
- `)` with a non-empty stack → pop (matched)
- `)` with an empty stack → index `i` can never be matched: mark it for removal
- at the end, every index still on the stack is a `(` that was never closed: mark those too

Rebuild the string, skipping the marked indices. Letters are simply copied.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [stack basics](../../course/primers/04-stack.md#stack-basics)
3. [LeetCode Editorial](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+minimum+remove+to+make+valid+parentheses)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def minRemoveToMakeValid(self, s: str) -> str:
        stack = []                        # indices of '(' not matched yet
        remove = set()
        for i, ch in enumerate(s):
            if ch == '(':
                stack.append(i)
            elif ch == ')':
                if stack:
                    stack.pop()
                else:
                    remove.add(i)         # ')' with nothing to match
        remove.update(stack)              # '(' never closed
        return ''.join(ch for i, ch in enumerate(s) if i not in remove)
```

</details>

**Time:** O(n) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `"lee(t(c)o)de)"` | `"lee(t(c)o)de"` |
| `"a)b(c)d"` | `"ab(c)d"` |
| `"))(("` | `""` |

## Pitfall

Don't build the result by deleting characters from the string one by one (each deletion is O(n), so O(n²) overall). Collect the bad indices, then join once.

## What changes in the target

In Longest Valid Parentheses there are no letters, and we want the longest **piece between** the bad indices. For `")()())"` the bad indices are 0 and 5, so the longest valid piece is 1..4, of length 4.

Instead of collecting all bad indices first, the target keeps the **most recent** bad index at the bottom of the stack as a "base" and measures `i − base` each time a `)` is matched → [NOTES](../NOTES.md).
