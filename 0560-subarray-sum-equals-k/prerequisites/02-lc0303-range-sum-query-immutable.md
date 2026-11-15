# Step 02 · 303. Range Sum Query – Immutable (Easy)

[← Previous: Two Sum](01-lc0001-two-sum.md) · [Roadmap](00-roadmap.md) · [Next: Subarray sum as a prefix difference →](03-concept-subarray-sum-as-prefix-difference.md)

**Practise:** [LeetCode 303](https://leetcode.com/problems/range-sum-query-immutable/)
**Skill it adds:** **prefix sums**. After O(n) preprocessing, any range sum is one subtraction.

## Problem in brief

Design a class built from `nums` that answers many `sumRange(left, right)` queries (inclusive).

## Key idea

Let `P[0] = 0` and `P[i+1] = P[i] + nums[i]`, so `P[i]` is the sum of the first `i` elements. Then:

```
sum(nums[left .. right]) = P[right + 1] − P[left]
```

The leading `0` means a range starting at index 0 needs no special case.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [prefix sums](../../course/primers/01-arrays-and-hashing.md#3-prefix-sums)
3. [LeetCode Editorial](https://leetcode.com/problems/range-sum-query-immutable/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+range+sum+query+immutable)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class NumArray:
    def __init__(self, nums: List[int]):
        self.prefix = [0]
        for x in nums:
            self.prefix.append(self.prefix[-1] + x)

    def sumRange(self, left: int, right: int) -> int:
        return self.prefix[right + 1] - self.prefix[left]
```

</details>

**Time:** O(n) to build, O(1) per query · **Space:** O(n)

## Test cases

`nums = [-2, 0, 3, -5, 2, -1]`, so `P = [0, -2, -2, 1, -4, -2, -3]`

| Query | Expected |
| --- | --- |
| `sumRange(0, 2)` | `1` |
| `sumRange(2, 5)` | `-1` |
| `sumRange(0, 5)` | `-3` |

## What changes in the next step

Every subarray sum is `P[j] − P[i]` for some `i < j`. Step 03 uses that to count subarrays that sum to `k`, first the slow way.
