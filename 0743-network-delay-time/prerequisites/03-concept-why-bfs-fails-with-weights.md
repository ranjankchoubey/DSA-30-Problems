# Step 03 · Concept: why BFS fails with weights

[← Previous: Shortest Path in Binary Matrix](02-lc1091-shortest-path-in-binary-matrix.md) · [Roadmap](00-roadmap.md) · [Target: Network Delay Time →](../NOTES.md)

> 📚 **New to this?** Primer: [weighted graphs](../../course/primers/09-advanced-graphs.md#weighted-graphs)

**Skill it adds:** seeing that BFS's "first visit is the shortest" rule only holds when every edge costs the same, and knowing what a fix must guarantee.

## Idea

BFS visits nodes in order of **number of edges**. When every edge costs 1, that is also the order of total cost, so the first visit is final. With weights, a path with **more** edges can be **cheaper**:

```
1 --10--> 2
1 --1--> 3 --1--> 2
```

BFS reaches node 2 straight from node 1 (1 edge, cost 10) and never looks again. The cheapest route is 1 → 3 → 2, cost 2.

## Code

Edges are `[u, v, w]` triples with nodes `1..n`, the same format as Network Delay Time.

```python
from collections import deque

def bfs_first_visit(n, edges, src):
    # WRONG with weights: fixes a node's distance the first time it is reached
    graph = {u: [] for u in range(1, n + 1)}
    for u, v, w in edges:
        graph[u].append((v, w))
    dist = {src: 0}
    queue = deque([src])
    while queue:
        u = queue.popleft()
        for v, w in graph[u]:
            if v not in dist:
                dist[v] = dist[u] + w
                queue.append(v)
    return dist


def bfs_relax(n, edges, src):
    # Correct: re-queue a node whenever a cheaper route to it is found
    graph = {u: [] for u in range(1, n + 1)}
    for u, v, w in edges:
        graph[u].append((v, w))
    dist = {src: 0}
    queue = deque([src])
    while queue:
        u = queue.popleft()
        for v, w in graph[u]:
            if dist[u] + w < dist.get(v, float("inf")):
                dist[v] = dist[u] + w
                queue.append(v)
    return dist
```

**`bfs_first_visit`:** O(V + E), but wrong. **`bfs_relax`:** correct, but a node can be re-processed many times, so the worst case is far slower than O(V + E).

## Test cases

| Call | Edges | Result |
| --- | --- | --- |
| `bfs_first_visit(3, edges, 1)` | `[[1,2,10],[1,3,1],[3,2,1]]` | `{1: 0, 2: 10, 3: 1}` ✗ (node 2 should be 2) |
| `bfs_relax(3, edges, 1)` | `[[1,2,10],[1,3,1],[3,2,1]]` | `{1: 0, 2: 2, 3: 1}` ✓ |
| `bfs_relax(4, edges, 2)` | `[[2,1,1],[2,3,1],[3,4,1]]` | `{2: 0, 1: 1, 3: 1, 4: 2}` ✓ (largest = 2, Q29 TC 1) |

## What a fix must guarantee

`bfs_relax` is correct because it never trusts a distance until nothing can improve it. That's wasteful. A better rule: **always expand the closest unfinished node next**. If every weight is ≥ 0, no later path can be cheaper than that node's current distance, since any other route already costs at least as much before it even arrives. So its distance is **final** the moment it is picked.

## What breaks with a negative edge

That "final when picked" argument needs every weight ≥ 0. Add a negative edge and it fails:

```
1 --2--> 2
1 --3--> 3 --(-2)--> 2
```

Picking the closest node first settles node 2 at distance 2, but 1 → 3 → 2 costs 1. `bfs_relax(3, [[1,2,2],[1,3,3],[3,2,-2]], 1)` still finds `{1: 0, 2: 1, 3: 3}` because it keeps re-checking. That re-checking idea is **Bellman-Ford**, which you'll meet in the Q28 ladder.

## What changes in the target

Replace the FIFO queue with step 01's **min-heap** of `(distance, node)`. Pop the closest node; if it was already settled, skip it; otherwise its distance is final, so record it and push its neighbours. Each node is settled once: **O(E log E)**. That is Dijkstra → [NOTES](../NOTES.md).
