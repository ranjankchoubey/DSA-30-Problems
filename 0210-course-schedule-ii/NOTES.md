# 0210. Course Schedule II

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q25 | [210](https://leetcode.com/problems/course-schedule-ii/) | Medium | Graphs (Topological Sort) | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0210-course-schedule-ii.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**University Course Planner:** `numCourses` courses and pairs `[a, b]` meaning "b before a". Return one order that finishes every course, or `[]` if it's impossible. Solve it with Kahn's algorithm (BFS) **and** DFS with cycle detection, then explain the minimum number of semesters when any number of courses fits in a semester. Up to 2000 courses and 5000 pairs.

## 2. Pattern recognition

- **Pattern:** Graphs, topological sort
- **Signals in the statement:** "prerequisite", "must be taken before", "one valid order", "impossible". Dependencies form a directed graph; an order exists only if it has no cycle.
- **Core idea in one line:** repeatedly take a course whose prerequisites are all done (in-degree 0); if some courses are never freed, there's a cycle.

## 3. Approaches

### Approach 1: Brute force: rescan for a ready course

Keep a set of taken courses. Each round, scan every course for one that isn't taken and whose prerequisites are all taken; if none is found before everything is taken, there's a cycle. Up to V rounds, each scanning all courses and pairs. **Time:** O(V · (V + E)) · **Space:** O(V + E)

### Approach 2: Kahn's algorithm, BFS on in-degrees (optimal)

Store in-degrees so "is it ready?" is O(1), and only re-check the courses a finished course unlocks. **Time:** O(V + E) · **Space:** O(V + E)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        adj = [[] for _ in range(numCourses)]
        indegree = [0] * numCourses
        for a, b in prerequisites:
            adj[b].append(a)                     # b → a
            indegree[a] += 1
        queue = deque(c for c in range(numCourses) if indegree[c] == 0)
        order = []
        while queue:
            course = queue.popleft()
            order.append(course)
            for nxt in adj[course]:
                indegree[nxt] -= 1
                if indegree[nxt] == 0:           # last prerequisite just finished
                    queue.append(nxt)
        return order if len(order) == numCourses else []
```

</details>

### Approach 3: DFS with 3-colour cycle detection

A course turns BLACK only after every course it unlocks is BLACK. So the finishing order lists each course **after** everything that depends on it, and reversing it gives a valid order. Meeting a GREY node means a cycle. **Time:** O(V + E) · **Space:** O(V + E) (recursion depth up to V)

<details>
<summary><b>Show DFS solution</b></summary>

```python
import sys

class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        sys.setrecursionlimit(10_000)            # chains can be 2000 courses deep
        adj = [[] for _ in range(numCourses)]
        for a, b in prerequisites:
            adj[b].append(a)
        WHITE, GREY, BLACK = 0, 1, 2
        color = [WHITE] * numCourses
        finished = []

        def dfs(u):                              # returns False if a cycle is found
            color[u] = GREY
            for v in adj[u]:
                if color[v] == GREY:
                    return False
                if color[v] == WHITE and not dfs(v):
                    return False
            color[u] = BLACK
            finished.append(u)                   # everything after u is already done
            return True

        for c in range(numCourses):
            if color[c] == WHITE and not dfs(c):
                return []
        return finished[::-1]
```

</details>

## 4. Why it works

- **Kahn's:** a course enters the queue only when its in-degree reaches 0, i.e. after **every** prerequisite has been output. So each output course comes after all its prerequisites.
- If there's a cycle, every course on it keeps in-degree ≥ 1 (its predecessor on the cycle is never output), so `len(order) < numCourses` and we return `[]`. If there's no cycle, some remaining course always has in-degree 0, so all of them get output.
- **DFS:** when `u` turns BLACK, every course reachable from `u` is already BLACK and already in `finished`. So in `finished`, each course appears **after** the courses it unlocks; reversed, it appears **before** them.
- A GREY node is an ancestor on the current path. An edge to it closes a loop, which is a cycle. Edges to BLACK nodes are safe.

## 5. Dry run: TC 1, `numCourses = 4`, `prerequisites = [[1,0],[2,0],[3,1],[3,2]]`

`adj = [[1,2],[3],[3],[]]`, starting `indegree = [0,1,1,2]`.

| pop | `order` after | in-degree updates | queue after |
| --- | --- | --- | --- |
| — | `[]` | — | `[0]` |
| 0 | `[0]` | 1: 1→0 (push), 2: 1→0 (push) | `[1, 2]` |
| 1 | `[0, 1]` | 3: 2→1 | `[2]` |
| 2 | `[0, 1, 2]` | 3: 1→0 (push) | `[3]` |
| 3 | `[0, 1, 2, 3]` | — | `[]` |

`len(order) = 4`, so return **`[0, 1, 2, 3]`**. The DFS version finishes in the order `3, 1, 2, 0` and returns `[0, 2, 1, 3]`, the other valid answer.

## 6. Edge cases & pitfalls

- **Direction:** `[a, b]` means `b → a`, and the in-degree goes to `a`. Swapping them returns a reversed order.
- **Cycle** (TC 2): Kahn's queue starts empty, `order = []`, so we return `[]`. Don't return the partial order.
- **Courses with no pairs** (TC 3) have in-degree 0 and go into the first queue. Build `indegree` for all `range(numCourses)`.
- **Self-loop** `[0, 0]`: in-degree 1 that never drops, so `[]`. In DFS, 0 sees itself GREY.
- **Recursion depth:** a chain of 2000 courses exceeds Python's default limit (~1000), hence `setrecursionlimit` in the DFS version. Kahn's has no recursion.
- **Many valid answers:** check an order by testing every pair, not by comparing with one expected list.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]` | `[0,1,2,3]` or `[0,2,1,3]` |
| TC 2 | `numCourses = 2, prerequisites = [[1,0],[0,1]]` | `[]` (cycle) |
| TC 3 | `numCourses = 1, prerequisites = []` | `[0]` |

## 8. Follow-up questions (from course doc)

- **Q: Solve it with Kahn's algorithm (BFS) and with DFS cycle detection. How do they compare?**
  **A:** Both are O(V + E) time and space (§3). Kahn's builds the order front to back and detects a cycle by **counting** (fewer than V courses output). DFS builds it back to front (reversed finishing order) and detects a cycle by meeting a **GREY** node. Kahn's has no recursion-depth issue, and its queue levels give the semester count below for free.
- **Q: What is the minimum number of semesters if a student can take unlimited courses per semester?**
  **A:** Run Kahn's **level by level**: semester 1 is every course with in-degree 0, semester 2 is every course freed by semester 1, and so on. A course can't start earlier than its level, because its longest chain of prerequisites forces that many semesters, and taking the whole level at once never delays anything. So the answer is the **number of BFS levels**, which equals the number of courses on the **longest path** in the DAG. If not every course gets taken, there's a cycle: return `-1`. TC 1 → 3 semesters (`{0}`, `{1, 2}`, `{3}`); TC 3 → 1. **Time:** O(V + E).
  With a cap of `k` courses per semester the greedy no longer works (LeetCode 1494, Parallel Courses II, needs bitmask DP).

<details>
<summary><b>Show the semesters version</b></summary>

```python
def min_semesters(num_courses, prerequisites):
    adj = [[] for _ in range(num_courses)]
    indegree = [0] * num_courses
    for a, b in prerequisites:
        adj[b].append(a)
        indegree[a] += 1
    queue = deque(c for c in range(num_courses) if indegree[c] == 0)
    semesters = 0
    taken = 0
    while queue:
        semesters += 1
        for _ in range(len(queue)):              # every course available this semester
            course = queue.popleft()
            taken += 1
            for nxt in adj[course]:
                indegree[nxt] -= 1
                if indegree[nxt] == 0:
                    queue.append(nxt)
    return semesters if taken == num_courses else -1
```

</details>

## 9. Threads

- **Q30** [Alien Dictionary](../0269-alien-dictionary/NOTES.md): builds letter edges from adjacent words, then runs this topological sort.
- **Q26** Word Ladder: the same level-by-level BFS loop as the semesters follow-up, used for shortest paths.
- **Q27** Number of Islands II: another problem that is a graph in disguise (grid cells instead of courses).

## 10. One-line revision

> Edge b → a with in-degree on a; queue every in-degree-0 course, pop and free its neighbours; fewer than n popped means a cycle → []. Levels of that BFS = minimum semesters.

<!-- course-nav:start -->

---

### Course navigation

**Problem 23 of 30** · Graphs (1 of 3)

[← Previous: Q21 · Serialize and Deserialize Binary Tree](../0297-serialize-and-deserialize-binary-tree/NOTES.md) · [Course index](../INDEX.md) · [Next: Q26 · Word Ladder →](../0127-word-ladder/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
