# Step 02 · 724. Find Pivot Index (Easy)

[← Previous: Running Sum](01-lc1480-running-sum-of-1d-array.md) · [Roadmap](00-roadmap.md) · [Next: Prefix and suffix product arrays →](03-concept-prefix-and-suffix-product-arrays.md)

**Practise:** [LeetCode 724](https://leetcode.com/problems/find-pivot-index/)
**Skill it adds:** thinking of "everything except `i`" as **left of `i`** plus **right of `i`**.

## Problem in brief

Return the leftmost index `i` where the sum of elements **strictly left** of `i` equals the sum of elements **strictly right** of `i`. Return `-1` if there is none.

## Key idea

Keep `left` = the sum of everything before `i`, which is a running prefix that **excludes** `i`. Then `right = total − left − nums[i]`, so no second array is needed.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [prefix sums](../../course/primers/01-arrays-and-hashing.md#3-prefix-sums)
3. [LeetCode Editorial](https://leetcode.com/problems/find-pivot-index/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+find+pivot+index)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def pivotIndex(self, nums: List[int]) -> int:
        total = sum(nums)
        left = 0
        for i, x in enumerate(nums):
            if left == total - left - x:
                return i
            left += x
        return -1
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,7,3,6,5,6]` | `3` (left 11 = right 11) |
| `[1,2,3]` | `-1` |
| `[2,1,-1]` | `0` (left 0 = right 0) |

## Pitfall

The prefix must be **exclusive**: compare first, then add `x`. Adding before comparing is the classic off-by-one bug.

## What changes in the next step

Here we got the right side by **subtracting**. With products that would mean **dividing**, which is not allowed in Product Except Self and breaks on zeros. So step 03 builds the right side **directly** with a right-to-left pass.
