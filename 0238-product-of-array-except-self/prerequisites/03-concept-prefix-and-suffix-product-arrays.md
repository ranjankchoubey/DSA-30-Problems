# Step 03 · Concept: prefix and suffix product arrays

[← Previous: Find Pivot Index](02-lc0724-find-pivot-index.md) · [Roadmap](00-roadmap.md) · [Target: Product of Array Except Self →](../NOTES.md)

**Skill it adds:** building **exclusive** prefix and suffix arrays in two passes, one in each direction.

## Idea

For each `i`:
- `left[i]` = product of `nums[0 .. i−1]`, which is `1` when there's nothing on the left
- `right[i]` = product of `nums[i+1 .. n−1]`, which is `1` when there's nothing on the right
- the answer is `left[i] × right[i]`

The identity value for products is `1`, just as `0` is for sums.

## Code

```python
def product_except_self_two_arrays(nums):
    n = len(nums)
    left = [1] * n                   # left[i]  = product of nums[0 .. i-1]
    right = [1] * n                  # right[i] = product of nums[i+1 .. n-1]
    for i in range(1, n):
        left[i] = left[i - 1] * nums[i - 1]
    for i in range(n - 2, -1, -1):
        right[i] = right[i + 1] * nums[i + 1]
    return [left[i] * right[i] for i in range(n)]
```

**Time:** O(n) · **Space:** O(n) (two helper arrays)

## Walk-through: `[1, 2, 3, 4]`

| i | 0 | 1 | 2 | 3 |
| --- | --- | --- | --- | --- |
| `left` | 1 | 1 | 2 | 6 |
| `right` | 24 | 12 | 4 | 1 |
| answer | **24** | **12** | **8** | **6** |

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,3,4]` | `[24,12,8,6]` |
| `[-1,1,0,-3,3]` | `[0,0,9,0,0]` |
| `[0,0,2]` | `[0,0,0]` |

## What changes in the target

The target wants O(1) extra space, and the output array doesn't count. So:
1. Write `left` **directly into the output array**.
2. Replace `right` with a **single running variable** and multiply it in during a right-to-left pass.

→ [NOTES](../NOTES.md)
