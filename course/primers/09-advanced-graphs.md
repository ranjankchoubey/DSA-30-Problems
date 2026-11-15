# Primer 09 · Advanced Graphs

Read this **before** the Advanced Graphs ladders (Q29, Q28, Q30), or when a step's key idea doesn't make sense yet. It builds on [Primer 08 · Graphs](08-graphs.md) (BFS, DFS, topological sort).

Plain BFS finds shortest paths when every edge costs the same. This primer covers graphs where edges have **different costs**: travel times, prices, distances.

## Weighted graphs

Each edge carries a number, its **weight**. Store neighbours as `(neighbour, weight)` pairs:

```python
from collections import defaultdict

times = [[2, 1, 1], [2, 3, 1], [3, 4, 1]]   # [u, v, w]: u -> v costs w
graph = defaultdict(list)
for u, v, w in times:
    graph[u].append((v, w))                 # directed; add (u, w) to graph[v] too if undirected
# graph == {2: [(1, 1), (3, 1)], 3: [(4, 1)]}
```

| Representation | Memory | "Neighbours of u" | Used by |
| --- | --- | --- | --- |
| Adjacency list `graph[u] = [(v, w), …]` | O(V + E) | O(deg u) | Dijkstra |
| Edge list `[(u, v, w), …]` (the input as given) | O(E) | O(E) | Bellman-Ford |
| Matrix `dist[u][v] = w` (∞ if no edge) | O(V²) | O(V) | Floyd-Warshall |

Two things to check in every problem: are nodes numbered `0..n−1` or `1..n`, and can weights be **negative**? The answer decides the algorithm.

**Why BFS isn't enough:** BFS settles nodes in order of **number of edges**. With weights, a path with more edges can be cheaper (`1 →10→ 2` vs `1 →1→ 3 →1→ 2`), so BFS's first visit is not the cheapest.

## Dijkstra

Shortest distance from one source to every node, when **all weights are ≥ 0**. It's BFS with the queue replaced by a **min-heap** keyed by distance.

```python
import heapq

def dijkstra(graph, src):
    dist = {}                               # settled node -> final distance
    heap = [(0, src)]
    while heap:
        d, u = heapq.heappop(heap)
        if u in dist:
            continue                        # stale entry: u already settled
        dist[u] = d
        for v, w in graph[u]:
            if v not in dist:
                heapq.heappush(heap, (d + w, v))
    return dist
```

**Time:** O(E log E) · **Space:** O(V + E)

**Why it works:** the popped node is the closest unsettled one. Any other route to it must leave the settled area through a node still in the heap, which is already at least as far, and the rest of that route adds weights ≥ 0. So the first pop of a node is **final**.

| `heapq` operation | Code | Time |
| --- | --- | --- |
| Push | `heapq.heappush(h, (d, u))` | O(log n) |
| Pop the smallest | `heapq.heappop(h)` | O(log n) |
| Peek at the smallest | `h[0]` | O(1) |
| Turn a list into a heap | `heapq.heapify(a)` | O(n) |

Tuples compare field by field, so `(distance, node)` pops the smallest distance first. To carry extra state, add fields: `(cost, city, flights_used)`.

**Extra state:** if the problem limits something along the path (stops, fuel), make it part of the node: run Dijkstra on `(node, used)` pairs (Q28).

## Bellman Ford

Shortest distances from one source, even with **negative** weights. Relax every edge, in rounds:

```python
def bellman_ford(n, edges, src):
    INF = float("inf")
    dist = [INF] * n
    dist[src] = 0
    for _ in range(n - 1):                  # a shortest path has at most n-1 edges
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    for u, v, w in edges:                   # still improving after n-1 rounds?
        if dist[u] + w < dist[v]:
            return None                     # a negative cycle is reachable
    return dist
```

**Time:** O(V · E) · **Space:** O(V)

