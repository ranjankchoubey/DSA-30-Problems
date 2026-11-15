# Step 02 · Concept: Bellman-Ford in k rounds

[← Previous: Network Delay Time](01-q29-network-delay-time.md) · [Roadmap](00-roadmap.md) · [Next: State graph (city, stops) →](03-concept-state-graph-city-stops.md)

> 📚 **New to this?** Primer: [Bellman-Ford](../../course/primers/09-advanced-graphs.md#bellman-ford)

**Skill it adds:** edge **relaxation** in rounds, where round `i` means "cheapest price using at most `i` flights", and copying the array each round so one round can't sneak in extra flights.

## Idea

Keep `cost[c]` = the cheapest known price to reach city `c`. One **round** looks at every flight `u → v` with price `p` and tries `cost[u] + p` as a new price for `v` (that's "relaxing" the edge).

- Before any round, only `src` is reachable (0 flights).
- After round 1, `cost` holds the best price using **at most 1** flight. After round `i`, at most `i` flights.
- "At most `k` stops" = at most `k + 1` flights, so run exactly **`k + 1` rounds**.

**The copy matters.** Each round must read prices from the **previous** round only. If you update `cost` in place, a flight relaxed early in a round can feed a later flight in the **same** round, and one round then adds two or more flights.

## Code

```python
def cheapest_bellman_ford(n, flights, src, dst, k):
    INF = float("inf")
    cost = [INF] * n
    cost[src] = 0
    for _ in range(k + 1):              # k stops = k + 1 flights
        new_cost = cost[:]              # read old prices, write new ones
        for u, v, p in flights:
            if cost[u] + p < new_cost[v]:
                new_cost[v] = cost[u] + p
        cost = new_cost
    return -1 if cost[dst] == INF else cost[dst]


def cheapest_no_copy(n, flights, src, dst, k):
    # BUG: updates in place, so one round can chain several flights
    INF = float("inf")
    cost = [INF] * n
    cost[src] = 0
    for _ in range(k + 1):
        for u, v, p in flights:
            if cost[u] + p < cost[v]:
                cost[v] = cost[u] + p
    return -1 if cost[dst] == INF else cost[dst]
```

**Time:** O(k · E) · **Space:** O(n)

## Walk-through: `flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]]`, `src = 0`, `dst = 3`

| After round | Max flights | cost[0] | cost[1] | cost[2] | cost[3] |
| --- | --- | --- | --- | --- | --- |
| start | 0 | 0 | ∞ | ∞ | ∞ |
| 1 | 1 | 0 | 100 | ∞ | ∞ |
| 2 | 2 | 0 | 100 | 200 | **700** (0→1→3) |
| 3 | 3 | 0 | 100 | 200 | **400** (0→1→2→3) |

So `k = 1` (2 rounds) gives **700** and `k = 2` (3 rounds) gives **400**.

## Test cases

| Call | Expected |
| --- | --- |
| `cheapest_bellman_ford(4, flights above, 0, 3, 1)` | `700` |
| `cheapest_bellman_ford(4, flights above, 0, 3, 2)` | `400` |
| `cheapest_bellman_ford(3, [[0,1,100],[1,2,100],[0,2,500]], 0, 2, 0)` | `500` (direct flight only) |

## Pitfall

`cheapest_no_copy(3, [[0,1,100],[1,2,100],[0,2,500]], 0, 2, 0)` returns **200**. In its only round it sets `cost[1] = 100`, then immediately uses it to set `cost[2] = 200`. That's 2 flights when only 1 is allowed.

## What changes in the next step

Bellman-Ford fixes the limit by thinking in **rounds**. Step 03 keeps Dijkstra instead and fixes it by putting the flight count **inside the state**: `(city, flights used)`.
