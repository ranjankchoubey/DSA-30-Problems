# Primer 08 · Graphs

Read this **before** the Graphs ladders (Q25, Q26, Q27), or when a step's key idea doesn't make sense yet. The Advanced Graphs ladders (Q29, Q28, Q30) build on it too.

A **graph** is a set of **nodes** (vertices) joined by **edges**. Edges can be **directed** (`b → a`: "b comes before a") or **undirected** (`a — b`: "a and b are connected"). Most interview graphs hide behind other words: courses and prerequisites, words that differ by one letter, land cells on a map.

## Graph representation

| Word | Meaning |
| --- | --- |
| `V`, `E` | number of nodes, number of edges |
| neighbour | a node you can reach in one edge |
| in-degree / out-degree | edges coming **into** / going **out of** a node (directed graphs) |
| path | a walk along edges; its **length** is the number of edges (or nodes, read the problem) |
| cycle | a path that comes back to where it started |
| DAG | a **D**irected **A**cyclic **G**raph: directed, no cycles |
| connected component | a group of nodes that can all reach each other (undirected graphs) |

Problems usually give an **edge list**. Turn it into an **adjacency list** first: for each node, the list of its neighbours.

```python
def build_adj(n, edges, directed=True):
    adj = [[] for _ in range(n)]
    for u, v in edges:
        adj[u].append(v)          # u → v
        if not directed:
            adj[v].append(u)      # and v → u
    return adj
```

If nodes are strings (words, cities), use `defaultdict(list)` instead of a list of lists.

| Representation | Space | "Is u — v an edge?" | Visit all neighbours of u |
| --- | --- | --- | --- |
| Edge list `[[u, v], …]` | O(E) | O(E) | O(E) |
| Adjacency list `adj[u] = [v, …]` | O(V + E) | O(deg u) | O(deg u) |
| Adjacency matrix `m[u][v] = 1` | O(V²) | O(1) | O(V) |

Use the adjacency list unless the problem gives you a matrix (LC 547 does).

**Grids are graphs too.** Each cell is a node and its 4 (or 8) neighbours are the edges. You never build `adj`; you compute neighbours on the fly:

```python
def grid_neighbours(r, c, rows, cols):
    for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
        nr, nc = r + dr, c + dc
        if 0 <= nr < rows and 0 <= nc < cols:
            yield nr, nc
```

## BFS

**Breadth-first search** explores in rings: first every node 1 edge away, then 2 edges away, and so on. So the **first time BFS reaches a node is along a shortest path** (when every edge costs the same).

It needs a FIFO queue. In Python that's `collections.deque`:

| Operation | Code | Time |
| --- | --- | --- |
| Add to the back | `q.append(x)` | O(1) |
| Take from the front | `q.popleft()` | O(1) |
| Take from the front of a **list** | `a.pop(0)` | O(n), avoid |
| Size of the queue | `len(q)` | O(1) |

### Template: shortest distance from `start` (level by level)

```python
from collections import deque

def bfs_distances(adj, start):
    dist = {start: 0}
    queue = deque([start])
    while queue:
        u = queue.popleft()
        for v in adj[u]:
            if v not in dist:            # mark when you ENQUEUE
                dist[v] = dist[u] + 1
                queue.append(v)
    return dist
```

When you need the level number itself (minutes, semesters, word count), process one level at a time:

```python
def bfs_levels(adj, start):
    seen = {start}
    queue = deque([start])
    levels = []
    while queue:
        level = []
        for _ in range(len(queue)):      # exactly the nodes of this level
            u = queue.popleft()
            level.append(u)
            for v in adj[u]:
                if v not in seen:
                    seen.add(v)
                    queue.append(v)
        levels.append(level)
    return levels
```

**Time:** O(V + E) · **Space:** O(V)

**Bidirectional BFS** runs one BFS from the start and one from the goal, always growing the smaller frontier, and stops when they touch. If each node has `b` neighbours and the answer is `d` edges away, plain BFS touches about `b^d` nodes and bidirectional BFS about `2·b^(d/2)`. That is a huge saving (see Q26).

## DFS

**Depth-first search** goes as deep as possible along one path, then backs up. It does **not** find shortest paths, but it's the natural tool for "explore everything reachable", counting components and detecting cycles.

### Template: recursive DFS, counting connected components

```python
def count_components(n, adj):
    seen = [False] * n

    def dfs(u):
        seen[u] = True
        for v in adj[u]:
            if not seen[v]:
                dfs(v)

    components = 0
    for u in range(n):
        if not seen[u]:
            dfs(u)                       # floods one whole component
            components += 1
    return components
```

### Template: iterative DFS with a stack (no recursion limit)

```python
def reachable(adj, start):
    seen = {start}
    stack = [start]
    while stack:
        u = stack.pop()                  # pop from the END: last in, first out
        for v in adj[u]:
            if v not in seen:
                seen.add(v)
                stack.append(v)
    return seen
```

**Time:** O(V + E) · **Space:** O(V)

> Python's default recursion limit is about 1000 frames. A path of 2000 nodes (or a 300 × 300 grid flood fill) will crash a recursive DFS with `RecursionError`. Either use the stack version or call `sys.setrecursionlimit(...)` first.

## Topological sort

