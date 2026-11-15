# Step 01 · 200. Number of Islands (Medium)

[← Roadmap](00-roadmap.md) · [Next: Number of Provinces →](02-lc0547-number-of-provinces.md)

**Practise:** [LeetCode 200](https://leetcode.com/problems/number-of-islands/)
**Skill it adds:** **grid connectivity**. Each land cell is a node, its up/down/left/right land cells are its neighbours, and one flood fill visits one whole island.

## Problem in brief

You get a grid of `"1"` (land) and `"0"` (water). An island is a group of land cells joined horizontally or vertically (not diagonally). Count the islands.

## Key idea

Scan every cell. When you find land that no earlier flood has reached, that's a **new island**: add 1, then flood it (DFS or BFS) and mark every connected land cell as seen so it's never counted again.

Here the flood "sinks" land by overwriting `"1"` with `"0"`, which doubles as the visited set.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [DFS](../../course/primers/08-graphs.md#dfs)
3. [LeetCode Editorial](https://leetcode.com/problems/number-of-islands/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+number+of+islands)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        rows, cols = len(grid), len(grid[0])
        islands = 0
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] != "1":
                    continue
                islands += 1                       # land no flood has reached yet
                grid[r][c] = "0"
                stack = [(r, c)]                   # iterative DFS: no recursion limit
                while stack:
                    cr, cc = stack.pop()
                    for nr, nc in ((cr + 1, cc), (cr - 1, cc), (cr, cc + 1), (cr, cc - 1)):
                        if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == "1":
                            grid[nr][nc] = "0"     # sink it when it's pushed
                            stack.append((nr, nc))
        return islands
```

</details>

**Time:** O(m · n): every cell is sunk at most once · **Space:** O(m · n) for the stack in the worst case (an all-land grid).

## Test cases

| Input | Expected |
| --- | --- |
| `[["1","1","1","1","0"],["1","1","0","1","0"],["1","1","0","0","0"],["0","0","0","0","0"]]` | `1` |
| `[["1","1","0","0","0"],["1","1","0","0","0"],["0","0","1","0","0"],["0","0","0","1","1"]]` | `3` |
| `[["0"]]` | `0` |

## Pitfall

A **recursive** flood fill on a 300 × 300 all-land grid goes up to 90,000 calls deep and crashes Python with `RecursionError`. Use a stack (as above) or a BFS queue. Also note that this solution **modifies the input**; copy the grid first if the caller still needs it.

## What changes in the next step

This counts islands **once**, in O(m·n). Number of Islands II adds land one cell at a time and asks for the count after each addition; re-flooding every time would cost O(k·m·n). Step 02 introduces **Union-Find**, which keeps the count up to date as connections arrive.
