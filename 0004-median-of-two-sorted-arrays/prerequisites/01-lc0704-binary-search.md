# Step 01 · 704. Binary Search (Easy)

[← Roadmap](00-roadmap.md) · [Next: First Bad Version →](02-lc0278-first-bad-version.md)

**Practise:** [LeetCode 704](https://leetcode.com/problems/binary-search/)
**Skill it adds:** classic binary search, and the **loop invariant** that proves it's correct.

## Problem in brief

The array is sorted in ascending order with no repeated values. Return the index of `target`, or `-1` if it isn't there. Your solution must run in O(log n).

## Key idea

**Invariant:** "if `target` is in the array, it's inside `nums[lo..hi]`." Start with the whole array. Look at the middle:

| `nums[mid]` vs `target` | Move |
| --- | --- |
| equal | return `mid` |
| smaller | target is to the right: `lo = mid + 1` |
| bigger | target is to the left: `hi = mid − 1` |

Each step keeps the invariant and removes at least half the range. When `lo > hi` the range is empty, so the answer is `-1`.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [binary search template](../../course/primers/05-binary-search.md#binary-search-template)
3. [LeetCode Editorial](https://leetcode.com/problems/binary-search/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+binary+search)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        lo, hi = 0, len(nums) - 1
        while lo <= hi:                  # range nums[lo..hi] is not empty
            mid = (lo + hi) // 2
            if nums[mid] == target:
                return mid
            if nums[mid] < target:
                lo = mid + 1
            else:
                hi = mid - 1
        return -1
```

</details>

**Time:** O(log n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `nums = [-1,0,3,5,9,12], target = 9` | `4` |
| `nums = [-1,0,3,5,9,12], target = 2` | `-1` |
| `nums = [5], target = 5` | `0` |

## Pitfall

`while lo <= hi` goes with `lo = mid + 1` and `hi = mid − 1`. Writing `hi = mid` here can loop forever, and `while lo < hi` misses the last remaining item.

## What changes in the next step

Here we compare `nums[mid]` with a known **value**. Step 02 has no value to compare with, only a yes/no question about `mid`. The median target is like that too: the question is "did I take too many items from the first array?"
