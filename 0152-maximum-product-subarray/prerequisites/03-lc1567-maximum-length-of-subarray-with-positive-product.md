# Step 03 · 1567. Maximum Length of Subarray With Positive Product (Medium)

[← Previous: Maximum Subarray](02-lc0053-maximum-subarray.md) · [Roadmap](00-roadmap.md) · [Target: Maximum Product Subarray →](../NOTES.md)

**Practise:** [LeetCode 1567](https://leetcode.com/problems/maximum-length-of-subarray-with-positive-product/)
**Skill it adds:** tracking a **positive** and a **negative** state together, where a negative number **swaps** them and a zero **resets** both.

## Problem in brief

Return the length of the longest contiguous subarray whose product is **strictly positive**.

## Key idea

Only signs matter. Keep two Kadane-style states for subarrays **ending at** `i`:

- `pos` = length of the longest one with a **positive** product (0 if none)
- `neg` = length of the longest one with a **negative** product (0 if none)

Then for each `x`:

| `x` | new `pos` | new `neg` |
| --- | --- | --- |
| > 0 | `pos + 1` (positive × positive) | `neg + 1` if `neg > 0`, else 0 |
| < 0 | `neg + 1` if `neg > 0`, else 0 (negative × negative) | `pos + 1` (positive × negative, or `x` alone) |
| 0 | 0 | 0 (no subarray through a zero has a non-zero product) |

The answer is the largest `pos` ever seen.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [tracking max and min](../../course/primers/10-1d-dp.md#tracking-max-and-min)
3. [LeetCode Editorial](https://leetcode.com/problems/maximum-length-of-subarray-with-positive-product/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+maximum+length+of+subarray+with+positive+product)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def getMaxLen(self, nums: List[int]) -> int:
        pos = neg = best = 0
        for x in nums:
            if x > 0:
                pos, neg = pos + 1, (neg + 1 if neg else 0)
            elif x < 0:
                pos, neg = (neg + 1 if neg else 0), pos + 1
            else:
                pos = neg = 0
            best = max(best, pos)
        return best
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,-2,-3,4]` | `4` (two negatives: the whole array is positive) |
| `[0,1,-2,-3,-4]` | `3` (`[1,-2,-3]`) |
| `[-1,-2,-3,0,1]` | `2` (`[-1,-2]` or `[-2,-3]`) |

## Pitfall

When `x < 0`, the new `pos` comes from the old `neg` and the new `neg` from the old `pos`. Assign both in **one** statement (`pos, neg = …, …`), or the second update reads an already-changed value.

## What changes in the target

Swap "length" for "product": keep the **largest** and **smallest** product of a subarray ending at `i`. A negative `x` turns the smallest into the largest, exactly like `pos` and `neg` swapping here. And a zero resets both, just like here → [NOTES](../NOTES.md).
