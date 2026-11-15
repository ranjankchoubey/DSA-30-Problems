# 0305. Number of Islands II

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q27 | [305](https://leetcode.com/problems/number-of-islands-ii/) 🔒 | Hard | Graphs (Union-Find) | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0305-number-of-islands-ii.py` (save it yourself, see the 🔒 note below)

> 🔒 **LeetCode Premium:** LeetHub can't sync this without Premium. Save your solution yourself as `0305-number-of-islands-ii/0305-number-of-islands-ii.py`, or practise the free alternative: [200. Number of Islands](https://leetcode.com/problems/number-of-islands/) + [684. Redundant Connection](https://leetcode.com/problems/redundant-connection/).

## 1. Case scenario

**Growing Islands on a Map:** a satellite map is an `m × n` grid of water. Land cells are added one at a time; after each addition, report the number of islands (4-directional). Use Union-Find with path compression and union by rank, and give the complexity per operation. `m, n` up to 10,000 (so up to 10⁸ cells); up to 20,000 positions.

## 2. Pattern recognition

- **Pattern:** Graphs, Union-Find (dynamic connectivity)
- **Signals in the statement:** "added one at a time", "after each addition, report the number of", groups that only **merge**, never split. Re-running a flood fill per addition is too slow.
- **Core idea in one line:** a new land cell is a new island (`+1`); union it with each land neighbour, and every union that actually merges two islands is `−1`.

## 3. Approaches

Let k = number of positions.

### Approach 1: Brute force: flood fill after every addition

Mark the cell as land, then count islands from scratch with DFS/BFS ([step 01](./prerequisites/01-lc0200-number-of-islands.md)). **Time:** O(k · m · n), up to 2·10¹² here · **Space:** O(m · n)

### Approach 2: Union-Find over an array of all m·n cells

`parent` and `rank` as lists of size m·n. **Time:** O(m · n + k · α(m·n)) · **Space:** O(m · n), which is 10⁸ entries for a 10,000 × 10,000 grid: too much memory.

### Approach 3: Union-Find over a dict of land cells only (optimal)

Same algorithm, but `parent`/`rank` are dicts that only hold cells that have become land. A cell is land exactly when it's a key in `parent`. **Time:** O(k · α(k)), effectively O(k) · **Space:** O(k)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def numIslands2(self, m: int, n: int, positions: List[List[int]]) -> List[int]:
        parent = {}                              # only land cells are keys
        rank = {}

        def find(x):
            if parent[x] != x:
                parent[x] = find(parent[x])      # path compression
            return parent[x]

        def union(a, b):
            ra, rb = find(a), find(b)
            if ra == rb:
                return False                     # already the same island
            if rank[ra] < rank[rb]:
                ra, rb = rb, ra
            parent[rb] = ra                      # union by rank
            if rank[ra] == rank[rb]:
                rank[ra] += 1
            return True

        count = 0
        result = []
        for r, c in positions:
            cell = r * n + c                     # one integer id per cell
            if cell in parent:                   # duplicate position: nothing changes
                result.append(count)
                continue
            parent[cell] = cell
            rank[cell] = 0
            count += 1                           # a brand-new island
            for nr, nc in ((r + 1, c), (r - 1, c), (r, c + 1), (r, c - 1)):
                if 0 <= nr < m and 0 <= nc < n and nr * n + nc in parent:
                    if union(cell, nr * n + nc):
                        count -= 1               # two islands became one
            result.append(count)
        return result
```

</details>

## 4. Why it works

- **Invariant:** after each step, `count` equals the number of distinct roots among land cells, i.e. the number of islands.
- Adding an isolated cell creates one new root: `+1`. Each neighbour union either finds the two cells already share a root (no change) or merges two different islands into one: `−1`.
- Two neighbours of the new cell may already be in the **same** island. Add `(0,0)`, `(0,1)`, `(1,0)`, then `(1,1)`: its up and left neighbours are both in one island, so the first union merges (`2 → 1`) and the second returns `False` (stays 1). Counting only `True` unions handles this.
- Path compression plus union by rank make each `find` O(α(k)) amortised, and union by rank keeps tree height ≤ log₂ k, so the recursive `find` stays shallow.

## 5. Dry run: TC 3, `m = 3, n = 3`, `positions = [[0,0],[0,1],[1,2],[1,1]]` → `[1,1,2,1]`

Neighbours are checked in the order down, up, right, left. Cell id = `r * 3 + c`.

| add | id | `count` after +1 | land neighbours → union result | `count` | output so far |
| --- | --- | --- | --- | --- | --- |
| (0,0) | 0 | 1 | none | 1 | `[1]` |
| (0,1) | 1 | 2 | left (0,0): merged | 1 | `[1, 1]` |
| (1,2) | 5 | 2 | none ((0,2), (2,2), (1,1) are water) | 2 | `[1, 1, 2]` |
| (1,1) | 4 | 3 | up (0,1): merged → 2; right (1,2): merged → 1 | 1 | `[1, 1, 2, 1]` |

The last cell joins the `{(0,0), (0,1)}` island and the `{(1,2)}` island into one.

## 6. Edge cases & pitfalls

- **Duplicate positions:** adding land that's already land must not do `count += 1`. Check `cell in parent` first and just repeat the current count.
- **Two neighbours in the same island:** only subtract when `union` returns `True`.
- **Cell id:** `r * n + c` (multiply by the number of **columns**). `r * m + c` collides when `m ≠ n`.
- **Memory:** an `m × n` array can be 10⁸ entries. The dict holds at most k = 20,000 cells.
- **Boundaries:** check `0 <= nr < m` and `0 <= nc < n` before computing a neighbour's id. Without it, `(r, -1)` becomes id `r * n − 1`, which is the cell `(r − 1, n − 1)` at the end of the previous row.
- 1 × 1 grid (TC 2): one addition, one island.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `m = 3, n = 3, positions = [[0,0],[0,1],[1,2],[2,1]]` | `[1,1,2,3]` |
| TC 2 | `m = 1, n = 1, positions = [[0,0]]` | `[1]` |
| TC 3 | `m = 3, n = 3, positions = [[0,0],[0,1],[1,2],[1,1]]` | `[1,1,2,1]` |

## 8. Follow-up questions (from course doc)

- **Q: What is the complexity per operation?**
  **A:** Each added position does 1 `make-set` plus at most 4 `union`s, each with 2 `find`s. With path compression **and** union by rank, a `find` costs **O(α(N)) amortised**, where α is the inverse Ackermann function (≤ 4 for any N that fits in the universe). So each addition is **O(α(N)) ≈ O(1) amortised**, and all k additions take O(k · α(k)). Space is O(k) with the dict (O(m·n) with arrays).
  For comparison: no optimisation gives O(N) per `find` (trees can become chains); union by rank alone gives O(log N); path compression alone gives O(log N) amortised.
- **Q: What if the same position is added twice?**
  **A:** It's already land and already in an island, so the island count can't change. The `cell in parent` check appends the current `count` and skips the rest. Without it, `count` would go up by 1 and the cell's `parent` would be reset, cutting it off from its island.
- **Q: Why not just BFS after every addition?**
  **A:** That's O(m · n) per addition (Approach 1), up to 10⁸ × 20,000. Union-Find never revisits old cells; it only looks at the new cell's 4 neighbours.
- **Q: What if land can also turn back into water?**
  **A:** Union-Find can merge groups but can't split them. If all operations are known in advance, process them **in reverse** (removals become additions). Otherwise you need a different structure or a recount of the affected island.

## 9. Threads

- **Q6** Longest Consecutive Sequence: can also be solved by union-ing `x` with `x + 1`.
- **Q26** Word Ladder and **Q25** Course Schedule II: other graphs that are never given as an edge list.

## 10. One-line revision

> New land → its own set (+1); union with each land neighbour, −1 per union that really merges; dict-based parent with path compression + union by rank = O(α) per addition; skip duplicates.
