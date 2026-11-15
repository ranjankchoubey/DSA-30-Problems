# Step 02 · 682. Baseball Game (Easy)

[← Previous: Valid Parentheses](01-lc0020-valid-parentheses.md) · [Roadmap](00-roadmap.md) · [Next: Store the min with each push →](03-concept-store-min-with-each-push.md)

**Practise:** [LeetCode 682](https://leetcode.com/problems/baseball-game/)
**Skill it adds:** pushing values **computed from the top**, so a pop undoes them with no recalculation.

## Problem in brief

You get a list of operation strings, always valid:
- an integer: record that score
- `"+"`: record the sum of the last two scores
- `"D"`: record double the last score
- `"C"`: cancel (remove) the last score

Return the total of the scores that remain.

## Key idea

The scores live on a stack, because every operation only looks at or removes the **most recent** scores.

| Operation | Stack action |
| --- | --- |
| number | `push(int(op))` |
| `"+"` | `push(stack[-1] + stack[-2])` |
| `"D"` | `push(2 * stack[-1])` |
| `"C"` | `pop()` |

The `"+"` and `"D"` results are stored, not recomputed later. After a `"C"`, the stack is exactly what it was before that score was added.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [stack basics](../../course/primers/04-stack.md#stack-basics)
3. [LeetCode Editorial](https://leetcode.com/problems/baseball-game/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+baseball+game)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def calPoints(self, operations: List[str]) -> int:
        stack = []
        for op in operations:
            if op == '+':
                stack.append(stack[-1] + stack[-2])
            elif op == 'D':
                stack.append(2 * stack[-1])
            elif op == 'C':
                stack.pop()
            else:
                stack.append(int(op))
        return sum(stack)
```

</details>

**Time:** O(n) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `["5","2","C","D","+"]` | `30` (scores 5, 10, 15) |
| `["5","-2","4","C","D","9","+","+"]` | `27` (scores 5, −2, −4, 9, 5, 14) |
| `["1","C"]` | `0` |

## Pitfall

Check for `"+"`, `"D"` and `"C"` **before** calling `int(op)`. Negative scores like `"-2"` are numbers, so don't test the first character for a sign.

## What changes in the next step

Here each pushed value is worked out from the top at push time, and `"C"` undoes it for free. Step 03 does the same with the **minimum**: work out `min_so_far` when pushing and store it next to the value, so a pop also undoes the minimum.
