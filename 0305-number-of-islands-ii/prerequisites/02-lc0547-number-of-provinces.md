# Step 02 · 547. Number of Provinces (Medium)

[← Previous: Number of Islands](01-lc0200-number-of-islands.md) · [Roadmap](00-roadmap.md) · [Next: Redundant Connection →](03-lc0684-redundant-connection.md)

**Practise:** [LeetCode 547](https://leetcode.com/problems/number-of-provinces/)
**Skill it adds:** **Union-Find, the basic version**. Start with every node in its own group, merge groups along each connection, and subtract 1 from the count per real merge.

## Problem in brief

There are `n` cities. `isConnected[i][j] == 1` means cities `i` and `j` are directly connected (the matrix is symmetric, with 1s on the diagonal). A province is a group of cities connected directly or through other cities. Return the number of provinces.

## Key idea

Give each city a `parent`. At first every city is its own parent, i.e. the **root** of its own group, so there are `n` groups.

- `find(x)`: follow `parent` links up until you reach a root. Two cities are in the same group exactly when they have the same root.
- `union(a, b)`: find both roots. If they differ, point one root at the other; two groups became one, so `count -= 1`. If they're equal, do nothing.

Call `union(i, j)` for every connection; `count` is the answer.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [Union Find](../../course/primers/08-graphs.md#union-find)
3. [LeetCode Editorial](https://leetcode.com/problems/number-of-provinces/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+number+of+provinces)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def findCircleNum(self, isConnected: List[List[int]]) -> int:
        n = len(isConnected)
        parent = list(range(n))                   # everyone is their own root
        count = n

        def find(x):
            while parent[x] != x:
                x = parent[x]
            return x

        for i in range(n):
            for j in range(i + 1, n):             # the matrix is symmetric
                if isConnected[i][j]:
                    ri, rj = find(i), find(j)
                    if ri != rj:
                        parent[rj] = ri           # merge two groups
                        count -= 1
        return count
```

</details>

**Time:** O(n² · n) = O(n³) worst case: n² matrix cells, and without any balancing a `find` can walk a chain of length n · **Space:** O(n)

DFS from each unvisited city (as in step 01) would be O(n²) here. Union-Find is introduced now because it will shine when connections **arrive one by one**.

## Test cases

| Input | Expected |
| --- | --- |
| `[[1,1,0],[1,1,0],[0,0,1]]` | `2` ({0, 1} and {2}) |
| `[[1,0,0],[0,1,0],[0,0,1]]` | `3` |
| `[[1]]` | `1` |

## Pitfall

Compare **roots**, not parents: `parent[i] == parent[j]` can be false even when `i` and `j` are in the same group (one may be several links below the root). And set `parent[rj] = ri` on the **root** `rj`, not on `j`, or you'd split `j` away from its old group.

## What changes in the next step

This `find` can walk a long chain, since nothing keeps the trees short. Step 03 adds **path compression** (point every node on the walk straight at the root) and **union by rank** (hang the shorter tree under the taller one), making each operation almost O(1). It also makes `union()` return whether a merge happened.
