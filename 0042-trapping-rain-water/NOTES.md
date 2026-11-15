# 0042. Trapping Rain Water

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q2 | [42](https://leetcode.com/problems/trapping-rain-water/) | Hard | Two Pointers | ✅ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** [0042-trapping-rain-water.py](./0042-trapping-rain-water.py) (auto-synced by LeetHub) · **Problem statement:** [README.md](./README.md) (auto-synced by LeetHub)

## 1. Case scenario

**Flood Water Storage Planner:** a street's elevation profile is an array of bar heights (width 1). Compute the total rain water trapped between the bars.

## 2. Pattern recognition

- **Pattern:** Two Pointers (alternatives: prefix/suffix max arrays, monotonic stack)
- **Signals in the statement:** the water at a position depends on the tallest bar on its **left** and on its **right**. The doc also requires O(n) time and O(1) extra space.
- **Core idea in one line:** `water[i] = min(maxLeft[i], maxRight[i]) - height[i]`. Each approach is a different way to get `maxLeft` and `maxRight` cheaply.

## 3. Approaches

### Approach 1: Brute force ([step 03](./prerequisites/03-concept-water-at-index.md))

For every `i`, scan left and right to find both maximums. **Time:** O(n²) · **Space:** O(1)

### Approach 2: Prefix and suffix max arrays ([step 04](./prerequisites/04-concept-prefix-suffix-max-arrays.md))

Precompute `L[i] = max(height[0..i])` and `R[i] = max(height[i..n-1])`, then sum `min(L[i], R[i]) - height[i]`. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show prefix/suffix max solution</b></summary>

```python
def trap(height):
    n = len(height)
    if n == 0:
        return 0
    L, R = [0] * n, [0] * n
    L[0] = height[0]
    for i in range(1, n):
        L[i] = max(L[i - 1], height[i])
    R[-1] = height[-1]
    for i in range(n - 2, -1, -1):
        R[i] = max(R[i + 1], height[i])
    return sum(min(L[i], R[i]) - height[i] for i in range(n))
```

</details>

### Approach 3: Monotonic stack (fills water layer by layer horizontally)

Keep a stack of indices with decreasing heights. When a taller bar arrives, pop the "bottom". The water above it is bounded by the new top of the stack (left wall) and the current bar (right wall). **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show monotonic stack solution</b></summary>

```python
def trap(height):
    stack, total = [], 0
    for i, h in enumerate(height):
        while stack and height[stack[-1]] < h:
            bottom = stack.pop()
            if not stack:
                break
            left = stack[-1]
            total += (min(height[left], h) - height[bottom]) * (i - left - 1)
        stack.append(i)
    return total
```

</details>

### Approach 4: Two pointers (optimal, this is the submitted solution)

Two pointers move inward from both ends with a running `leftMax` and `rightMax`. Process the side whose max is smaller: its water level is already known (§4). **Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def trap(self, height: List[int]) -> int:
        start, end = 0, len(height) - 1
        leftMax = rightMax = totalWater = 0
        while start < end:
            leftMax = max(leftMax, height[start])
            rightMax = max(rightMax, height[end])
            if leftMax < rightMax:
                totalWater += leftMax - height[start]
                start += 1
            else:
                totalWater += rightMax - height[end]
                end -= 1
        return totalWater
```

</details>

## 4. Why it works (proof the doc asks for)

**Claim:** if `leftMax < rightMax`, the water at `start` is exactly `leftMax - height[start]`.

- The water at `start` is `min(trueLeftMax, trueRightMax) - height[start]`.
- `leftMax` **is** the true left max of `start`, because we have seen every bar to its left.
- `rightMax` is the max of only a *part* of the bars to its right, so `trueRightMax ≥ rightMax > leftMax`.
- So `min(trueLeftMax, trueRightMax) = leftMax`. The unseen bars in the middle can only raise the right side and never change the answer.

The symmetric argument handles the `else` branch. So moving the pointer on the smaller side is always safe: that side's water level is already fully determined.

## 5. Dry run: `height = [4,2,0,3,2,5]` → 9

| start | end | leftMax | rightMax | branch | water added | total |
| --- | --- | --- | --- | --- | --- | --- |
| 0 | 5 | 4 | 5 | left | 4 − 4 = 0 | 0 |
| 1 | 5 | 4 | 5 | left | 4 − 2 = 2 | 2 |
| 2 | 5 | 4 | 5 | left | 4 − 0 = 4 | 6 |
| 3 | 5 | 4 | 5 | left | 4 − 3 = 1 | 7 |
| 4 | 5 | 4 | 5 | left | 4 − 2 = 2 | 9 |
| 5 | 5 | | | stop | | **9** |

## 6. Edge cases & pitfalls

- `n < 3`: no water can be trapped, and the loop handles it naturally.
- Strictly increasing or strictly decreasing heights give 0 (TC 3).
- Update `leftMax`/`rightMax` **before** adding water, so the added water is never negative.
- On equal maxima either branch is correct. The code uses `else`, so `<` vs `<=` doesn't matter.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `height = [0,1,0,2,1,0,1,3,2,1,2,1]` | 6 |
| TC 2 | `height = [4,2,0,3,2,5]` | 9 |
| TC 3 | `height = [1,2,3]` | 0 |

## 8. Follow-up questions (from course doc)

- **Q: Prove why moving the pointer with the smaller height is always safe.**
  **A:** See §4. If `leftMax < rightMax`, the true right max of `start` is at least `rightMax`, so the lower wall for `start` is `leftMax`, which is already final. Its water `leftMax − height[start]` is decided, and the bars still unseen can't change it. The `else` case is the mirror image.
- **Q: How do you extend this to a 2D grid ([407. Trapping Rain Water II](https://leetcode.com/problems/trapping-rain-water-ii/))?**
  **A:** The two-pointer idea generalises to "always process the lowest wall on the boundary". Push all border cells into a **min-heap** and mark them visited. Repeatedly pop the lowest cell (height `h`). For each unvisited neighbour, add `max(0, h - neighbour)` water and push the neighbour with height `max(h, neighbour)`. The current boundary minimum is the limiting wall, just like the smaller pointer in 1D. **Time:** O(mn log(mn)) · **Space:** O(mn).

## 9. Threads

- **Q7** Product of Array Except Self: the same left pass / right pass as Approach 2, with products instead of maximums.
- **Q1** Subarray Sum Equals K: prefix precomputation, with sums.
- **Q10** 3Sum: opposite-end pointers; each move discards the side that can't do better.
- **Q5 / Q15 / Q17** monotonic stack/deque: the same structure as Approach 3.
- **Q19 / Q29** heap: the 2D follow-up always processes the lowest boundary cell with a min-heap.

## 10. One-line revision

> Water at i = min(maxLeft, maxRight) − h[i]. Move the pointer on the smaller max side, because that side's water level is already decided.

<!-- course-nav:start -->

---

### Course navigation

**Problem 6 of 30** · Two Pointers (2 of 2)

[← Previous: Q10 · 3Sum](../0015-3sum/NOTES.md) · [Course index](../INDEX.md) · [Next: Q9 · Longest Substring with At Most K Distinct Characters →](../0340-longest-substring-with-at-most-k-distinct-characters/prerequisites/00-roadmap.md)

> 🧭 **Next pattern: Sliding Window.** Read [03 · Sliding Window primer](../course/primers/03-sliding-window.md) before starting Q9.

<!-- course-nav:end -->
