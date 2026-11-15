# Step 01 · Concept: from edge list to adjacency list

[← Roadmap](00-roadmap.md) · [Next: Find the Town Judge →](02-lc0997-find-the-town-judge.md)

**Skill it adds:** turning a list of pairs into "for each node, who comes next?", with the edge pointing the **right way**.

> 📚 **New to this?** Primer: [graph representation](../../course/primers/08-graphs.md#graph-representation)

## Idea

Course problems give prerequisites as pairs `[a, b]`, meaning **b must be taken before a**. Read it as an arrow `b → a`: "after b, a becomes possible".

To answer "which courses does finishing `b` unlock?" quickly, store the arrows in an **adjacency list**: `adj[b]` is the list of every `a` with `[a, b]` in the input. Building it is one pass over the pairs.

## Code

```python
def build_graph(num_courses, prerequisites):
    adj = [[] for _ in range(num_courses)]   # one list per course, even with no edges
    for a, b in prerequisites:
        adj[b].append(a)                      # b → a: b unlocks a
    return adj
```

**Time:** O(V + E) · **Space:** O(V + E), where V = courses and E = pairs.

## Walk-through: `numCourses = 4`, `prerequisites = [[1,0],[2,0],[3,1],[3,2]]`

| pair `[a, b]` | arrow | `adj` after |
| --- | --- | --- |
| `[1, 0]` | 0 → 1 | `[[1], [], [], []]` |
| `[2, 0]` | 0 → 2 | `[[1, 2], [], [], []]` |
| `[3, 1]` | 1 → 3 | `[[1, 2], [3], [], []]` |
| `[3, 2]` | 2 → 3 | `[[1, 2], [3], [3], []]` |

```
      0
     / \
    1   2
     \ /
      3
```

## Test cases

| Input | Expected `adj` |
| --- | --- |
| `4, [[1,0],[2,0],[3,1],[3,2]]` | `[[1,2],[3],[3],[]]` |
| `2, [[1,0],[0,1]]` | `[[1],[0]]` (each points to the other: a cycle) |
| `3, []` | `[[],[],[]]` (courses with no edges still get a list) |

## Pitfall

Writing `adj[a].append(b)` builds every arrow backwards. That graph is still a valid graph, so nothing crashes, but a topological sort on it returns the order **reversed**. Always say the pair out loud: "b before a, so b → a".

## What changes in the next step

The adjacency list says where arrows **go**. Step 02 counts how many arrows **arrive** at each node (in-degree) and leave it (out-degree), which is the number Kahn's algorithm needs.
