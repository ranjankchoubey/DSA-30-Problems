# Step 02 · 209. Minimum Size Subarray Sum (Medium)

[← Previous: Longest Substring Without Repeating Characters](01-lc0003-longest-substring-without-repeating-characters.md) · [Roadmap](00-roadmap.md) · [Next: Valid Anagram →](03-lc0242-valid-anagram.md)

**Practise:** [LeetCode 209](https://leetcode.com/problems/minimum-size-subarray-sum/)
**Skill it adds:** shrinking **while the window is valid** to find the **shortest** valid window.

## Problem in brief

You get a positive integer `target` and an array `nums` of **positive** integers. Return the length of the shortest contiguous subarray whose sum is **at least** `target`, or `0` if there is none.

## Key idea

Add `nums[right]` to a running `total`. As soon as `total ≥ target`, the window is valid, so try to make it shorter: record its length, subtract `nums[left]`, move `left` forward, and **repeat while it is still valid**.

This only works because every number is positive: growing the window raises the sum and shrinking lowers it. So once a window is valid, every longer window ending at the same `right` is valid too, and only the shortest one matters.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [variable size window](../../course/primers/03-sliding-window.md#variable-size-window)
3. [LeetCode Editorial](https://leetcode.com/problems/minimum-size-subarray-sum/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+minimum+size+subarray+sum)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        left = 0
        total = 0
        best = float("inf")
        for right, x in enumerate(nums):
            total += x
            while total >= target:           # valid: record, then try shorter
                best = min(best, right - left + 1)
                total -= nums[left]
                left += 1
        return 0 if best == float("inf") else best
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `target = 7, nums = [2,3,1,2,4,3]` | `2` (`[4,3]`) |
| `target = 4, nums = [1,4,4]` | `1` (`[4]`) |
| `target = 11, nums = [1,1,1,1,1,1,1,1]` | `0` (the total is only 8) |

## Pitfall

Record `best` **inside** the shrink loop, before removing `nums[left]`. After the loop ends the window is no longer valid, so recording there gives the wrong length.

## What changes in the next step

In the target, "valid" isn't "sum ≥ target" but "contains every character of `t` with the right counts". Before sliding anything, step 03 practises the counting part: comparing two strings by their character frequencies.
