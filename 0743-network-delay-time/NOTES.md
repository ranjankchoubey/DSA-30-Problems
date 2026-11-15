# 0743. Network Delay Time

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q29 | [743](https://leetcode.com/problems/network-delay-time/) | Medium | Advanced Graphs | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0743-network-delay-time.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Signal Propagation in a Network:** a signal starts at node `k` of a directed network, and `times[i] = [u, v, w]` means it takes `w` time units to go from `u` to `v`. Return the time until all `n` nodes have the signal, or `-1` if some node never gets it. Solve it with Dijkstra and a heap, then explain when Bellman-Ford or Floyd-Warshall would be required or preferred, and what breaks with a negative edge.

## 2. Pattern recognition

- **Pattern:** Advanced Graphs, single-source shortest path (Dijkstra)
- **Signals in the statement:** "directed **weighted**", "minimum time", "from one node to **all** nodes", and weights that are never negative (LeetCode: `0 ≤ w ≤ 100`).
- **Core idea in one line:** find the shortest time from `k` to every node; every node has the signal once the **farthest** one does, so the answer is the largest of those times.

## 3. Approaches

All three compute shortest distances from `k`; the answer is `max(dist)`, or `-1` if any node is unreachable.

### Approach 1: Bellman-Ford

Relax every edge (`dist[v] = min(dist[v], dist[u] + w)`) in rounds. A shortest path uses at most `n − 1` edges, so `n − 1` rounds are enough. Stop early when a round changes nothing. **Time:** O(n · E) · **Space:** O(n)

<details>
<summary><b>Show Bellman-Ford</b></summary>

```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], n: int, k: int) -> int:
        INF = float("inf")
        dist = [INF] * (n + 1)          # nodes are 1..n; index 0 unused
        dist[k] = 0
        for _ in range(n - 1):
            changed = False
            for u, v, w in times:
                if dist[u] + w < dist[v]:
                    dist[v] = dist[u] + w
                    changed = True
            if not changed:
                break
        best = max(dist[1:])
        return -1 if best == INF else best
```

</details>

### Approach 2: Floyd-Warshall

Compute the shortest time between **every** pair, allowing intermediate nodes `1..m` one at a time, then read row `k`. Overkill for one source, but tiny code and fine for `n ≤ 100`. **Time:** O(n³) · **Space:** O(n²)

<details>
<summary><b>Show Floyd-Warshall</b></summary>

```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], n: int, k: int) -> int:
        INF = float("inf")
        dist = [[INF] * (n + 1) for _ in range(n + 1)]
        for i in range(1, n + 1):
            dist[i][i] = 0
        for u, v, w in times:
            dist[u][v] = min(dist[u][v], w)
        for m in range(1, n + 1):               # allow m as a stop-over
            for i in range(1, n + 1):
                for j in range(1, n + 1):
                    if dist[i][m] + dist[m][j] < dist[i][j]:
                        dist[i][j] = dist[i][m] + dist[m][j]
        best = max(dist[k][1:])
        return -1 if best == INF else best
```

</details>

### Approach 3: Dijkstra with a min-heap (optimal)

BFS with a heap instead of a queue ([step 03](./prerequisites/03-concept-why-bfs-fails-with-weights.md)). Pop the closest unsettled node, settle it, and push its neighbours with their new distances. Skip stale entries for nodes that are already settled. **Time:** O(E log E) · **Space:** O(n + E)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
import heapq
from collections import defaultdict

class Solution:
    def networkDelayTime(self, times: List[List[int]], n: int, k: int) -> int:
        graph = defaultdict(list)
        for u, v, w in times:
            graph[u].append((v, w))
        dist = {}                       # settled nodes -> final distance
        heap = [(0, k)]
        while heap:
            d, u = heapq.heappop(heap)
            if u in dist:
                continue                # stale entry: u was settled earlier
            dist[u] = d
            for v, w in graph[u]:
                if v not in dist:
                    heapq.heappush(heap, (d + w, v))
        return max(dist.values()) if len(dist) == n else -1
```

</details>

## 4. Why it works

- **Invariant:** when a node is popped for the first time, its distance `d` is the true shortest distance.
- **Why:** every other route to it must leave the settled area through some node still in the heap, whose distance is already ≥ `d`. Weights are ≥ 0, so the rest of that route can only add time. Nothing can beat `d`.
- Each node is settled once, and each edge pushes at most one entry, so the heap holds at most E entries: O(E log E).
- A node that's never popped is unreachable, so `len(dist) < n` means `-1`. Otherwise the last node to get the signal sets the answer: `max(dist.values())`.

## 5. Dry run: TC 1 `times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2` → 2

| Pop | Already settled? | Settle | Push | Heap after |
| --- | --- | --- | --- | --- |
| (0, 2) | no | dist[2] = 0 | (1, 1), (1, 3) | [(1, 1), (1, 3)] |
| (1, 1) | no | dist[1] = 1 | — (no out-edges) | [(1, 3)] |
| (1, 3) | no | dist[3] = 1 | (2, 4) | [(2, 4)] |
| (2, 4) | no | dist[4] = 2 | — | [] |

All 4 nodes settled, so the answer is `max(0, 1, 1, 2)` = **2**.

## 6. Edge cases & pitfalls

- Nodes are numbered **1..n**, not 0..n−1. Size arrays `n + 1` or use a dict.
- **Unreachable node** (TC 3): it's never popped, so check `len(dist) == n` before taking the max.
- **Stale heap entries:** the same node can be pushed several times with different distances. Skip it if already settled, or you'll redo work (and with `dist` as a dict you'd overwrite a correct value).
- **Parallel edges** (two edges `u → v`) and self-loops need no special handling.
- `n = 1`: the source alone, answer `0`.
- Don't mark a node as done when you **push** it (that's BFS's rule). In Dijkstra a node is final only when it's **popped**.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2` | `2` |
| TC 2 | `times = [[1,2,1]], n = 2, k = 1` | `1` |
| TC 3 | `times = [[1,2,1]], n = 2, k = 2` | `-1` (node 1 is unreachable from 2) |

## 8. Follow-up questions (from course doc)

- **Q: Solve it with Dijkstra and a heap.**
  **A:** Approach 3 above. The heap always gives the closest unsettled node in O(log E), and that node's distance is final because weights are non-negative (§4).
- **Q: When would Bellman-Ford be required or preferred?**
  **A:**
  - **Required with negative edges.** Dijkstra's "popped = final" rule breaks (see below). Bellman-Ford still works as long as there is no negative cycle.
  - **Required to detect a negative cycle.** Run one more round after `n − 1`. If anything still improves, a negative cycle is reachable and "shortest time" is undefined.
  - **Preferred with a limit on the number of edges.** Round `i` gives the best cost using at most `i` edges, which is exactly what Q28 (at most `k` stops) needs.
  - **Preferred for its simplicity or in distributed settings.** It's a plain loop over an edge list, and each node only needs its neighbours' current distances. Distance-vector routing protocols such as RIP work this way.
- **Q: When would Floyd-Warshall be preferred?**
  **A:** When you need **all-pairs** shortest paths, e.g. "what's the delay if the signal starts at *any* node?", on a **small, dense** graph. With `n = 100` that's 10⁶ steps, cheaper than coding n separate Dijkstra runs, and it handles negative edges (a negative `dist[i][i]` reveals a negative cycle). For one source on a sparse graph it's wasteful.
- **Q: What breaks if a negative edge exists?**
  **A:** Dijkstra settles a node when it's popped, assuming no later path can be cheaper. A negative edge discovered later **can** make it cheaper, and the code never revisits a settled node.
  Example: `times = [[1,2,4],[1,3,5],[3,2,-3],[2,4,10]], n = 4, k = 1`.

  | Pop | Settle | Push |
  | --- | --- | --- |
  | (0, 1) | dist[1] = 0 | (4, 2), (5, 3) |
  | (4, 2) | dist[2] = **4** | (14, 4) |
  | (5, 3) | dist[3] = 5 | nothing: 2 is already settled, so 5 − 3 = 2 is ignored |
  | (14, 4) | dist[4] = **14** | — |

  Dijkstra returns **14**, but the true distances are `2 → 2` (via 3) and `4 → 12`, so the answer is **12**. Bellman-Ford returns 12.
  With a **negative cycle**, times can drop forever, so no answer exists. The version below reports it.
  Physically, a negative delay means bad data, so validating `w ≥ 0` up front is also a fair answer.

<details>
<summary><b>Show Bellman-Ford with a negative-cycle check</b></summary>

```python
def network_delay_bellman_ford(times, n, k):
    INF = float("inf")
    dist = [INF] * (n + 1)
    dist[k] = 0
    for _ in range(n - 1):
        for u, v, w in times:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    for u, v, w in times:               # an n-th round that still improves
        if dist[u] + w < dist[v]:       # means a reachable negative cycle
            return None
    best = max(dist[1:])
    return -1 if best == INF else best
```

`network_delay_bellman_ford([[1,2,4],[1,3,5],[3,2,-3],[2,4,10]], 4, 1)` → `12`. With a cycle such as `[[1,2,1],[2,3,-2],[3,2,1]]` it returns `None`.

</details>

## 9. Threads

- **Q28** Cheapest Flights Within K Stops: Bellman-Ford limited to `k + 1` rounds, or Dijkstra over `(city, stops)` states.
- **Q19** Merge k Sorted Lists: the same heap loop (pop the smallest, push what follows it).
- **Q26** Word Ladder: BFS, which is Dijkstra when every edge costs 1.

## 10. One-line revision

> Dijkstra: min-heap of (time, node); the first pop of a node is final (weights ≥ 0); answer = max time, or -1 if not every node was popped.
