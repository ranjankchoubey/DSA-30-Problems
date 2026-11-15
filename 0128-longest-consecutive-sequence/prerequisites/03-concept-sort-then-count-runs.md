# Step 03 · Concept: sort, then count runs (the O(n log n) baseline)

[← Previous: Max Consecutive Ones](02-lc0485-max-consecutive-ones.md) · [Roadmap](00-roadmap.md) · [Next: Naive hash-set walk →](04-concept-naive-hash-set-walk.md)

**Skill it adds:** a correct, simple baseline, and the **duplicate** pitfall.

## Idea

After sorting, consecutive values sit next to each other. Run step 02's counter with the condition `nums[i] == nums[i−1] + 1`.

**Duplicates** (e.g. `[1, 2, 2, 3]`) must be **skipped**: they neither extend the run nor break it.

## Code

```python
def longest_consecutive_sorted(nums):
    if not nums:
        return 0
    nums = sorted(nums)
    best = cur = 1
    for i in range(1, len(nums)):
        if nums[i] == nums[i - 1]:
            continue                       # duplicate: run unchanged
        if nums[i] == nums[i - 1] + 1:
            cur += 1
        else:
            cur = 1
        best = max(best, cur)
    return best
```

**Time:** O(n log n) · **Space:** O(n) for the sorted copy (O(1) with an in-place sort)

## Test cases

| Input | Expected |
| --- | --- |
| `[100,4,200,1,3,2]` | `4` |
| `[0,3,7,2,5,8,4,6,0,1]` | `9` (the duplicate `0` must not reset the run) |
| `[]` | `0` |

## What changes in the next step

The course requires **O(n)**, so sorting is not allowed. Instead of looking at the *array neighbour*, ask "**is `x + 1` in the set?**" (step 01). Step 04 tries that directly, and shows why the direct version is still too slow.
