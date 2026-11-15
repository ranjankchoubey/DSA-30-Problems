# Step 03 · Concept: water at one index (brute force)

[← Previous: Container With Most Water](02-lc0011-container-with-most-water.md) · [Roadmap](00-roadmap.md) · [Next: Prefix and suffix max arrays →](04-concept-prefix-suffix-max-arrays.md)

> 📚 **New to this?** Primer: [prefix and suffix maximums](../../course/primers/02-two-pointers.md#prefix-and-suffix-maximums)

**Skill it adds:** the formula for the water above **one** bar, and a correct O(n²) baseline.

## Idea

Water above bar `i` rises until it would spill over the lower of the two walls around it:
- `maxL` = the tallest bar in `height[0..i]`
- `maxR` = the tallest bar in `height[i..n−1]`
- water at `i` = `min(maxL, maxR) − height[i]`

Both ranges **include** `i` itself, so `min(maxL, maxR) ≥ height[i]` and the water is never negative. A bar taller than everything on one side holds 0.

The answer is the sum over all `i`.

## Code

```python
def trap_brute_force(height):
    total = 0
    for i in range(len(height)):
        max_left = max(height[: i + 1])     # tallest bar in height[0..i]
        max_right = max(height[i:])         # tallest bar in height[i..n-1]
        total += min(max_left, max_right) - height[i]
    return total
```

**Correct, but slow:** O(n²) time (two scans for every bar) · O(n) space for the slices (O(1) if you scan with plain loops instead)

## Walk-through: `height = [4,2,0,3,2,5]` → 9

| i | height[i] | maxL | maxR | water = min(maxL, maxR) − height[i] |
| --- | --- | --- | --- | --- |
| 0 | 4 | 4 | 5 | 0 |
| 1 | 2 | 4 | 5 | 2 |
| 2 | 0 | 4 | 5 | 4 |
| 3 | 3 | 4 | 5 | 1 |
| 4 | 2 | 4 | 5 | 2 |
| 5 | 5 | 5 | 5 | 0 |
| | | | | **9** |

## Test cases

| Input | Expected |
| --- | --- |
| `[0,1,0,2,1,0,1,3,2,1,2,1]` | `6` |
| `[4,2,0,3,2,5]` | `9` |
| `[1,2,3]` | `0` |

## What changes in the next step

For every `i` we rescan the whole array, even though `maxL` for `i + 1` is just `max(maxL for i, height[i + 1])`. Step 04 computes all the `maxL` values in one left-to-right pass and all the `maxR` values in one right-to-left pass.
