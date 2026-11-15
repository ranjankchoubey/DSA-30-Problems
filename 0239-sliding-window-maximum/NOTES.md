# 0239. Sliding Window Maximum

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q5 | [239](https://leetcode.com/problems/sliding-window-maximum/) | Hard | Sliding Window | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0239-sliding-window-maximum.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Peak Traffic Monitor:** a network dashboard has per-second request counts and needs the **maximum** of every window of `k` consecutive seconds, in O(n). Explain the monotonic deque invariant, why each element is pushed and popped at most once, and what changes if the window size is variable. `n` is up to 100,000.

## 2. Pattern recognition

- **Pattern:** Sliding Window (fixed size) with a monotonic deque
- **Signals in the statement:** "maximum in **every window** of k consecutive", O(n). A max can't be updated by subtraction like a sum, and a heap is O(n log n), so you need a structure that drops useless values.
- **Core idea in one line:** keep indices in a deque with decreasing values; pop smaller values from the back, expired indices from the front, and read the max at the front.

## 3. Approaches

### Approach 1: Brute force

Take `max` of every window. **Time:** O(nk) · **Space:** O(1) besides the output

<details>
<summary><b>Show brute force</b></summary>

```python
def max_sliding_window_brute(nums, k):
    return [max(nums[i:i + k]) for i in range(len(nums) - k + 1)]
```

</details>

### Approach 2: Max-heap with lazy deletion ([step 02](./prerequisites/02-concept-brute-force-and-heap.md))

Push `(-value, index)`; before reading the top, pop it while its index has left the window. **Time:** O(n log n) · **Space:** O(n)

<details>
<summary><b>Show heap solution</b></summary>

```python
import heapq

def max_sliding_window_heap(nums, k):
    heap = []
    out = []
    for i, x in enumerate(nums):
        heapq.heappush(heap, (-x, i))
        if i >= k - 1:
            while heap[0][1] <= i - k:
                heapq.heappop(heap)
            out.append(-heap[0][0])
    return out
```

</details>

### Approach 3: Monotonic deque of indices (optimal)

**Time:** O(n) · **Space:** O(k)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
from collections import deque

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        dq = deque()                     # indices; nums[dq[0]] > nums[dq[1]] > ...
        out = []
        for i, x in enumerate(nums):
            while dq and nums[dq[-1]] <= x:
                dq.pop()                 # smaller and older: never the max again
            dq.append(i)
            if dq[0] <= i - k:
                dq.popleft()             # the front has left the window
            if i >= k - 1:
                out.append(nums[dq[0]])
        return out
```

</details>

## 4. Why it works

- **Invariant:** the deque holds indices in increasing order whose values are **strictly decreasing**, all inside the current window `[i − k + 1, i]`.
- **Every dropped index is useless:** an index `j` is popped from the back only when a later index `i` has `nums[i] ≥ nums[j]`. Every future window that contains `j` also contains `i` (it is newer), so `j` can never be the unique answer.
- **So the front is the max:** every window index not in the deque is beaten by some index that is, and the deque's values decrease, so `nums[dq[0]]` is the window maximum.
- **One front pop is enough:** the window moves by one index per step, so at most one index can expire, and it must be the oldest (the front).

## 5. Dry run: `nums = [1,3,-1,-3,5,3,6,7], k = 3` → `[3,3,5,5,6,7]`

The deque is shown as `index:value`.

| i | x | popped from back | popped from front | deque after | out |
| --- | --- | --- | --- | --- | --- |
| 0 | 1 | — | — | [0:1] | — |
| 1 | 3 | 1 | — | [1:3] | — |
| 2 | −1 | — | — | [1:3, 2:−1] | [3] |
| 3 | −3 | — | — | [1:3, 2:−1, 3:−3] | [3, 3] |
| 4 | 5 | −3, −1, 3 | — | [4:5] | [3, 3, 5] |
| 5 | 3 | — | — | [4:5, 5:3] | [3, 3, 5, 5] |
| 6 | 6 | 3, 5 | — | [6:6] | [3, 3, 5, 5, 6] |
| 7 | 7 | 6 | — | [7:7] | [3, 3, 5, 5, 6, 7] |

On this input the front pop never fires, because each old maximum is beaten from the back before it expires. On `[4,3,2,1], k = 2` → `[4,3,2]` it does: at `i = 2`, index 0 (value 4) leaves from the front.

## 6. Edge cases & pitfalls

- **`k = 1`:** every window is one element, so the output equals `nums` (TC 2).
- **`k = n`:** one window, so the output is `[max(nums)]` (TC 3).
- Store **indices**, not values, or you can't tell when the front has left the window.
- `<=` vs `<` on the back: both give correct maxima with indices. `<=` drops equal older values early, which keeps the deque smaller.
- Only append to `out` once `i ≥ k − 1`, when the first full window exists.
- Use `collections.deque`; `list.pop(0)` is O(n) and makes the whole thing O(nk).

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [1,3,-1,-3,5,3,6,7], k = 3` | `[3,3,5,5,6,7]` |
| TC 2 | `nums = [1], k = 1` | `[1]` |
| TC 3 | `nums = [9,11], k = 2` | `[11]` |

## 8. Follow-up questions (from course doc)

- **Q: Explain the monotonic deque invariant.**
  **A:** The deque holds indices of the current window in increasing order, and their values strictly decrease from front to back. So the front is the window's maximum. Each index after it is the maximum of what remains once everything in front of it expires (see §4).
- **Q: Why is each element pushed and popped at most once?**
  **A:** Index `i` is appended exactly once, at step `i`. After that it can only leave by one pop, from the back (beaten by a newer, bigger-or-equal value) or from the front (expired), and it is never pushed again. So there are at most `n` pushes and `n` pops in total. One step can pop many indices, but the total work over the whole scan is O(n), which is **amortised O(1)** per element.
- **Q: What changes if the window size is variable?**
  **A:** It depends on how the window moves.
  - **Both edges only move forward** (e.g. "all requests in the last 60 seconds" with uneven timestamps, or a window that grows and shrinks by a rule): the same deque works. The back is unchanged. For the front, replace the single `if` with `while dq[0] < left: dq.popleft()`, because several indices can expire at once. It is still O(n) amortised. LC 1438 (longest subarray with max − min ≤ limit) uses this with a max deque and a min deque.
  - **Arbitrary windows** (random `[l, r]` queries where `l` can move backwards): the deque doesn't apply, because popped values may be needed again. Use a **sparse table** (O(n log n) build, O(1) per query) or a **segment tree** (O(log n) per query, and it supports updates).

<details>
<summary><b>Show the variable-window (time-based) version</b></summary>

```python
from collections import deque

def max_in_time_windows(times, values, span):
    # For each i: max of values[j], j <= i, with times[i] - span < times[j].
    # times is sorted (non-decreasing) and span >= 1.
    dq = deque()
    out = []
    left = 0
    for i, x in enumerate(values):
        while dq and values[dq[-1]] <= x:
            dq.pop()
        dq.append(i)
        while times[i] - times[left] >= span:
            left += 1                    # the window's left edge moves forward
        while dq[0] < left:
            dq.popleft()                 # several indices may expire at once
        out.append(values[dq[0]])
    return out
```

</details>

## 9. Threads

- **Q17** Next Greater Element II and **Q15** Largest Rectangle in Histogram: the monotonic stack from step 03.
- **Q9** and **Q4**: sliding windows of variable size, with count maps instead of a deque.
- **Q19** Merge K Sorted Lists: heaps, as in Approach 2.

## 10. One-line revision

> Deque of indices with decreasing values: pop the back while nums[back] ≤ x, append i, pop the front if it is ≤ i − k, and from i = k − 1 read nums[dq[0]].
