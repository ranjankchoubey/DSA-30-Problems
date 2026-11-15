# Step 01 · 643. Maximum Average Subarray I (Easy)

[← Roadmap](00-roadmap.md) · [Next: Longest Substring Without Repeating Characters →](02-lc0003-longest-substring-without-repeating-characters.md)

**Practise:** [LeetCode 643](https://leetcode.com/problems/maximum-average-subarray-i/)
**Skill it adds:** the **fixed-size sliding window**. Update a window's summary in O(1) instead of recomputing it in O(k).

## Problem in brief

You get an integer array `nums` and a length `k`. Among all contiguous pieces of exactly `k` elements, return the largest average. Small floating-point errors (about 10⁻⁵) are accepted.

## Key idea

All windows have the same length `k`, so the largest average belongs to the largest **sum**. Compute the first window's sum once. To move the window one step right, **add** the element that enters (`nums[i]`) and **subtract** the one that leaves (`nums[i − k]`). Divide by `k` only once, at the end.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [fixed size window](../../course/primers/03-sliding-window.md#fixed-size-window)
3. [LeetCode Editorial](https://leetcode.com/problems/maximum-average-subarray-i/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+maximum+average+subarray+i)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def findMaxAverage(self, nums: List[int], k: int) -> float:
        window = sum(nums[:k])               # nums[0 .. k-1]
        best = window
        for i in range(k, len(nums)):
            window += nums[i] - nums[i - k]  # nums[i] enters, nums[i-k] leaves
            best = max(best, window)
        return best / k
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `nums = [1,12,-5,-6,50,3], k = 4` | `12.75` (window `[12,-5,-6,50]`, sum 51) |
| `nums = [5], k = 1` | `5.0` |
| `nums = [-1,-2,-3], k = 2` | `-1.5` (window `[-1,-2]`) |

## Pitfall

Start `best` at the **first window's sum**, not at `0`. With all-negative numbers (test 3) every sum is below 0, and starting at 0 would return a window that doesn't exist.

## What changes in the next step

Here the window size is **fixed**. In Longest Substring Without Repeating Characters the size is the **answer**: the window grows while it follows a rule and shrinks from the left when it breaks it.
