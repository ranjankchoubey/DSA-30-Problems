# Step 02 · Concept: brute force and heap

[← Previous: Maximum Average Subarray I](01-lc0643-maximum-average-subarray-i.md) · [Roadmap](00-roadmap.md) · [Next: Daily Temperatures →](03-lc0739-daily-temperatures.md)

> 📚 **New to this?** Primer: [window maximum with a heap](../../course/primers/03-sliding-window.md#window-maximum-with-a-heap)

**Skill it adds:** the two baselines, O(nk) by rescanning and O(n log n) with a heap and **lazy deletion**, and what they waste.

## Idea

**Brute force:** for every window, call `max` on its `k` values.

**Heap:** keep a max-heap of `(-value, index)` pairs. Python's `heapq` is a min-heap, so negate the value. When the window slides, the leaving element is **not** removed, because deleting from the middle of a heap is slow. Instead, before reading the top, pop it while its index is outside the window (`index ≤ i − k`). Entries buried below the top can stay stale; they don't matter until they reach the top.

## Code

```python
def max_sliding_window_brute(nums, k):
    return [max(nums[i:i + k]) for i in range(len(nums) - k + 1)]
```

**Time:** O(nk) · **Space:** O(1) besides the output

```python
import heapq

def max_sliding_window_heap(nums, k):
    heap = []                            # (-value, index): a max-heap by negation
    out = []
    for i, x in enumerate(nums):
        heapq.heappush(heap, (-x, i))
        if i >= k - 1:
            while heap[0][1] <= i - k:   # the top has left the window
                heapq.heappop(heap)
            out.append(-heap[0][0])
    return out
```

**Time:** O(n log n) · **Space:** O(n) (stale entries can pile up, e.g. on increasing input nothing is ever popped)

## Walk-through (heap): `nums = [1,3,-1,-3,5,3,6,7], k = 3`

| i | push | stale tops popped | top (max) | out |
| --- | --- | --- | --- | --- |
| 0 | 1 | — | — | — |
| 1 | 3 | — | — | — |
| 2 | −1 | — | 3 (index 1) | [3] |
| 3 | −3 | — | 3 (index 1) | [3, 3] |
| 4 | 5 | — | 5 (index 4) | [3, 3, 5] |
| 5 | 3 | — | 5 (index 4) | [3, 3, 5, 5] |
| 6 | 6 | — | 6 (index 6) | [3, 3, 5, 5, 6] |
| 7 | 7 | — | 7 (index 7) | [3, 3, 5, 5, 6, 7] |

The heap never pops here, so by the end it holds all 8 entries, including `−1` and `−3`, which stopped mattering at `i = 4`.

## Test cases

| Input | Expected |
| --- | --- |
| `nums = [1,3,-1,-3,5,3,6,7], k = 3` | `[3,3,5,5,6,7]` |
| `nums = [1], k = 1` | `[1]` |
| `nums = [9,11], k = 2` | `[11]` |

## What changes in the next step

Look at `i = 4`: once `5` arrives, the earlier `−1` and `−3` can **never** be a window maximum again. `5` is bigger, and it stays in the window longer. Throwing such values away as soon as a bigger one arrives is the **monotonic stack** idea, taught next with Daily Temperatures.
