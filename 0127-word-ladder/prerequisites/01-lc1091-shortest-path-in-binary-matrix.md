# Step 01 · 1091. Shortest Path in Binary Matrix (Medium)

[← Roadmap](00-roadmap.md) · [Next: Minimum Genetic Mutation →](02-lc0433-minimum-genetic-mutation.md)

**Practise:** [LeetCode 1091](https://leetcode.com/problems/shortest-path-in-binary-matrix/)
**Skill it adds:** **BFS level by level**. On a graph where every move costs the same, the level at which BFS first reaches a node **is** its shortest distance.

## Problem in brief

You get an `n × n` grid of 0s (open) and 1s (blocked). Move from the top-left cell to the bottom-right cell through open cells only. From a cell you may step to any of its **8** neighbours (diagonals too). Return the number of **cells** on the shortest such path, counting both ends, or `-1` if there's no path.

## Key idea

Every cell is a node, and every open neighbour is an edge of cost 1. BFS explores the grid in **rings**: first the cells 1 cell away from the start, then those 2 away, and so on. So the first time BFS pops the target, the current ring number is the shortest path length.

Process the queue **one level at a time**: `for _ in range(len(queue))` handles exactly the cells of the current ring, then `length += 1`.

## Why BFS gives the shortest path

A FIFO queue holds at most two levels at once: the rest of level `d`, then the start of level `d + 1`. Every cell at distance `d` is popped before any cell at distance `d + 1`. So when a cell is first discovered, it's discovered from the closest possible cell, and no later route can be shorter.

DFS has no such guarantee. It can reach the target first by a long winding path.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [BFS](../../course/primers/08-graphs.md#bfs)
3. [LeetCode Editorial](https://leetcode.com/problems/shortest-path-in-binary-matrix/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+shortest+path+in+binary+matrix)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def shortestPathBinaryMatrix(self, grid: List[List[int]]) -> int:
        n = len(grid)
        if grid[0][0] == 1 or grid[n - 1][n - 1] == 1:
            return -1
        visited = {(0, 0)}
        queue = deque([(0, 0)])
        length = 1                                # cells on the path so far
        while queue:
            for _ in range(len(queue)):           # exactly the cells of this level
                r, c = queue.popleft()
                if r == n - 1 and c == n - 1:
                    return length
                for dr in (-1, 0, 1):
                    for dc in (-1, 0, 1):
                        nr, nc = r + dr, c + dc
                        if (0 <= nr < n and 0 <= nc < n and grid[nr][nc] == 0
                                and (nr, nc) not in visited):
                            visited.add((nr, nc))  # mark on ENQUEUE
                            queue.append((nr, nc))
            length += 1
        return -1
```

</details>

**Time:** O(n²), as each cell is queued at most once and has 8 neighbours · **Space:** O(n²) for `visited` and the queue.

## Dry run: `[[0,0,0],[1,1,0],[1,1,0]]` → 4

| `length` | cells popped (this level) | newly queued |
| --- | --- | --- |
| 1 | (0,0) | (0,1) |
| 2 | (0,1) | (0,2), (1,2) |
| 3 | (0,2), (1,2) | (2,2) |
| 4 | (2,2) = target | return **4** |

## Test cases

| Input | Expected |
| --- | --- |
| `[[0,1],[1,0]]` | `2` (one diagonal step) |
| `[[0,0,0],[1,1,0],[1,1,0]]` | `4` |
| `[[1,0,0],[1,1,0],[1,1,0]]` | `-1` (start is blocked) |

## Pitfall

Mark a cell visited when you **enqueue** it, not when you pop it. Otherwise a cell with several open neighbours gets queued several times, and on a big open grid the queue grows far beyond n². Also check that the **start and target** are open before starting; a blocked start would otherwise be "found" at length 1 on a 1 × 1 grid.

## What changes in the next step

The nodes here are grid cells, and the neighbours are the 8 surrounding cells. In Minimum Genetic Mutation the nodes are **strings** and a neighbour is any string in the bank that differs in **one letter**. The BFS loop stays the same.
