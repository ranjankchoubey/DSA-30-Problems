# 0084. Largest Rectangle in Histogram

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q15 | [84](https://leetcode.com/problems/largest-rectangle-in-histogram/) | Hard | Stack | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0084-largest-rectangle-in-histogram.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Skyline Billboard:** buildings of width 1 have the given heights. Place the largest rectangular billboard that fits entirely inside the skyline and return its area. Required: O(n) with a monotonic stack, then explain how the same idea solves the largest rectangle of 1s in a binary matrix. `1 ≤ n ≤ 100,000`, `0 ≤ heights[i] ≤ 10,000`.

## 2. Pattern recognition

- **Pattern:** Stack, monotonic (increasing) stack
- **Signals in the statement:** bars, "largest rectangle", O(n). Each bar can stretch until it meets a **shorter** bar on each side, which is a previous/next smaller element question.
- **Core idea in one line:** keep bar indices with increasing heights. When a shorter bar arrives, each popped bar's rectangle is bounded by the new bar on the right and the bar beneath it on the stack on the left.

## 3. Approaches

### Approach 1: Every pair of edges

For every left edge `i`, extend the right edge `j` and keep a running minimum height. **Time:** O(n²) · **Space:** O(1)

### Approach 2: Expand each bar ([step 02](./prerequisites/02-concept-expand-each-bar.md))

Treat each bar as the shortest bar and stretch it left and right. **Time:** O(n²) · **Space:** O(1)

### Approach 3: Previous and next smaller arrays ([step 03](./prerequisites/03-concept-previous-and-next-smaller-element.md))

Two stack passes, then `h × (next − prev − 1)` for every bar. **Time:** O(n) · **Space:** O(n)

### Approach 4: One-pass monotonic stack (optimal)

Compute each bar's area at the moment it is popped. A height-0 sentinel at the end pops the rest. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        stack = []                        # indices; heights increasing bottom → top
        best = 0
        for i, h in enumerate(heights + [0]):        # the 0 sentinel pops every bar at the end
            while stack and heights[stack[-1]] >= h:
                height = heights[stack.pop()]
                left = stack[-1] if stack else -1    # previous smaller bar
                best = max(best, height * (i - left - 1))
            stack.append(i)
        return best
```

</details>

## 4. Why it works

- **Invariant:** the stack holds indices whose heights strictly increase from bottom to top.
- **Right boundary:** bar `j` is popped by the first bar `i` after it with `heights[i] ≤ heights[j]`. Every bar between them is taller than `j`, otherwise it would have popped `j` first. So a rectangle of height `heights[j]` reaches `i − 1`.
- **Left boundary:** after popping `j`, the new top `left` is the nearest bar to the left that is shorter than `heights[j]`. The bars between `left` and `j` are gone because `j`, or a bar that `j` later popped, was no taller than them. So they are all at least as tall as `j`, and the rectangle reaches `left + 1`.
- So the popped bar's best rectangle is `heights[j] × (i − left − 1)`, and the best rectangle overall has some shortest bar `j`, whose pop computes it.
- **Equal heights:** with `>=`, the earlier of two equal bars is popped by the later one, and its width stops short. The **last** bar of an equal run is popped by a truly shorter bar and gets the full width, so the maximum is still found.
- **O(n):** each index is pushed once and popped once.

## 5. Dry run: `heights = [2, 1, 5, 6, 2, 3]` → `10`

| i | h | popped (height) | left | width = i − left − 1 | area | best | stack after |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 2 | — | | | | 0 | [0] |
| 1 | 1 | 0 (2) | −1 | 1 | 2 | 2 | [1] |
| 2 | 5 | — | | | | 2 | [1, 2] |
| 3 | 6 | — | | | | 2 | [1, 2, 3] |
| 4 | 2 | 3 (6) | 2 | 1 | 6 | 6 | |
| | | 2 (5) | 1 | 2 | 10 | **10** | [1, 4] |
| 5 | 3 | — | | | | 10 | [1, 4, 5] |
| 6 | 0 (sentinel) | 5 (3) | 4 | 1 | 3 | 10 | |
| | | 4 (2) | 1 | 4 | 8 | 10 | |
| | | 1 (1) | −1 | 6 | 6 | 10 | [6] |

## 6. Edge cases & pitfalls

- **Forgetting the sentinel:** bars still on the stack at the end (e.g. TC 2 `[2, 4]`, which only ever increases) never get measured. The trailing `0` flushes them.
- **Width off by one:** the width is `i − left − 1`, with `left = -1` when the stack is empty after the pop.
- **Zero-height bars** pop everything taller and give area 0 themselves. That's correct.
- **All equal** (TC 3 `[1, 1, 1, 1]` → 4): see "equal heights" in §4.
- `heights + [0]` copies the list (O(n) memory). To avoid that, loop `i` over `range(n + 1)` and use `h = 0` when `i == n`.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `heights = [2,1,5,6,2,3]` | `10` |
| TC 2 | `heights = [2,4]` | `4` |
| TC 3 | `heights = [1,1,1,1]` | `4` |

## 8. Follow-up questions (from course doc)

- **Q: Return the maximum area using a monotonic stack in O(n).**
  **A:** See §3, Approach 4, and §4. Each bar is pushed and popped once, and its area is computed when it is popped.
- **Q: How does the same idea solve the maximal rectangle of 1s in a binary matrix?** ([LC 85](https://leetcode.com/problems/maximal-rectangle/))
  **A:** Treat each row as the **floor** of a histogram. For row `r`, `heights[c]` = the number of consecutive `1`s in column `c` ending at row `r` (add 1 for a `1`, reset to 0 for a `0`). Any all-1s rectangle has a bottom row, and in that row's histogram it is a rectangle under the bars. So run the histogram algorithm once per row and keep the best.
  **Time:** O(rows × cols) · **Space:** O(cols).

  Example: for the matrix below, the histogram for the third row is `[3, 1, 3, 2, 2]`, and its largest rectangle is `2 × 3 = 6` (columns 2–4, height 2).

  ```
  1 0 1 0 0
  1 0 1 1 1
  1 1 1 1 1
  1 0 0 1 0
  ```

<details>
<summary><b>Show the maximal rectangle solution</b></summary>

```python
class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        if not matrix:
            return 0
        heights = [0] * len(matrix[0])
        best = 0
        for row in matrix:
            for c, cell in enumerate(row):
                heights[c] = heights[c] + 1 if cell == '1' else 0
            best = max(best, self.largestRectangleArea(heights))
        return best

    def largestRectangleArea(self, heights: List[int]) -> int:
        stack = []
        best = 0
        for i, h in enumerate(heights + [0]):
            while stack and heights[stack[-1]] >= h:
                height = heights[stack.pop()]
                left = stack[-1] if stack else -1
                best = max(best, height * (i - left - 1))
            stack.append(i)
        return best
```

</details>

## 9. Threads

- **Q17** Next Greater Element II: the same stack with the comparison flipped.
- **Q2** Trapping Rain Water: bars again; its stack solution pops a bar when a taller wall arrives.
- **Q5** Sliding Window Maximum: a monotonic deque.

## 10. One-line revision

> Increasing stack of indices plus a 0 sentinel; when a bar is popped, the bar popping it is the right wall and the new top is the left wall: `area = h × (i − left − 1)`.
