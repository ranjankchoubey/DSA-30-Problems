# Roadmap → Q29 · 743. Network Delay Time (Medium)

**Goal:** a signal starts at node `k` of a directed network where each edge has a travel time. Return how long it takes until **every** node has the signal (the largest shortest-path distance from `k`), or `-1` if some node is never reached.

> 📚 **Before you start:** read [Primer 09 · Advanced Graphs](../../course/primers/09-advanced-graphs.md).

## Skills you need

- [ ] Pop the smallest item from a min-heap with `heapq`
- [ ] Use BFS level by level to find shortest paths when every edge costs 1
- [ ] Explain why BFS breaks once edges have different weights, and why negative weights break things even more

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Kth Largest Element in a Stream](01-lc0703-kth-largest-element-in-a-stream.md) ↩ recap | Heap: get the smallest item in O(log n) | [LC 703](https://leetcode.com/problems/kth-largest-element-in-a-stream/) |
| 02 | [Shortest Path in Binary Matrix](02-lc1091-shortest-path-in-binary-matrix.md) ↩ recap | BFS shortest path | [LC 1091](https://leetcode.com/problems/shortest-path-in-binary-matrix/) |
| 03 | [Why BFS fails with weights](03-concept-why-bfs-fails-with-weights.md) | First visit ≠ cheapest once weights differ; negative edges break "popped = final" | — |
| ★ | [**Network Delay Time** → NOTES](../NOTES.md) | Dijkstra: BFS with a min-heap ordered by distance | [LC 743](https://leetcode.com/problems/network-delay-time/) |

## How the steps add up

- Step 01: a heap hands back the smallest item quickly, even while new items keep arriving.
- Step 02: BFS settles nodes in order of distance, as long as every edge costs 1.
- Step 03: with weights, "fewest edges" is no longer "cheapest", so first-visit order is wrong.
- **Target:** swap BFS's queue for step 01's heap, keyed by distance. The first time a node is popped, its distance is final.

## Threads

- **Q28** Cheapest Flights Within K Stops: Dijkstra and Bellman-Ford again, with a limit on the number of edges.
- **Q19** Merge k Sorted Lists: the same "pop the smallest from a heap, push its successor" loop.
- **Q26** Word Ladder: BFS shortest path on an unweighted graph.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
