# Step 04 · Concept: monotonic deque

[← Previous: Daily Temperatures](03-lc0739-daily-temperatures.md) · [Roadmap](00-roadmap.md) · [Target: Sliding Window Maximum →](../NOTES.md)

> 📚 **New to this?** Primer: [monotonic deque](../../course/primers/03-sliding-window.md#monotonic-deque)

**Skill it adds:** a queue that reports its **maximum** in O(1), built from a deque that pops from the **back** (smaller values) and the **front** (the oldest value leaving).

## Idea

A sliding window is a **queue**: values join at the back and leave from the front, oldest first. Build a queue that also knows its max:
- `items`: every value in the queue, oldest first.
- `cands`: the values that could still be the maximum, **decreasing** from front to back. The front is the current max.

| Operation | What happens to `cands` |
| --- | --- |
| `push(x)` | Pop from the **back** every value smaller than `x` (as in Daily Temperatures): they are older **and** smaller, so they can never be the max while `x` is in the queue. Then append `x`. |
| `pop()` | The oldest item leaves. If it equals `cands[0]`, it **was** the max, so pop it from the **front** too. |
| `max()` | `cands[0]` |

Equal values are kept (strict `<` when pushing), so popping one copy of the max leaves the other copy in place.

## Code

```python
from collections import deque

class MaxQueue:
    def __init__(self):
        self.items = deque()             # every value, oldest first
        self.cands = deque()             # max candidates, decreasing front -> back

    def push(self, x):
        self.items.append(x)
        while self.cands and self.cands[-1] < x:
            self.cands.pop()             # older and smaller: never the max again
        self.cands.append(x)

    def pop(self):
        x = self.items.popleft()
        if x == self.cands[0]:
            self.cands.popleft()         # the value leaving was the max
        return x

    def max(self):
        return self.cands[0]


def max_sliding_window_queue(nums, k):
    q = MaxQueue()
    out = []
    for i, x in enumerate(nums):
        if i >= k:
            q.pop()                      # nums[i - k] leaves the window
        q.push(x)
        if i >= k - 1:
            out.append(q.max())
    return out
```

**Time:** O(n): every value enters `cands` once and leaves it at most once · **Space:** O(k) (two deques)

## Walk-through: `nums = [1,3,-1,-3,5,3,6,7], k = 3`

| i | leaves (front) | pushed | popped from `cands` back | `cands` after | out |
| --- | --- | --- | --- | --- | --- |
| 0 | — | 1 | — | [1] | — |
| 1 | — | 3 | 1 | [3] | — |
| 2 | — | −1 | — | [3, −1] | [3] |
| 3 | 1 (not the max) | −3 | — | [3, −1, −3] | [3, 3] |
| 4 | 3 (**was** the max, popped from the front) | 5 | −3, −1 | [5] | [3, 3, 5] |
| 5 | −1 (not the max) | 3 | — | [5, 3] | [3, 3, 5, 5] |
| 6 | −3 (not the max) | 6 | 3, 5 | [6] | [3, 3, 5, 5, 6] |
| 7 | 5 (not the max) | 7 | 6 | [7] | [3, 3, 5, 5, 6, 7] |

At `i = 4` both ends are used: the front loses `3` because it left the window, and the back loses `−3` and `−1` because `5` beats them.

## Test cases

| Input | Expected |
| --- | --- |
| `nums = [1,3,-1,-3,5,3,6,7], k = 3` | `[3,3,5,5,6,7]` |
| `nums = [1], k = 1` | `[1]` |
| `nums = [9,11], k = 2` | `[11]` |

## What changes in the target

The second deque, `items`, exists only to know which value leaves and whether it was the max. Store **indices** in `cands` instead of values: `nums[j]` gives the value, and `j ≤ i − k` says whether it has left the window. One deque now does both jobs, and the front check becomes `if dq[0] <= i - k: dq.popleft()` → [NOTES](../NOTES.md).
