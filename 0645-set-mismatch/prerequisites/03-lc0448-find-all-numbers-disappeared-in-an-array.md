# Step 03 · 448. Find All Numbers Disappeared in an Array (Easy)

[← Previous: Missing Number](02-lc0268-missing-number.md) · [Roadmap](00-roadmap.md) · [Target: Set Mismatch →](../NOTES.md)

**Practise:** [LeetCode 448](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/)
**Skill it adds:** **index marking**. When values are in `1..n`, the array itself can record what you've seen (O(1) extra space).

## Problem in brief

`nums` has length `n` and every value is in `1..n`. Some values appear twice, others not at all. Return every value in `1..n` that does not appear.

## Key idea

Value `x` "owns" index `x − 1`. For every `x`, make `nums[x − 1]` **negative** to mean "x was seen". Use `abs()` when reading, because a cell may already be negated. Any index `i` that is still **positive** at the end means `i + 1` never appeared.

## Solution

```python
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
        for x in nums:
            i = abs(x) - 1
            nums[i] = -abs(nums[i])
        return [i + 1 for i, v in enumerate(nums) if v > 0]
```

**Time:** O(n) · **Space:** O(1) extra (the output doesn't count)

## Dry run: `[4,3,2,7,8,2,3,1]`

| x | index marked | array after |
| --- | --- | --- |
| 4 | 3 | `[4,3,2,-7,8,2,3,1]` |
| 3 | 2 | `[4,3,-2,-7,8,2,3,1]` |
| 2 | 1 | `[4,-3,-2,-7,8,2,3,1]` |
| 7 | 6 | `[4,-3,-2,-7,8,2,-3,1]` |
| 8 | 7 | `[4,-3,-2,-7,8,2,-3,-1]` |
| 2 | 1 | already negative |
| 3 | 2 | already negative |
| 1 | 0 | `[-4,-3,-2,-7,8,2,-3,-1]` |

Indices 4 and 5 are still positive, so the answer is `[5, 6]`.

## Test cases

| Input | Expected |
| --- | --- |
| `[4,3,2,7,8,2,3,1]` | `[5,6]` |
| `[1,1]` | `[2]` |
| `[1,2,3]` | `[]` |

## Pitfall

This **modifies the input**. If the caller needs the original, restore it afterwards with `nums[i] = abs(nums[i])`.

## What changes in the target

While marking, if `nums[x − 1]` is **already negative**, you've just found the **duplicate**. The one index left positive at the end gives the **missing** number. That's Set Mismatch in O(1) extra space → [NOTES](../NOTES.md).
