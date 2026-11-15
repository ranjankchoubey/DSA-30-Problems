# Step 02 · 703. Kth Largest Element in a Stream (Easy)

[← Previous: Merge Two Sorted Lists](01-lc0021-merge-two-sorted-lists.md) · [Roadmap](00-roadmap.md) · [Next: Sort List →](03-lc0148-sort-list.md)

**Practise:** [LeetCode 703](https://leetcode.com/problems/kth-largest-element-in-a-stream/)
**Skill it adds:** **heap basics with `heapq`**: push, pop and peek, and the "keep a min-heap of size k" trick.

## Problem in brief

Build a class that is created with a number `k` and a starting list of scores. Each call `add(val)` adds one more score and returns the **k-th largest** score seen so far (counting duplicates).

## Key idea

**What a heap gives you.** A min-heap is a list kept in a special order: every parent is ≤ its children, so the **smallest item is always at index 0**. Python's `heapq` keeps that order for you:

| Call | What it does | Time |
| --- | --- | --- |
| `heapq.heapify(h)` | turn a list into a heap in place | O(n) |
| `heapq.heappush(h, x)` | add `x` | O(log n) |
| `heapq.heappop(h)` | remove and return the smallest | O(log n) |
| `h[0]` | look at the smallest without removing it | O(1) |

Only `h[0]` is guaranteed. The rest of the list is **not** sorted. `heapq` has no max-heap; push `-x` if you need one.

**The trick.** Keep only the **k largest** scores, in a **min**-heap. The smallest of those k is exactly the k-th largest overall, and it sits at `h[0]`. On each `add`:

1. Push the new value.
2. If the heap now has more than `k` items, pop the smallest. It can't be in the top k any more.
3. Return `h[0]`.

```
k = 3, start [4, 5, 8, 2] → keep [4, 5, 8]       (2 popped)
add(3):  push 3 → [3, 4, 5, 8] → pop 3 → [4, 5, 8] → return 4
add(5):  push 5 → pop 4 → [5, 5, 8]               → return 5
add(10): push 10 → pop 5 → [5, 8, 10]             → return 5
```

(The lists above are shown sorted to make them easy to read. Inside the heap only the smallest is guaranteed to be first.)

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [heap basics](../../course/primers/11-heap-priority-queue.md#heap-basics)
3. [LeetCode Editorial](https://leetcode.com/problems/kth-largest-element-in-a-stream/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+kth+largest+element+in+a+stream)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
import heapq

class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.heap = nums[:]                    # copy; don't change the caller's list
        heapq.heapify(self.heap)               # O(n)
        while len(self.heap) > k:
            heapq.heappop(self.heap)           # drop the smallest until k remain

    def add(self, val: int) -> int:
        heapq.heappush(self.heap, val)
        if len(self.heap) > self.k:
            heapq.heappop(self.heap)
        return self.heap[0]                    # smallest of the k largest
```

</details>

**Time:** constructor O(n log n) in the worst case (heapify, then up to n − k pops) · `add` O(log k) · **Space:** O(k) after construction

## Test cases

| Input | `add` outputs |
| --- | --- |
| `k = 3, nums = [4,5,8,2]`, then add 3, 5, 10, 9, 4 | `4, 5, 5, 8, 8` |
| `k = 1, nums = []`, then add -3, -2, -4, 0, 4 | `-3, -2, -2, 0, 4` |
| `k = 2, nums = [0]`, then add -1, 1, -2, -4, 3 | `-1, 0, 0, 0, 1` |

## Pitfall

- Using a **max**-heap of everything. Then each query has to pop k items (O(k log n)) and the heap grows forever. A size-k **min**-heap is smaller and answers in O(1).
- Reading `heap[-1]` or `heap[k-1]` as if the list were sorted. Only `heap[0]` has a guaranteed position.
- The starting list can have fewer than `k` items (test 2 starts empty). Don't pop in that case. The problem guarantees there are at least `k` items whenever `add` returns.

## What changes in the next step

Here the heap holds plain numbers. In Merge k Sorted Lists it holds the **current head of each list**: pop the smallest, attach it to the answer (step 01's `tail`), and push that node's `next`. Since `ListNode` can't be compared with `<`, you'll push tuples `(node.val, i, node)` where `i` breaks ties. That's approach A of the target. Step 03 teaches approach B, divide and conquer.
