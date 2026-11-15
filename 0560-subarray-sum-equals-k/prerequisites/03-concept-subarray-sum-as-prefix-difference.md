# Step 03 · Concept: subarray sum as a prefix difference

[← Previous: Range Sum Query](02-lc0303-range-sum-query-immutable.md) · [Roadmap](00-roadmap.md) · [Target: Subarray Sum Equals K →](../NOTES.md)

**Skill it adds:** turning "count subarrays with sum k" into "count **pairs** of prefix sums whose difference is k", plus knowing why a sliding window can't solve it.

> 📚 **New to this?** Primer: [prefix sum + hash map](../../course/primers/01-arrays-and-hashing.md#4-prefix-sum--hash-map-of-counts-subarray-sum-equals-k)

## Why not a sliding window?

A sliding window needs a fixed rule: "growing the window increases the sum, shrinking decreases it". That only holds when every value is **non-negative**. With negative values, adding an element can **decrease** the sum, so you can't tell whether to grow or shrink.
Example: `nums = [1, 2, 3, -3, 3], k = 3` has 5 answers, including `[3, -3, 3]`, whose sum dips and then recovers.

## Idea: count prefix pairs

With `P[0] = 0` and `P[j] = nums[0] + … + nums[j−1]`:

```
sum(nums[i .. j−1]) = P[j] − P[i] = k   ⟺   P[i] = P[j] − k
```

So the answer is the number of pairs `i < j` with `P[i] = P[j] − k`.

## Code (O(n²), correct but slow)

```python
def subarray_sum_quadratic(nums, k):
    prefix = [0]
    for x in nums:
        prefix.append(prefix[-1] + x)
    count = 0
    for j in range(1, len(prefix)):
        for i in range(j):
            if prefix[j] - prefix[i] == k:
                count += 1
    return count
```

**Time:** O(n²) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `nums = [1,1,1], k = 2` | `2` |
| `nums = [1,2,3,-3,3], k = 3` | `5` |
| `nums = [0,0,0], k = 0` | `6` |

## What changes in the target

The inner loop asks: "how many earlier prefix sums equal `P[j] − k`?" That's **Two Sum's complement lookup** (step 01), except we need a **count**, not an index. Keep a hash map `prefix_sum → how many times seen`, and the inner loop becomes one O(1) lookup → [NOTES](../NOTES.md).
