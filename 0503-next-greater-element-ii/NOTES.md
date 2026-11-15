# 0503. Next Greater Element II

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q17 | [503](https://leetcode.com/problems/next-greater-element-ii/) | Medium | Stack | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0503-next-greater-element-ii.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Circular Temperature Alerts:** sensors sit in a circle. For each sensor, find the next **higher** reading going clockwise, wrapping around, or `-1` if there is none. Required: O(n) with a monotonic stack, plus an explanation of why iterating the array twice handles the circle. `1 ≤ n ≤ 10,000`.

## 2. Pattern recognition

- **Pattern:** Stack, monotonic (decreasing) stack
- **Signals in the statement:** "next higher" for **every** element, "clockwise, wrapping around", O(n).
- **Core idea in one line:** run the next-greater stack pass over `i = 0 … 2n−1` reading `nums[i % n]`. The second lap lets elements near the end find answers near the start.

## 3. Approaches

### Approach 1: Brute force

For each `i`, check `(i + k) % n` for `k = 1 … n−1` and stop at the first bigger value. **Time:** O(n²) · **Space:** O(1) extra

### Approach 2: Doubled array + monotonic stack ([step 03](./prerequisites/03-concept-circular-array-iterate-2n.md))

Run the linear algorithm on `nums + nums` and keep the first `n` answers. **Time:** O(n) · **Space:** O(n), including a 2n-sized copy

### Approach 3: Two laps with `i % n` (optimal)

The same as Approach 2, but read `nums[i % n]` instead of copying, and push indices only in the first lap. **Time:** O(n) · **Space:** O(n) for the stack and the answer

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        n = len(nums)
        answer = [-1] * n
        stack = []                        # indices still waiting; values decreasing bottom → top
        for i in range(2 * n):
            x = nums[i % n]
            while stack and nums[stack[-1]] < x:
                answer[stack.pop()] = x
            if i < n:                     # the second lap only pops
                stack.append(i)
        return answer
```

</details>

## 4. Why it works

- **Invariant:** the stack holds the indices whose next greater value hasn't been found yet, and their values never increase from bottom to top.
- **The first bigger value is the one that pops `i`.** Everything above `i` on the stack is `≤ nums[i]`. So any value bigger than `nums[i]` pops those first and then pops `i`. A value that isn't bigger can't pop `i`. So `i` is popped by the **first** bigger value after it.
- **Two laps are enough:** index `i` must be compared with `i+1 … n−1` (first lap) and then `0 … i−1` (second lap, positions `n … n+i−1`). The latest of these is position `n + i − 1 < 2n`, so the loop visits every candidate, in clockwise order.
- **Leftovers:** an index still on the stack after both laps has been compared with every other element and nothing was bigger. It holds the maximum value, so `-1` is correct.
- **O(n):** each index is pushed once (first lap only) and popped at most once, over `2n` loop steps.

## 5. Dry run: `nums = [1, 2, 3, 4, 3]` → `[2, 3, 4, -1, 4]`

| i | i % n | x | popped → answer | stack after (indices) |
| --- | --- | --- | --- | --- |
| 0 | 0 | 1 | — | [0] |
| 1 | 1 | 2 | 0 → 2 | [1] |
| 2 | 2 | 3 | 1 → 3 | [2] |
| 3 | 3 | 4 | 2 → 4 | [3] |
| 4 | 4 | 3 | — | [3, 4] |
| 5 | 0 | 1 | — | [3, 4] (second lap: no push) |
| 6 | 1 | 2 | — | [3, 4] |
| 7 | 2 | 3 | — (3 is not > 3) | [3, 4] |
| 8 | 3 | 4 | 4 → 4 | [3] |
| 9 | 4 | 3 | — | [3] |

Index 3 (value 4, the maximum) stays on the stack and keeps `-1`. Index 4 needed the second lap to find the 4.

## 6. Edge cases & pitfalls

- **Equal values aren't "higher":** pop with strict `<`. `[1, 1, 1]` → `[-1, -1, -1]`.
- **Duplicates:** store **indices**, not values. A value-keyed map (step 02) breaks when values repeat.
- **Every copy of the maximum** gets `-1` (`[5, 1, 5]` → `[-1, 5, -1]`).
- `n = 1` → `[-1]`: the only other "element" is itself.
- Many solutions push in both laps. That gives the same answers only if they push `i % n`. Pushing `i` itself when `i ≥ n` makes `answer[stack.pop()]` index past the end. Pushing only while `i < n` avoids the question.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [1, 2, 1]` | `[2, -1, 2]` |
| TC 2 | `nums = [1, 2, 3, 4, 3]` | `[2, 3, 4, -1, 4]` |
| TC 3 | `nums = [5, 4, 3]` | `[-1, 5, 5]` |

## 8. Follow-up questions (from course doc)

- **Q: Solve it in O(n) with a monotonic stack.**
  **A:** See §3, Approach 3. The stack holds indices with non-increasing values, each index is pushed once and popped at most once, and the loop runs `2n` times, so the total is O(n).
- **Q: Why does iterating the array twice handle the circularity?**
  **A:** Going clockwise from `i`, the candidates are the `n − 1` items after it, wrapping round. Reading the array twice in a row lays the circle out flat: positions `i+1 … n+i−1` of the doubled sequence are exactly those candidates, in clockwise order. So the ordinary linear stack pass sees every candidate. The first lap pushes every index and answers what it can. The second lap gives the indices still waiting a chance to find a bigger value at the start of the array. A third lap would add nothing, because after two laps every waiting index has been compared with every other element. `i % n` gives the second lap without copying the array (§4).

## 9. Threads

- **Q15** Largest Rectangle in Histogram: the same stack, finding the next and previous **smaller** bars.
- **Q5** Sliding Window Maximum: the same pop rule in a deque, plus removing from the front.
- **Q2** Trapping Rain Water: has a monotonic-stack solution too.

## 10. One-line revision

> Decreasing stack of indices; loop `i` over `0..2n−1` reading `nums[i % n]` and push only in the first lap. Whoever pops you is your next greater.

<!-- course-nav:start -->

---

### Course navigation

**Problem 11 of 30** · Stack (2 of 5)

[← Previous: Q20 · Min Stack](../0155-min-stack/NOTES.md) · [Course index](../INDEX.md) · [Next: Q15 · Largest Rectangle in Histogram →](../0084-largest-rectangle-in-histogram/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