A **topological order** of a DAG lists the nodes so that every edge `u → v` has `u` before `v`. It exists **if and only if the graph has no cycle**. There may be many valid orders, so check an order by testing every edge, not by comparing to one fixed list.

### Kahn's algorithm (BFS on in-degrees)

Repeatedly take a node with in-degree 0 (nothing left before it), output it, and "remove" its outgoing edges.

```python
from collections import deque

def topo_sort_kahn(n, edges):            # edges: (u, v) means u → v
    adj = [[] for _ in range(n)]
    indegree = [0] * n
    for u, v in edges:
        adj[u].append(v)
        indegree[v] += 1
    queue = deque(u for u in range(n) if indegree[u] == 0)
    order = []
    while queue:
        u = queue.popleft()
        order.append(u)
        for v in adj[u]:
            indegree[v] -= 1
            if indegree[v] == 0:
                queue.append(v)
    return order if len(order) == n else []   # short order ⇒ cycle
```

### DFS with three colours

`WHITE` = not visited, `GREY` = on the current DFS path, `BLACK` = finished. Meeting a **GREY** node means you've walked back onto your own path: a **cycle**. A node turns BLACK only after everything after it is finished, so the **reversed** finishing order is a topological order.

```python
def topo_sort_dfs(n, edges):
    adj = [[] for _ in range(n)]
    for u, v in edges:
        adj[u].append(v)
    WHITE, GREY, BLACK = 0, 1, 2
    color = [WHITE] * n
    finished = []

    def dfs(u):
        color[u] = GREY
        for v in adj[u]:
            if color[v] == GREY:
                return False             # back edge: cycle
            if color[v] == WHITE and not dfs(v):
                return False
        color[u] = BLACK
        finished.append(u)
        return True

    for u in range(n):
        if color[u] == WHITE and not dfs(u):
            return []
    return finished[::-1]
```

**Time:** O(V + E) for both · **Space:** O(V + E)

Why not just "visited / not visited"? In a directed graph you can reach a finished (BLACK) node again through a different branch without any cycle (a diamond `0→1→3`, `0→2→3`). Only GREY means "still on my path".

## Union Find

**Union-Find** (Disjoint Set Union, DSU) keeps track of groups that only ever **merge**. Each group is a tree; its root is the group's name.

- `find(x)`: the root of `x`'s group.
- `union(a, b)`: merge the two groups. Returns `False` if they were already one group (in an undirected graph that edge closes a **cycle**).

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n              # upper bound on tree height
        self.count = n                   # number of groups

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])   # path compression
        return self.parent[x]

    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return False
        if self.rank[ra] < self.rank[rb]:                # union by rank
            ra, rb = rb, ra
        self.parent[rb] = ra             # shorter tree under taller tree
        if self.rank[ra] == self.rank[rb]:
            self.rank[ra] += 1
        self.count -= 1
        return True
```

| Version | `find` / `union` cost |
| --- | --- |
| No optimisation | O(n) worst case (the tree becomes a chain) |
| Union by rank only | O(log n) |
| Path compression + union by rank | O(α(n)) amortised, where α ≤ 4 for any realistic n, so effectively O(1) |

Why Union-Find instead of BFS/DFS? BFS/DFS answer "how many groups?" **once**. If edges or nodes keep arriving and you need the answer **after each one** (Q27), re-running BFS each time costs O(V + E) per step, while Union-Find costs almost O(1).

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "prerequisites", "must come before", "dependencies", "build order" | directed graph → topological sort |
| "is it possible to finish", "deadlock", "circular dependency" | cycle detection (Kahn's or 3-colour DFS) |
| "minimum number of steps / moves / transformations", every move costs the same | BFS |
| start and goal both known, huge branching factor | bidirectional BFS |
| "number of islands / regions / provinces", connected cells | DFS or BFS flood fill, or Union-Find |
| items added one by one, "after each addition, how many groups?" | Union-Find |
| "which edge creates a cycle" in an undirected graph | Union-Find, `union()` returns `False` |
| "trusts", "follows", "points to" with counting | in-degree / out-degree |

## Common pitfalls

- Getting the edge **direction** backwards. In `[a, b]` = "take b before a", the edge is `b → a`.
- Marking nodes visited when you **dequeue** instead of when you **enqueue**. The same node gets queued many times, and BFS can blow up.
- Using `list.pop(0)` as a queue: O(n) per pop.
- Forgetting nodes with **no edges at all**. Loop over `range(n)`, not just the keys of `adj`.
- Using plain visited/not-visited for **directed** cycle detection. You need the GREY state.
- Recursive DFS on long chains or large grids hitting the recursion limit.
- Union-Find without `find()` on both sides: compare **roots**, not `parent[a] == parent[b]`.

## Learn more

- NeetCode roadmap (Graphs node, then Advanced Graphs): <https://neetcode.io/roadmap>
- Python docs: [collections.deque](https://docs.python.org/3/library/collections.html#collections.deque) · [sys.setrecursionlimit](https://docs.python.org/3/library/sys.html#sys.setrecursionlimit)
- Visualise BFS, DFS and topological sort: <https://visualgo.net/en/dfsbfs>
- Visualise graph representations: <https://visualgo.net/en/graphds>
- Visualise Union-Find: <https://visualgo.net/en/ufds>
