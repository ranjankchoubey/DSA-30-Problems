# Step 02 · 53. Maximum Subarray (Medium)

[← Previous: Climbing Stairs](01-lc0070-climbing-stairs.md) · [Roadmap](00-roadmap.md) · [Next: Maximum Length of Subarray With Positive Product →](03-lc1567-maximum-length-of-subarray-with-positive-product.md)

**Practise:** [LeetCode 53](https://leetcode.com/problems/maximum-subarray/)
**Skill it adds:** **Kadane's algorithm**: the DP state "best sum of a subarray that **ends at** `i`".

## Problem in brief

Return the largest sum of any contiguous, non-empty subarray of `nums`. Values can be negative.

## Key idea

"Best subarray anywhere" is hard to build step by step. "Best subarray **ending at** `i`" is easy:

```
best_ending_here(i) = max(nums[i], best_ending_here(i - 1) + nums[i])
```

Either **start fresh** at `i`, or **extend** the best subarray that ended at `i − 1`. Extending only helps if that previous sum is positive. The answer is the largest `best_ending_here` seen at any `i`, because every subarray ends somewhere.

Like Climbing Stairs, each value needs only the previous one, so one variable is enough.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [Kadane](../../course/primers/10-1d-dp.md#kadane)
3. [LeetCode Editorial](https://leetcode.com/problems/maximum-subarray/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+maximum+subarray)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        best = current = nums[0]
        for x in nums[1:]:
            current = max(x, current + x)   # start fresh, or extend
            best = max(best, current)
        return best
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `[-2,1,-3,4,-1,2,1,-5,4]` | `6` (`[4,-1,2,1]`) |
| `[5,4,-1,7,8]` | `23` (the whole array) |
| `[-3,-1,-2]` | `-1` (all negative: the single largest value) |

## Pitfall

Starting `best = 0` returns 0 for an all-negative array, but the subarray must be non-empty. Start from `nums[0]`.

## What changes in the next step

With **sums**, the best way to extend is always from the **largest** previous value. With **products**, a negative number turns the largest into the smallest and the smallest (most negative) into the largest. Step 03 shows how to keep the positive and negative sides together.
