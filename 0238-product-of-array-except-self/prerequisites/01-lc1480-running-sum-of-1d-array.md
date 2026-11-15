# Step 01 · 1480. Running Sum of 1d Array (Easy)

[← Roadmap](00-roadmap.md) · [Next: Find Pivot Index →](02-lc0724-find-pivot-index.md)

**Practise:** [LeetCode 1480](https://leetcode.com/problems/running-sum-of-1d-array/)
**Skill it adds:** **prefix accumulation**. One pass builds a value that summarises everything up to `i`.

## Problem in brief

Return an array where `out[i] = nums[0] + nums[1] + … + nums[i]`.

## Key idea

Don't re-add everything for each `i`, which is O(n²). Carry a running `total` forward: `out[i] = out[i−1] + nums[i]`.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [prefix sums](../../course/primers/01-arrays-and-hashing.md#3-prefix-sums)
3. [LeetCode Editorial](https://leetcode.com/problems/running-sum-of-1d-array/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+running+sum+of+1d+array)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def runningSum(self, nums: List[int]) -> List[int]:
        out = []
        total = 0
        for x in nums:
            total += x
            out.append(total)
        return out
```

</details>

**Time:** O(n) · **Space:** O(1) extra (the output doesn't count)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,3,4]` | `[1,3,6,10]` |
| `[1,1,1,1,1]` | `[1,2,3,4,5]` |
| `[3,1,2,10,1]` | `[3,4,6,16,17]` |

## What changes in the next step

A prefix value covers everything **left of and including** `i`. Product Except Self needs everything **except** `i`, so we also need what's on the **right**. Step 02 splits an array around `i`.
