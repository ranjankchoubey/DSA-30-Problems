# Step 03 · 904. Fruit Into Baskets (Medium)

[← Previous: Longest Substring Without Repeating Characters](02-lc0003-longest-substring-without-repeating-characters.md) · [Roadmap](00-roadmap.md) · [Target: Longest Substring with At Most K Distinct Characters →](../NOTES.md)

**Practise:** [LeetCode 904](https://leetcode.com/problems/fruit-into-baskets/)
**Skill it adds:** a **count map** inside the window, so `len(count)` is the number of distinct values. This is the target with k = 2.

## Problem in brief

A row of trees is given as `fruits`, where `fruits[i]` is the fruit type of tree `i`. You have two baskets, and each basket holds only one type (any amount of it). You start at any tree and pick one fruit from every tree moving right, stopping at the first fruit that fits in neither basket. Return the most fruit you can pick.

In other words: the length of the longest contiguous subarray with **at most 2 distinct** values.

## Key idea

Keep `count[type]` for the window `fruits[left .. right]`.
- Add `fruits[right]` to the map.
- While `len(count) > 2`, remove `fruits[left]`: decrement its count, **delete the key when it reaches 0**, and move `left` forward.
- The window is now valid. Record `right − left + 1`.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [variable size window](../../course/primers/03-sliding-window.md#variable-size-window)
3. [LeetCode Editorial](https://leetcode.com/problems/fruit-into-baskets/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+fruit+into+baskets)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def totalFruit(self, fruits: List[int]) -> int:
        count = {}
        left = 0
        best = 0
        for right, f in enumerate(fruits):
            count[f] = count.get(f, 0) + 1
            while len(count) > 2:            # 3 types: shrink from the left
                g = fruits[left]
                count[g] -= 1
                if count[g] == 0:
                    del count[g]             # g has fully left the window
                left += 1
            best = max(best, right - left + 1)
        return best
```

</details>

**Time:** O(n) · **Space:** O(1) (at most 3 keys)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,1]` | `3` (all trees) |
| `[0,1,2,2]` | `3` (`[1,2,2]`) |
| `[1,2,3,2,2]` | `4` (`[2,3,2,2]`) |

## Pitfall

If you only decrement and never `del`, a type with count 0 still counts in `len(count)`. The window then looks like it has 3 types when it really has 2, and it shrinks too much.

## What changes in the target

Replace `2` with `k`, and the fruit list with a string. Nothing else changes. `k = 0` works too: every character is removed straight away, so the answer is `0` → [NOTES](../NOTES.md).
