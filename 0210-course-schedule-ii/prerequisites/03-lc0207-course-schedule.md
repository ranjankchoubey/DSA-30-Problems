# Step 03 · 207. Course Schedule (Medium)

[← Previous: Find the Town Judge](02-lc0997-find-the-town-judge.md) · [Roadmap](00-roadmap.md) · [Target: Course Schedule II →](../NOTES.md)

**Practise:** [LeetCode 207](https://leetcode.com/problems/course-schedule/)
**Skill it adds:** **cycle detection in a directed graph**, done two ways: Kahn's algorithm and 3-colour DFS.

## Problem in brief

There are `numCourses` courses and pairs `[a, b]` meaning "take b before a". Return `True` if it's possible to finish every course, otherwise `False`.

## Key idea

It's impossible **exactly when the prerequisites form a cycle** (0 needs 1, 1 needs 0). So this is "does the directed graph have a cycle?".

- **Kahn's algorithm:** put every course with in-degree 0 in a queue. Take one, and lower the in-degree of everything it unlocks; any that hit 0 join the queue. If you take all `numCourses` courses, there's no cycle. Courses on a cycle never reach in-degree 0, so they never get taken.
- **3-colour DFS:** WHITE = unvisited, GREY = on the current path, BLACK = done. Reaching a GREY node means you've come back around to your own path, which is a cycle.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [topological sort](../../course/primers/08-graphs.md#topological-sort)
3. [LeetCode Editorial](https://leetcode.com/problems/course-schedule/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+course+schedule)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b> (Kahn's algorithm, then 3-colour DFS)</summary>

```python
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        adj = [[] for _ in range(numCourses)]
        indegree = [0] * numCourses
        for a, b in prerequisites:
            adj[b].append(a)
            indegree[a] += 1
        queue = deque(c for c in range(numCourses) if indegree[c] == 0)
        taken = 0
        while queue:
            course = queue.popleft()
            taken += 1
            for nxt in adj[course]:
                indegree[nxt] -= 1
                if indegree[nxt] == 0:
                    queue.append(nxt)
        return taken == numCourses
```

```python
import sys

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        sys.setrecursionlimit(10_000)             # a chain of 2000 courses is 2000 calls deep
        adj = [[] for _ in range(numCourses)]
        for a, b in prerequisites:
            adj[b].append(a)
        WHITE, GREY, BLACK = 0, 1, 2
        color = [WHITE] * numCourses

        def has_cycle(u):
            color[u] = GREY
            for v in adj[u]:
                if color[v] == GREY:
                    return True                   # back to our own path
                if color[v] == WHITE and has_cycle(v):
                    return True
            color[u] = BLACK
            return False

        return not any(color[c] == WHITE and has_cycle(c) for c in range(numCourses))
```

</details>

**Time:** O(V + E) · **Space:** O(V + E) for both, where V = courses and E = pairs.

## Test cases

| Input | Expected |
| --- | --- |
| `numCourses = 2, prerequisites = [[1,0]]` | `True` (take 0, then 1) |
| `numCourses = 2, prerequisites = [[1,0],[0,1]]` | `False` (0 ↔ 1 cycle) |
| `numCourses = 4, prerequisites = [[1,0],[2,1],[3,2],[1,3]]` | `False` (1 → 2 → 3 → 1) |

## Pitfall

A two-state DFS (visited / not visited) reports false cycles. In the diamond `0 → 1 → 3`, `0 → 2 → 3`, node 3 is reached a second time from 2 without any cycle. That's why finished nodes are BLACK, and only GREY counts as a cycle.

## What changes in the target

Course Schedule II wants the **order**, not just yes/no. In Kahn's, append each course to a list when you pop it. In DFS, append each course when it turns BLACK, then reverse the list. Return `[]` instead of `False` → [NOTES](../NOTES.md).
