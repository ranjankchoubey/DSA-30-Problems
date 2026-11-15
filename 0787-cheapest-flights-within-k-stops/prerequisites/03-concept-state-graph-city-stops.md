# Step 03 · Concept: state graph (city, stops)

[← Previous: Bellman-Ford in k rounds](02-concept-bellman-ford-k-rounds.md) · [Roadmap](00-roadmap.md) · [Target: Cheapest Flights Within K Stops →](../NOTES.md)

> 📚 **New to this?** Primer: [Dijkstra](../../course/primers/09-advanced-graphs.md#dijkstra)

**Skill it adds:** when a problem limits something along the path (stops, fuel, discounts), make that thing **part of the node**, then run the usual algorithm on the bigger graph.

## Idea

Plain Dijkstra settles each **city** once, at its cheapest price. But the cheapest way into a city may have used so many flights that you can't continue, while a pricier way with fewer flights could still reach `dst`. Plain Dijkstra has already thrown that second way away.

Fix: the node is now the pair **`(city, flights used)`**. Reaching city 2 after 1 flight and after 2 flights are **different states**, so one can't block the other.

- Heap entries: `(cost, city, used)`.
- Settle each `(city, used)` state once.
- Don't take another flight from a state with `used == k + 1`.
- The first time `dst` is popped, its cost is the cheapest over all routes with at most `k + 1` flights.

## Code

```python
import heapq
from collections import defaultdict

def cheapest_by_states(n, flights, src, dst, k):
    graph = defaultdict(list)
    for u, v, p in flights:
        graph[u].append((v, p))
    heap = [(0, src, 0)]                 # (cost, city, flights used)
    settled = set()
    while heap:
        cost, city, used = heapq.heappop(heap)
        if city == dst:
            return cost
        if (city, used) in settled or used == k + 1:
            continue
        settled.add((city, used))
        for nxt, price in graph[city]:
            heapq.heappush(heap, (cost + price, nxt, used + 1))
    return -1
```

**Time:** O(E · k · log(E · k)), because there are up to `n · (k + 2)` states and each edge is pushed once per `used` value · **Space:** O(E · k)

## Walk-through: `flights = [[0,1,1],[1,2,1],[0,2,5],[2,3,1]]`, `src = 0`, `dst = 3`, `k = 1`

| Pop `(cost, city, used)` | Action |
| --- | --- |
| (0, 0, 0) | settle; push (1, 1, 1), (5, 2, 1) |
| (1, 1, 1) | settle; push (2, 2, 2) |
| (2, 2, 2) | used = 2 = k + 1, so no more flights |
| (5, 2, 1) | a **different** state from (2, 2), so settle it; push (6, 3, 2) |
| (6, 3, 2) | city = dst → return **6** |

Plain Dijkstra on cities would skip the `(5, 2, 1)` pop because city 2 was already settled, and return `-1`.

## Test cases

| Call | Expected |
| --- | --- |
| `cheapest_by_states(4, [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]], 0, 3, 1)` | `700` |
| `cheapest_by_states(4, [[0,1,1],[1,2,1],[0,2,5],[2,3,1]], 0, 3, 1)` | `6` |
| `cheapest_by_states(3, [[0,1,100],[1,2,100],[0,2,500]], 0, 2, 0)` | `500` |

## What changes in the target

Nothing new to learn. The target puts both methods side by side under LeetCode's signature, and explains with a counterexample why plain Dijkstra on cities fails → [NOTES](../NOTES.md).
