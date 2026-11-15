# Step 02 · 485. Max Consecutive Ones (Easy)

[← Previous: Contains Duplicate](01-lc0217-contains-duplicate.md) · [Roadmap](00-roadmap.md) · [Next: Sort, then count runs →](03-concept-sort-then-count-runs.md)

**Practise:** [LeetCode 485](https://leetcode.com/problems/max-consecutive-ones/)
**Skill it adds:** the **run-length counter**. Extend the current run while a condition holds, reset it when the condition breaks, and remember the best run seen.

## Problem in brief

Given a binary array, return the length of the longest run of consecutive `1`s.

## Key idea

Two variables: `cur` (length of the current run) and `best` (longest run so far).

## Solution

```python
class Solution:
    def findMaxConsecutiveOnes(self, nums: List[int]) -> int:
        best = cur = 0
        for x in nums:
            cur = cur + 1 if x == 1 else 0
            best = max(best, cur)
        return best
```

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,1,0,1,1,1]` | `3` |
| `[1,0,1,1,0,1]` | `2` |
| `[0]` | `0` |

## What changes in the next step

Here the run condition is "`x == 1`". In Longest Consecutive Sequence it's "`x == previous + 1`", but the input is **unsorted**, so neighbours in the array aren't neighbours in value. Step 03 sorts first, to get a working baseline.
