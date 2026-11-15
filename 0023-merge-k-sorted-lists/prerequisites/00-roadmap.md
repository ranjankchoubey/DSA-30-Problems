# Roadmap → Q19 · 23. Merge k Sorted Lists (Hard)

**Goal:** merge `k` sorted linked lists into one sorted list in **O(N log k)** (N = total nodes), once with a **min-heap** and once with **divide and conquer**.

> 📚 **Before you start:** read [Primer 06 · Linked List](../../course/primers/06-linked-list.md) and [Primer 11 · Heap / Priority Queue](../../course/primers/11-heap-priority-queue.md).

## Skills you need

- [ ] Merge two sorted lists with a dummy node and a `tail` pointer
- [ ] Use `heapq` to get the smallest of many items in O(log k)
- [ ] Split a problem in half, solve both halves, and merge the results (divide and conquer)

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Merge Two Sorted Lists](01-lc0021-merge-two-sorted-lists.md) | Merging two lists | [LC 21](https://leetcode.com/problems/merge-two-sorted-lists/) |
| 02 | [Kth Largest Element in a Stream](02-lc0703-kth-largest-element-in-a-stream.md) | Heap basics with `heapq` (first full lesson) | [LC 703](https://leetcode.com/problems/kth-largest-element-in-a-stream/) |
| 03 | [Sort List](03-lc0148-sort-list.md) | Merge sort on a list, i.e. divide-and-conquer merging | [LC 148](https://leetcode.com/problems/sort-list/) |
| ★ | [**Merge k Sorted Lists** → NOTES](../NOTES.md) | Heap of the k heads, or merge the lists in pairs: both O(N log k) | [LC 23](https://leetcode.com/problems/merge-k-sorted-lists/) |

## How the steps add up

- Step 01: merging two sorted lists is O(n + m) with a dummy and a `tail`.
- Step 02: a min-heap returns the smallest item in O(log size), which is how we'll pick the smallest of k heads.
- Step 03: merge sort merges halves level by level, so every node is touched once per level.
- **Target:** approach A keeps the k current heads in a min-heap (step 02 + step 01's `tail`). Approach B merges the lists in pairs, round after round (step 03's merging half + step 01).

## Threads

- **Q29** Network Delay Time and **Q28** Cheapest Flights: the same heap, used for Dijkstra.
- **Q3** Median of Two Sorted Arrays: divide and conquer on sorted data.
- **Q13** and **Q18**: dummy nodes and pointer relinking.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
