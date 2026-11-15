# Step 03 · 684. Redundant Connection (Medium)

[← Previous: Number of Provinces](02-lc0547-number-of-provinces.md) · [Roadmap](00-roadmap.md) · [Target: Number of Islands II →](../NOTES.md)

**Practise:** [LeetCode 684](https://leetcode.com/problems/redundant-connection/)
**Skill it adds:** **path compression + union by rank**, and a `union()` that **returns whether it merged** two groups.

## Problem in brief

A tree with `n` nodes (labelled `1..n`) had one extra edge added, so the graph now has exactly one cycle. You get the `n` edges in order. Return an edge whose removal leaves a tree; if several qualify, return the one that appears **last** in the input.

## Key idea

Add the edges one by one with Union-Find. An edge whose two ends **already** have the same root connects two nodes that were already connected, so it closes the cycle. The first such edge is the answer: every other edge on the cycle came earlier, so it's also the last cycle edge in the input.

Two upgrades make every operation almost O(1):
- **Path compression:** after `find(x)` walks up to the root, point every node on the way **directly** at the root.
- **Union by rank:** keep a `rank` (an upper bound on tree height) per root and hang the shorter tree under the taller one. Heights then stay ≤ log₂ n.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [Union Find](../../course/primers/08-graphs.md#union-find)
3. [LeetCode Editorial](https://leetcode.com/problems/redundant-connection/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+redundant+connection)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
        n = len(edges)
        parent = list(range(n + 1))               # labels are 1..n
        rank = [0] * (n + 1)

        def find(x):
            if parent[x] != x:
                parent[x] = find(parent[x])       # path compression
            return parent[x]

        def union(a, b):
            ra, rb = find(a), find(b)
            if ra == rb:
                return False                      # already connected
            if rank[ra] < rank[rb]:
                ra, rb = rb, ra
            parent[rb] = ra                       # shorter tree under taller tree
            if rank[ra] == rank[rb]:
                rank[ra] += 1
            return True

        for a, b in edges:
            if not union(a, b):
                return [a, b]
        return []
```

</details>

**Time:** O(n · α(n)), effectively O(n); α is the inverse Ackermann function, ≤ 4 for any realistic n · **Space:** O(n)

Union by rank keeps every tree's height ≤ log₂ n, so the recursive `find` never goes deep.

## Test cases

| Input | Expected |
| --- | --- |
| `[[1,2],[1,3],[2,3]]` | `[2,3]` |
| `[[1,2],[2,3],[3,4],[1,4],[1,5]]` | `[1,4]` |
| `[[3,4],[1,2],[2,4],[3,1],[2,5]]` | `[3,1]` (3–4–2–1 already connected) |

## Pitfall

Union by rank compares the ranks of the **roots**, so call `find` first. Comparing `rank[a]` with `rank[b]` for the original nodes (which may not be roots) loses the height guarantee. And only increase the rank when the two ranks were **equal**.

## What changes in the target

Nodes are now grid cells that **appear over time**. Each new land cell adds a group (`count += 1`), then `union` with each of its up-to-4 land neighbours; every `union` that returns `True` does `count -= 1`. A grid can have 10⁸ cells, so store `parent` in a dict for land cells only → [NOTES](../NOTES.md).
