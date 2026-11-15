# Step 02 · 268. Missing Number (Easy)

[← Previous: Contains Duplicate](01-lc0217-contains-duplicate.md) · [Roadmap](00-roadmap.md) · [Next: Find All Numbers Disappeared →](03-lc0448-find-all-numbers-disappeared-in-an-array.md)

**Practise:** [LeetCode 268](https://leetcode.com/problems/missing-number/)
**Skill it adds:** finding a missing value with **math or XOR** in O(1) extra space.

## Problem in brief

`nums` holds `n` distinct numbers from the range `0..n`. Exactly one number in that range is missing; return it.

## Key idea

- **Sum formula:** `0 + 1 + … + n = n(n+1)/2`. The missing number is `expected − sum(nums)`.
- **XOR:** `a ^ a = 0` and `a ^ 0 = a`. XOR all indices `0..n` with all values; every present number cancels out and only the missing one remains.

## Solution

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        n = len(nums)
        return n * (n + 1) // 2 - sum(nums)
```

XOR version (no risk of overflow in languages with fixed-size integers):

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        x = len(nums)
        for i, v in enumerate(nums):
            x ^= i ^ v
        return x
```

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `[3,0,1]` | `2` |
| `[0,1]` | `2` |
| `[9,6,4,2,3,5,7,0,1]` | `8` |

## What changes in the next step

In Set Mismatch the range is `1..n`, and the missing value has been **replaced by the duplicate**, so:

```
n(n+1)/2 − sum(nums) = missing − duplicate
```

Combined with step 01's set, that already solves Set Mismatch, but with O(n) space for the set. Step 03 shows how to find "seen" values **without** a set.
