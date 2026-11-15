# Step 04 · Concept: all unique pairs (Two Sum II, every answer)

[← Previous: Remove Duplicates from Sorted Array](03-lc0026-remove-duplicates-from-sorted-array.md) · [Roadmap](00-roadmap.md) · [Target: 3Sum →](../NOTES.md)

> 📚 **New to this?** Primer: [skipping duplicates](../../course/primers/02-two-pointers.md#skipping-duplicates)

**Skill it adds:** listing **every** pair with a given sum, each distinct pair once, in one O(n) two-pointer pass.

## Idea

Run step 02's two pointers on a sorted array, but **don't stop** at a match:

1. Record the pair `[nums[l], nums[r]]`.
2. Move **both** pointers inward. Neither value can make another *new* pair with the other one.
3. Step `l` past any copies of the value just used (step 03's check, `nums[l] == nums[l − 1]`). Otherwise the next round would report the same pair again.

There's no need to skip copies on the `r` side too. Once `nums[l]` has grown, a leftover copy of the old `nums[r]` gives a sum that's too big, so the normal `r -= 1` moves past it.

## Code

```python
def all_unique_pairs(nums, target):
    """nums must be sorted. Returns each distinct pair [a, b] with a + b == target once."""
    pairs = []
    l, r = 0, len(nums) - 1
    while l < r:
        s = nums[l] + nums[r]
        if s < target:
            l += 1
        elif s > target:
            r -= 1
        else:
            pairs.append([nums[l], nums[r]])
            l += 1
            r -= 1
            while l < r and nums[l] == nums[l - 1]:   # skip copies of the value just used
                l += 1
    return pairs
```

**Time:** O(n) (each pointer only moves inward) · **Space:** O(1) apart from the output

## Walk-through: `nums = [-1,-1,0,1,2,2]`, `target = 1`

| l | r | nums[l] + nums[r] | action |
| --- | --- | --- | --- |
| 0 | 5 | −1 + 2 = 1 | match → record `[-1,2]`; l = 1, r = 4; `nums[1] == nums[0]`, so skip to l = 2 |
| 2 | 4 | 0 + 2 = 2 | too big → r = 3 |
| 2 | 3 | 0 + 1 = 1 | match → record `[0,1]`; l = 3, r = 2 |
| 3 | 2 | | stop |

Result: `[[-1,2],[0,1]]`. Without the skip, `l = 1, r = 4` would report `[-1,2]` a second time.

## Test cases

| Input | Expected |
| --- | --- |
| `nums = [-1,-1,0,1,2,2], target = 1` | `[[-1,2],[0,1]]` |
| `nums = [1,1,1,1], target = 2` | `[[1,1]]` |
| `nums = [1,2,3], target = 10` | `[]` |

## What changes in the target

3Sum is this function inside one more loop:
1. **Sort** the array once.
2. For each index `i`, run this pass on `nums[i+1:]` with `target = −nums[i]`, and add `nums[i]` to the front of each pair.
3. **Outer-level dedupe:** skip `i` when `nums[i] == nums[i − 1]`, because that first value has already been tried.

→ [NOTES](../NOTES.md)
