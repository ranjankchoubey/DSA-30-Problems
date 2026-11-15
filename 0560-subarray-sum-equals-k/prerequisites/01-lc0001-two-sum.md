# Step 01 · 1. Two Sum (Easy)

[← Roadmap](00-roadmap.md) · [Next: Range Sum Query →](02-lc0303-range-sum-query-immutable.md)

**Practise:** [LeetCode 1](https://leetcode.com/problems/two-sum/)
**Skill it adds:** **complement lookup**. While scanning, ask "have I already seen the value that would complete this one?" in O(1) with a hash map.

## Problem in brief

Return the indices of the two numbers that add up to `target`. Exactly one answer exists, and you can't use the same element twice.

## Key idea

| Approach | Time | Space |
| --- | --- | --- |
| Check every pair | O(n²) | O(1) |
| Sort + two pointers (loses the original indices) | O(n log n) | O(n) |
| **Hash map value → index, look up before inserting** | **O(n)** | **O(n)** |

Look up `target − x` **before** inserting `x`, so an element is never paired with itself.

## Solution

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        index_of = {}
        for i, x in enumerate(nums):
            if target - x in index_of:
                return [index_of[target - x], i]
            index_of[x] = i
        return []
```

**Time:** O(n) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `nums = [2,7,11,15], target = 9` | `[0,1]` |
| `nums = [3,2,4], target = 6` | `[1,2]` |
| `nums = [3,3], target = 6` | `[0,1]` |

## What changes in the next step

Here we look back for a **value**. In Subarray Sum Equals K we'll look back for a **prefix sum**. Step 02 introduces prefix sums.
