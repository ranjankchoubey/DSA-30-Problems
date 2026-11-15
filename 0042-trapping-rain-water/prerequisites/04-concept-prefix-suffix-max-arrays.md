# Step 04 · Concept: prefix and suffix max arrays

[← Previous: Water at one index](03-concept-water-at-index.md) · [Roadmap](00-roadmap.md) · [Target: Trapping Rain Water →](../NOTES.md)

> 📚 **New to this?** Primer: [prefix and suffix maximums](../../course/primers/02-two-pointers.md#prefix-and-suffix-maximums)

**Skill it adds:** computing every `maxL` and `maxR` in **two passes**, for O(n) time.

## Idea

- `L[i] = max(height[0..i])`, so `L[i] = max(L[i − 1], height[i])`: one pass left to right.
- `R[i] = max(height[i..n−1])`, so `R[i] = max(R[i + 1], height[i])`: one pass right to left.
- The answer is the sum of `min(L[i], R[i]) − height[i]`.

This is step 03's formula with each maximum looked up instead of rescanned. It's the same left pass / right pass as Product of Array Except Self, with `max` in place of `×`.

## Code

```python
def trap_prefix_suffix(height):
    n = len(height)
    if n == 0:
        return 0
    L, R = [0] * n, [0] * n
    L[0] = height[0]
    for i in range(1, n):
        L[i] = max(L[i - 1], height[i])
    R[-1] = height[-1]
    for i in range(n - 2, -1, -1):
        R[i] = max(R[i + 1], height[i])
    return sum(min(L[i], R[i]) - height[i] for i in range(n))
```

**Time:** O(n) · **Space:** O(n) (two helper arrays)

## Walk-through: `height = [4,2,0,3,2,5]` → 9

| i | 0 | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- | --- |
| `height` | 4 | 2 | 0 | 3 | 2 | 5 |
| `L` | 4 | 4 | 4 | 4 | 4 | 5 |
| `R` | 5 | 5 | 5 | 5 | 5 | 5 |
| water | 0 | 2 | 4 | 1 | 2 | 0 |

Total: 0 + 2 + 4 + 1 + 2 + 0 = **9**.

## Test cases

| Input | Expected |
| --- | --- |
| `[0,1,0,2,1,0,1,3,2,1,2,1]` | `6` |
| `[4,2,0,3,2,5]` | `9` |
| `[1,2,3]` | `0` |

## What changes in the target

The formula only uses the **smaller** of `L[i]` and `R[i]`. Two pointers moving inward keep a running `leftMax` and `rightMax`. Whichever is smaller is already the true limit for that side's next bar, since the other side can only be taller. So that bar's water can be added right away, and both arrays shrink to two variables: O(1) space. The NOTES prove this step by step.

→ [NOTES](../NOTES.md)
