# Step 03 · Concept: previous and next smaller element

[← Previous: Expand each bar](02-concept-expand-each-bar.md) · [Roadmap](00-roadmap.md) · [Target: Largest Rectangle in Histogram →](../NOTES.md)

> 📚 **New to this?** Primer: [monotonic stack](../../course/primers/04-stack.md#monotonic-stack)

**Skill it adds:** finding every bar's previous and next smaller bar in two O(n) stack passes, then `area = h × (next − prev − 1)`.

## Idea

- `prev_smaller[i]` = index of the nearest bar on the left that is **strictly shorter** than bar `i` (`-1` if none)
- `next_smaller[i]` = index of the nearest bar on the right that is **strictly shorter** (`n` if none)
- Bar `i` stretches over `prev_smaller[i] + 1 … next_smaller[i] − 1`, so `width = next_smaller[i] − prev_smaller[i] − 1`

Each array comes from one pass with an **increasing** stack: pop everything `≥` the current bar, and whatever is left on top is the nearest smaller one. The right-to-left pass is the mirror image.

## Code

```python
def largest_rectangle_two_passes(heights):
    n = len(heights)
    prev_smaller = [-1] * n
    next_smaller = [n] * n
    stack = []                            # indices; heights increasing bottom → top
    for i in range(n):
        while stack and heights[stack[-1]] >= heights[i]:
            stack.pop()
        prev_smaller[i] = stack[-1] if stack else -1
        stack.append(i)
    stack = []
    for i in range(n - 1, -1, -1):
        while stack and heights[stack[-1]] >= heights[i]:
            stack.pop()
        next_smaller[i] = stack[-1] if stack else n
        stack.append(i)
    return max(heights[i] * (next_smaller[i] - prev_smaller[i] - 1) for i in range(n))
```

**Time:** O(n) · **Space:** O(n) (two boundary arrays and a stack)

## Walk-through: `[2, 1, 5, 6, 2, 3]`

| i | 0 | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- | --- |
| height | 2 | 1 | 5 | 6 | 2 | 3 |
| `prev_smaller` | −1 | −1 | 1 | 2 | 1 | 4 |
| `next_smaller` | 1 | 6 | 4 | 4 | 6 | 6 |
| width | 1 | 6 | 2 | 1 | 4 | 1 |
| area | 2 | 6 | **10** | 6 | 8 | 3 |

The widths match step 02's stretches, but no bar walks its neighbours.

## Test cases

| Input | Expected |
| --- | --- |
| `[2,1,5,6,2,3]` | `10` |
| `[2,4]` | `4` |
| `[1,1,1,1]` | `4` |

## What changes in the target

Look at the left-to-right pass: when bar `j` is **popped** by bar `i`, then `i` is the first bar after `j` that is no taller (its right boundary), and the bar left on top of the stack is its previous smaller bar (its left boundary). So the area for `j` can be computed **at pop time**: one pass, one stack, no boundary arrays. A height-0 bar added at the end pops whatever is left → [NOTES](../NOTES.md).
