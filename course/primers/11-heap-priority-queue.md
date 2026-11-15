# Primer 11 · Heap / Priority Queue

Read this **before** the heap steps (Q19 step 02, then Q29 and Q28), or when a step's key idea doesn't make sense yet.

## Heap basics

A **min-heap** always gives you its **smallest** item in O(1), and lets you add or remove an item in **O(log n)**. A sorted list would make every insert O(n); a heap only keeps "partly sorted" order, which is cheaper.

How it works: a heap is a binary tree stored in a plain list. The children of index `i` sit at `2i + 1` and `2i + 2`, and **every parent is ≤ its children**. So the smallest item is always at index 0. A push adds at the end and swaps upward; a pop moves the last item to the top and swaps downward. Each walk is one root-to-leaf path, so it takes O(log n).

```
list:  [1, 3, 2, 7, 4, 5]          tree:        1
                                              /   \
                                             3     2
                                            / \   /
                                           7   4 5
```

Only `heap[0]` is guaranteed. The rest of the list is **not** sorted.

Python's `heapq` module works on an ordinary list:

```python
import heapq

heap = [5, 1, 4]
heapq.heapify(heap)           # O(n): now a valid heap
heapq.heappush(heap, 2)       # O(log n)
smallest = heap[0]            # O(1) peek -> 1
smallest = heapq.heappop(heap)  # O(log n) remove -> 1
```

| Operation | Code | Time |
| --- | --- | --- |
| Turn a list into a heap | `heapq.heapify(a)` | O(n) |
| Push | `heapq.heappush(h, x)` | O(log n) |
| Pop the smallest | `heapq.heappop(h)` | O(log n) |
| Peek the smallest | `h[0]` | O(1) |
| Push, then pop the smallest | `heapq.heappushpop(h, x)` | O(log n) |
| k largest / k smallest of n items | `heapq.nlargest(k, a)` / `nsmallest` | O(n log k) |
| Size | `len(h)` | O(1) |

**Max-heap:** `heapq` only has a min-heap. Push `-x` and negate again when you pop.

**Tuples:** items are compared like tuples, field by field. Push `(priority, item)` to order by priority. If two priorities can be equal and the items can't be compared (a `ListNode` has no `<`), put a unique tie-breaker in the middle: `(priority, i, item)`.

```python
h = []
heapq.heappush(h, (3, "c"))
heapq.heappush(h, (1, "a"))
heapq.heappop(h)              # (1, 'a')
```

## Top k pattern

To track the **k largest** items, keep a **min-heap of size k**. Its root is the smallest of the k largest, which is the **k-th largest** overall. When the heap grows past k, pop the root, since it can no longer be in the top k.

```python
def kth_largest(nums, k):
    heap = []
    for x in nums:
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)    # drop the smallest
    return heap[0]
```

**Time:** O(n log k) · **Space:** O(k). For the k **smallest**, use a max-heap of size k (push `-x`).

## Merging sorted streams

To merge k sorted sequences, keep **one item per sequence** (its current front) in a min-heap. Pop the smallest, output it, then push the next item from the same sequence. The heap never holds more than k items, so each step is O(log k), and N items in total cost **O(N log k)**.

```python
def merge_sorted(seqs):                     # seqs: list of sorted lists
    heap = [(s[0], i, 0) for i, s in enumerate(seqs) if s]
    heapq.heapify(heap)
    out = []
    while heap:
        val, i, j = heapq.heappop(heap)
        out.append(val)
        if j + 1 < len(seqs[i]):
            heapq.heappush(heap, (seqs[i][j + 1], i, j + 1))
    return out
```

For plain Python iterables, `heapq.merge(*seqs)` does the same thing lazily. The same "pop the closest, push its neighbours" loop is the heart of Dijkstra (Q29, Q28).

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "k-th largest / smallest", "top k", "k closest" | size-k heap (min-heap for largest, max-heap for smallest) |
| "merge k sorted …" | heap of k fronts, O(N log k) |
| a stream of values and a running "best so far" query | heap kept between calls |
| "always process the cheapest / earliest next" | priority queue (Dijkstra, scheduling) |
| "median of a stream" | two heaps (a max-heap and a min-heap) |

## Common pitfalls

- Treating the heap list as sorted. Only `h[0]` is guaranteed.
- Forgetting that `heapq` is a **min**-heap. Negate values for a max-heap.
- Pushing items that can't be compared (`ListNode`, dicts) without a tie-breaker → `TypeError` when two priorities are equal.
- Using a heap of all n items when a size-k heap is enough (O(n) space instead of O(k)).
- Calling `heapify` again after every push. Use `heappush`, which keeps the heap valid.

## Learn more

- NeetCode roadmap (Heap / Priority Queue node): <https://neetcode.io/roadmap>
- Python docs: [heapq](https://docs.python.org/3/library/heapq.html)
- Visualise a binary heap: <https://visualgo.net/en/heap>