- **Relaxing** an edge `u → v` means: "is going through `u` a cheaper way to reach `v`?"
- After round `i`, every path with at most `i` edges has been found. That's why `n − 1` rounds are enough, and why an `n`-th round that still improves something proves a **negative cycle**.
- **Limit the number of edges to `k`** (Q28): run exactly `k` rounds, and read from a **copy** of the previous round so one round can't chain two edges:

```python
def bellman_ford_k_edges(n, edges, src, k):
    INF = float("inf")
    dist = [INF] * n
    dist[src] = 0
    for _ in range(k):
        new_dist = dist[:]
        for u, v, w in edges:
            if dist[u] + w < new_dist[v]:
                new_dist[v] = dist[u] + w
        dist = new_dist
    return dist
```

## Floyd Warshall

Shortest distances between **every pair** of nodes. Allow intermediate node `m = 0, 1, …` one at a time:

```python
def floyd_warshall(n, edges):
    INF = float("inf")
    dist = [[INF] * n for _ in range(n)]
    for i in range(n):
        dist[i][i] = 0
    for u, v, w in edges:
        dist[u][v] = min(dist[u][v], w)
    for m in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][m] + dist[m][j] < dist[i][j]:
                    dist[i][j] = dist[i][m] + dist[m][j]
    return dist                             # dist[i][i] < 0 means a negative cycle
```

**Time:** O(V³) · **Space:** O(V²). Fine up to a few hundred nodes; works with negative edges.

## Which algorithm when

| Situation | Use | Time |
| --- | --- | --- |
| Every edge costs the same | BFS ([Primer 08](08-graphs.md#bfs)) | O(V + E) |
| One source, weights ≥ 0 | Dijkstra | O(E log E) |
| One source, some weights < 0, or must detect a negative cycle | Bellman-Ford | O(V · E) |
| Path may use at most `k` edges | Bellman-Ford with `k` rounds, or Dijkstra on `(node, edges used)` | O(k · E) |
| Every pair of nodes, small graph | Floyd-Warshall | O(V³) |
| "X must come before Y", find an order | Topological sort ([Primer 08](08-graphs.md#topological-sort)) | O(V + E) |

## Topological sort recap

Alien Dictionary (Q30) isn't a shortest-path problem. It **derives** "letter X comes before letter Y" edges from the input, then runs the topological sort from [Primer 08](08-graphs.md#topological-sort). If the sort can't place every node, there's a cycle and no valid order.

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "minimum time / cost / distance", edges with different costs | Dijkstra |
| "time for **all** nodes to receive", "network delay" | Dijkstra, answer = largest distance |
| "at most k stops / edges / transfers" | Bellman-Ford in rounds, or Dijkstra with the count in the state |
| negative weights, "arbitrage", "detect a negative cycle" | Bellman-Ford |
| "shortest path between every pair", n ≤ ~400 | Floyd-Warshall |
| "sorted in an unknown order", "derive the order" | build edges, then topological sort |

## Common pitfalls

- Marking a node done when you **push** it (BFS's rule). In Dijkstra a node is final only when **popped**.
- Not skipping **stale** heap entries, so nodes are processed again and correct distances get overwritten.
- Using Dijkstra with **negative** edges. It returns wrong answers without any error.
- Forgetting the **copy** in limited-round Bellman-Ford, which lets one round use several edges.
- Off-by-one between "stops" and "edges": `k` stops = `k + 1` edges.
- 1-indexed nodes: size arrays `n + 1`, and skip index 0 when taking a max.
- Unreachable nodes stay at `∞`. Convert to `-1` (or whatever the problem asks) at the end.

## Learn more

- NeetCode roadmap (Advanced Graphs node): <https://neetcode.io/roadmap>
- Python docs: [heapq](https://docs.python.org/3/library/heapq.html) · [collections.defaultdict](https://docs.python.org/3/library/collections.html#collections.defaultdict)
- Visualise Dijkstra and Bellman-Ford (single-source shortest paths): <https://visualgo.net/en/sssp>
- Visualise topological sort: <https://visualgo.net/en/dfsbfs>
