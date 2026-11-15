# Step 02 · Concept: expand each bar

[← Previous: Daily Temperatures](01-lc0739-daily-temperatures.md) · [Roadmap](00-roadmap.md) · [Next: Previous and next smaller element →](03-concept-previous-and-next-smaller-element.md)

> 📚 **New to this?** Primer: [monotonic stack](../../course/primers/04-stack.md#monotonic-stack)

**Skill it adds:** seeing that the best rectangle is limited by its **shortest** bar, so each bar only needs to be tried once, as that shortest bar.

## Idea

Any rectangle under the histogram has a shortest bar inside it, and its height can be at most that bar's height. So:
- for each bar `i` with height `h`, assume it is the shortest bar of the rectangle
- stretch **left** while the neighbour is `≥ h`, and **right** while the neighbour is `≥ h`
- `area = h × width`, and the answer is the largest area over all bars

That gives n candidates instead of n² pairs of edges.

## Code

```python
def largest_rectangle_expand(heights):
    n = len(heights)
    best = 0
    for i, h in enumerate(heights):
        left = i
        while left > 0 and heights[left - 1] >= h:
            left -= 1
        right = i
        while right < n - 1 and heights[right + 1] >= h:
            right += 1
        best = max(best, h * (right - left + 1))
    return best
```

**Correct, but slow:** O(n²) time (e.g. all bars equal: every bar stretches across the whole array) · O(1) space

## Walk-through: `[2, 1, 5, 6, 2, 3]`

| i | h | stretches over | width | area |
| --- | --- | --- | --- | --- |
| 0 | 2 | 0..0 | 1 | 2 |
| 1 | 1 | 0..5 | 6 | 6 |
| 2 | 5 | 2..3 | 2 | **10** |
| 3 | 6 | 3..3 | 1 | 6 |
| 4 | 2 | 2..5 | 4 | 8 |
| 5 | 3 | 5..5 | 1 | 3 |

## Test cases

| Input | Expected |
| --- | --- |
| `[2,1,5,6,2,3]` | `10` |
| `[2,4]` | `4` |
| `[1,1,1,1]` | `4` |

## What changes in the next step

The left loop stops at the **previous smaller** bar and the right loop stops at the **next smaller** bar. Step 03 finds both for every bar with a monotonic stack in O(n), instead of walking each time.
