# Step 01 · 703. Kth Largest Element in a Stream (recap)

[← Roadmap](00-roadmap.md) · [Next: Shortest Path in Binary Matrix →](02-lc1091-shortest-path-in-binary-matrix.md)

> ↩ **Taught in full in the Q19 ladder:** [Merge k Sorted Lists → Step 02 · Kth Largest Element in a Stream](../../0023-merge-k-sorted-lists/prerequisites/02-lc0703-kth-largest-element-in-a-stream.md)

> 📚 **New to this?** Primer: [heap basics](../../course/primers/11-heap-priority-queue.md#heap-basics)

## Recap

- Python's `heapq` is a **min-heap** on a plain list: `heap[0]` is always the smallest item.
- `heapq.heappush(h, x)` and `heapq.heappop(h)` each cost **O(log n)**, even while new items keep arriving.
- Tuples compare by their first field, so `(distance, node)` pairs come out ordered by distance.

## Why it matters here

Dijkstra repeatedly asks "which unfinished node is **closest** to the source right now?" while new distances keep being discovered. That's exactly a stream of values where you keep popping the smallest, so a min-heap of `(distance, node)` answers it in O(log n) instead of scanning every node.
