# 0787. Cheapest Flights Within K Stops

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q28 | [787](https://leetcode.com/problems/cheapest-flights-within-k-stops/) | Medium | Advanced Graphs | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0787-cheapest-flights-within-k-stops.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Budget Airline Router:** an airline search engine must find the cheapest price from city `src` to city `dst` using **at most `k` stops** (`k + 1` flights), or `-1` if that's impossible. Explain why plain Dijkstra on cities fails, and solve it with Bellman-Ford in `k + 1` rounds or Dijkstra over `(city, stops)` states.

## 2. Pattern recognition

- **Pattern:** Advanced Graphs, shortest path **with a limit on the number of edges**
- **Signals in the statement:** "cheapest" on a weighted directed graph (shortest path), plus "at most `k` stops" (a second quantity that the path must respect).
- **Core idea in one line:** the answer depends on (price, flights used), not price alone, so either count flights with Bellman-Ford rounds or put the count into Dijkstra's state.

## 3. Approaches

### Approach 1: Plain Dijkstra on cities (wrong)

Settle each city once at its cheapest price, and refuse to fly on from a city reached with `k + 1` flights. It fails because the cheapest way into a city can block a pricier way that used fewer flights (§8). **Time:** O(E log E) · **Space:** O(n + E)

<details>
<summary><b>Show the broken version</b></summary>

```python
import heapq
from collections import defaultdict

def cheapest_plain_dijkstra(n, flights, src, dst, k):
    graph = defaultdict(list)
    for u, v, p in flights:
        graph[u].append((v, p))
    heap = [(0, src, 0)]                 # (cost, city, flights used)
    settled = set()
    while heap:
        cost, city, used = heapq.heappop(heap)
        if city == dst:
            return cost
        if city in settled:              # BUG: one entry per city
            continue
        settled.add(city)
        if used == k + 1:
            continue
        for nxt, price in graph[city]:
            heapq.heappush(heap, (cost + price, nxt, used + 1))
    return -1
```

</details>

### Approach 2: Dijkstra over `(city, flights used)` states ([step 03](./prerequisites/03-concept-state-graph-city-stops.md))

Each state is settled once, so a pricier route with fewer flights survives. **Time:** O(E · k · log(E · k)) · **Space:** O(E · k)

<details>
<summary><b>Show Dijkstra over states</b></summary>

```python
import heapq
from collections import defaultdict

class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        graph = defaultdict(list)
        for u, v, p in flights:
            graph[u].append((v, p))
        heap = [(0, src, 0)]             # (cost, city, flights used)
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

</details>

### Approach 3: Bellman-Ford in `k + 1` rounds (optimal)

After round `i`, `cost[c]` is the cheapest price to `c` using at most `i` flights ([step 02](./prerequisites/02-concept-bellman-ford-k-rounds.md)). Copy the array each round so a round adds at most one flight. **Time:** O(k · E) · **Space:** O(n)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        INF = float("inf")
        cost = [INF] * n
        cost[src] = 0
        for _ in range(k + 1):           # k stops = k + 1 flights
            new_cost = cost[:]           # read last round, write this round
            for u, v, p in flights:
                if cost[u] + p < new_cost[v]:
                    new_cost[v] = cost[u] + p
            cost = new_cost
        return -1 if cost[dst] == INF else cost[dst]
```

</details>

## 4. Why it works

- **Bellman-Ford invariant:** after round `i`, `cost[c]` is the cheapest price over all routes to `c` with **at most `i` flights**. Round `i + 1` extends each of those by exactly one flight, because it reads only the previous round's array.
- So after `k + 1` rounds, `cost[dst]` is the cheapest route with at most `k + 1` flights, which is at most `k` stops.
- **Dijkstra over states:** prices are non-negative, so the usual "first pop is final" argument holds in the bigger graph of `(city, used)` states. Every state has `used ≤ k + 1`, so the first pop of `dst` is the cheapest valid route.

## 5. Dry run: TC 1 and TC 2 (Bellman-Ford)

`flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]]`, `src = 0`, `dst = 3`

| After round | Max flights | cost[0] | cost[1] | cost[2] | cost[3] |
| --- | --- | --- | --- | --- | --- |
| start | 0 | 0 | ∞ | ∞ | ∞ |
| 1 | 1 | 0 | 100 | ∞ | ∞ |
| 2 | 2 | 0 | 100 | 200 | **700** |
| 3 | 3 | 0 | 100 | 200 | **400** |

- TC 1 (`k = 1`) stops after round 2: **700** via 0 → 1 → 3.
- TC 2 (`k = 2`) stops after round 3: **400** via 0 → 1 → 2 → 3.
- In round 3, flight 2 → 0 offers 300 for city 0, which doesn't beat 0.

## 6. Edge cases & pitfalls

- **Stops vs flights:** `k` stops means `k + 1` flights, so run `k + 1` rounds. Off by one here is the most common bug.
- **Forgetting the copy** lets one round chain several flights (step 02 pitfall: TC 3 would return 200 instead of 500).
- Compare against `cost[u]` (old) but write to and compare with `new_cost[v]`, so two flights into the same city in one round keep the cheaper.
- Unreachable `dst` → `-1`. `src == dst` is excluded by LeetCode's constraints, but both methods would return 0.
- In the state version, don't settle by city alone (Approach 1).

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `n = 4, flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]], src = 0, dst = 3, k = 1` | `700` |
| TC 2 | same graph, `k = 2` | `400` |
| TC 3 | `n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 0` | `500` |

## 8. Follow-up questions (from course doc)

- **Q: Why does plain Dijkstra on cities fail?**
  **A:** It keeps one result per city, the cheapest. But a cheaper route into a city may use **more flights**, leaving none to finish the trip, while a pricier route with fewer flights would have made it. Dijkstra has already discarded that second route.
  Example: `flights = [[0,1,1],[1,2,1],[0,2,5],[2,3,1]], src = 0, dst = 3, k = 1` (at most 2 flights). The answer is 0 → 2 → 3 = **6**.

  | Pop `(cost, city, used)` | Plain Dijkstra on cities |
  | --- | --- |
  | (0, 0, 0) | settle 0; push (1, 1, 1), (5, 2, 1) |
  | (1, 1, 1) | settle 1; push (2, 2, 2) |
  | (2, 2, 2) | settle **city 2**, but used = 2, so it can't fly on |
  | (5, 2, 1) | city 2 already settled → **skipped** |
  | heap empty | returns **-1** ✗ |

  This also shows why TC 1 can't be solved by "shortest path, then check the stop count": the cheapest route 0 → 1 → 2 → 3 (400) has 2 stops, and the right answer for `k = 1` is a pricier route (700).
- **Q: Give a solution using Bellman-Ford with `k + 1` rounds.**
  **A:** Approach 3. Round `i` = best price using at most `i` flights, and the per-round copy keeps it that way. O(k · E) time, O(n) space.
- **Q: Or Dijkstra over `(city, stops)` states.**
  **A:** Approach 2. The flight count is part of the node, so `(2, used=1)` and `(2, used=2)` are settled separately. In the example above, `(5, 2, 1)` is no longer skipped and leads to `(6, 3, 2)` → 6.
- **Q: Which would you pick?**
  **A:** Bellman-Ford: shorter, O(k · E) with no log factor, and easy to argue correct. Dijkstra over states can stop early when `dst` is popped, which helps when the cheap route is found quickly on a large graph.

## 9. Threads

- **Q29** Network Delay Time: the same two algorithms without a stop limit.
- **Q26** Word Ladder: BFS levels count edges, just like Bellman-Ford rounds here.
- **Q19** Merge k Sorted Lists: the heap loop.

## 10. One-line revision

> At most k stops = k + 1 flights: run k + 1 Bellman-Ford rounds, reading last round's copy; plain Dijkstra fails because a cheaper route with too many flights blocks a valid one.
