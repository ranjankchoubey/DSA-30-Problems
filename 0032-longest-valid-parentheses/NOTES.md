# 0032. Longest Valid Parentheses

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q14 | [32](https://leetcode.com/problems/longest-valid-parentheses/) | Hard | Stack | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0032-longest-valid-parentheses.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Longest Valid Parentheses in a Compiler:** a code editor highlights the longest well-formed run of brackets in a line made only of `(` and `)`. Return the length of the longest valid substring. Give a stack solution, a DP solution and an O(1)-space two-pass counter solution, and compare them. `0 ≤ |s| ≤ 30,000`.

## 2. Pattern recognition

- **Pattern:** Stack (also solvable with 1-D DP or two counters)
- **Signals in the statement:** "well-formed", "longest **substring**" (contiguous), a single bracket type.
- **Core idea in one line:** the brackets that can never be matched split the string into valid pieces, and the answer is the longest piece.

## 3. Approaches

### Approach 1: Brute force

For each start `i`, extend `j` with a balance counter. Stop when the balance goes negative, and record `j − i + 1` whenever it is 0. **Time:** O(n²) · **Space:** O(1)

### Approach 2: Stack of indices with a base

The stack starts as `[-1]`. Its bottom is always the index **just before** the current valid run. `(` pushes its index. `)` pops; if the stack is now empty, this `)` is unmatched and becomes the new base, otherwise the run length is `i − stack[-1]`. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show stack solution</b></summary>

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        stack = [-1]                      # bottom = index just before the current valid run
        best = 0
        for i, ch in enumerate(s):
            if ch == '(':
                stack.append(i)
            else:
                stack.pop()
                if not stack:
                    stack.append(i)       # unmatched ')': the new base
                else:
                    best = max(best, i - stack[-1])
        return best
```

</details>

### Approach 3: DP on "the longest valid substring ending at i"

`dp[i]` = length of the longest valid substring that **ends at** `i` (0 if `s[i] == '('`). For `s[i] == ')'`, the run ending at `i − 1` has length `dp[i−1]`, so the `(` that would match `s[i]` sits at `j = i − dp[i−1] − 1`. If `s[j] == '('`, then `dp[i] = dp[i−1] + 2 + dp[j−1]`, where `dp[j−1]` glues on a valid run that ends right before `j`. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show DP solution</b></summary>

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        n = len(s)
        dp = [0] * n                      # dp[i] = longest valid substring ending at i
        for i in range(1, n):
            if s[i] == ')':
                j = i - dp[i - 1] - 1     # where the matching '(' must be
                if j >= 0 and s[j] == '(':
                    dp[i] = dp[i - 1] + 2 + (dp[j - 1] if j >= 1 else 0)
        return max(dp, default=0)
```

</details>

### Approach 4: Two-pass counter (optimal on space)

Left to right, count `open_` and `close`. When they are equal, a valid run of `2 × close` just ended. When `close > open_`, that `)` can never be matched (step 02), so reset both to 0. This misses runs where extra `(` never close (`"(()"`), so do a mirror-image pass right to left, resetting when `open_ > close`. **Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        best = 0
        open_ = close = 0
        for ch in s:                      # left to right
            if ch == '(':
                open_ += 1
            else:
                close += 1
            if open_ == close:
                best = max(best, 2 * close)
            elif close > open_:           # this ')' can never be matched
                open_ = close = 0
        open_ = close = 0
        for ch in reversed(s):            # right to left, rules mirrored
            if ch == '(':
                open_ += 1
            else:
                close += 1
            if open_ == close:
                best = max(best, 2 * open_)
            elif open_ > close:           # this '(' can never be matched
                open_ = close = 0
        return best
```

</details>

## 4. Why it works

- **Stack:** the bottom of the stack is always the last index that can't be part of the current run: `-1` at the start, then the latest unmatched `)`. Above it sit the indices of `(` not yet matched. After a `)` pops its partner, everything from `stack[-1] + 1` to `i` is balanced and never dips below zero, so it is valid, and it is the longest valid run ending at `i`.
- **DP:** a valid substring ending in `)` looks like `( inner )` followed by nothing, or preceded by another valid run. `inner` is the longest run ending at `i − 1`, which pins the partner at `j`, and `dp[j−1]` adds the run touching it on the left. Each `dp[i]` uses only earlier entries.
- **Two-pass counter:** a substring is valid exactly when its counts are equal and, reading left to right, `close` never exceeds `open_`. The left pass resets at every `)` that breaks the second rule, so it measures every valid run whose surrounding segment has no extra `(`. A run hidden by extra `(` (like `"(()"`) has no extra `)` when read from the right, so the mirror pass finds it.

## 5. Dry run: stack on TC 2, `s = ")()())"` → `4`

| i | ch | action | stack after | best |
| --- | --- | --- | --- | --- |
| — | — | start | [-1] | 0 |
| 0 | `)` | pop −1 → empty, push 0 as the new base | [0] | 0 |
| 1 | `(` | push 1 | [0, 1] | 0 |
| 2 | `)` | pop 1, run = 2 − 0 = 2 | [0] | 2 |
| 3 | `(` | push 3 | [0, 3] | 2 |
| 4 | `)` | pop 3, run = 4 − 0 = 4 | [0] | **4** |
| 5 | `)` | pop 0 → empty, push 5 as the new base | [5] | 4 |

And why the counter needs two passes, on TC 1, `s = "(()"` → `2`:

| Pass | after each char (open_, close) | best |
| --- | --- | --- |
| left → right | (1,0) (2,0) (2,1) | 0, never equal |
| right → left (reads `)`, `(`, `(`) | (0,1) (1,1) → equal, run 2 · then (2,1) → reset | **2** |

## 6. Edge cases & pitfalls

- **Empty string** (TC 3) → 0. The DP needs `max(dp, default=0)`.
- **Stack must start as `[-1]`**, otherwise a run starting at index 0 has no base to measure from.
- **Concatenated runs:** `"()(())"` → 6. The stack measures from the shared base; the DP needs the `dp[j−1]` term to glue `()` onto `(())`.
- **Counter, one pass only:** fails on `"(()"` (gives 0). Both passes are required.
- **All `(` or all `)`** → 0.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `s = "(()"` | `2` |
| TC 2 | `s = ")()())"` | `4` |
| TC 3 | `s = ""` | `0` |

## 8. Follow-up questions (from course doc)

- **Q: Give a stack solution, a DP solution and an O(1)-space two-pass counter solution, and compare them.**
  **A:** The three solutions are in §3. All are O(n) time.

  | | Stack | DP | Two-pass counter |
  | --- | --- | --- | --- |
  | Time | O(n), one pass | O(n), one pass | O(n), two passes |
  | Extra space | O(n) stack | O(n) array | **O(1)** |
  | Key state | index before the current run | longest run ending at `i` | `open_` / `close` counts |
  | Hard part | starting with `[-1]`, the new base on an unmatched `)` | `j = i − dp[i−1] − 1` and the `dp[j−1]` glue | remembering the second pass |
  | Extra output for free | where the runs break | the best run ending at **every** index | nothing beyond the length |

  In an interview, start with the stack (it follows straight from Valid Parentheses and is easiest to argue). Offer the counter when asked for O(1) space. The DP is useful when you need the run ending at each position, e.g. to highlight every valid run, not just the longest.

## 9. Threads

- **Q20** Min Stack: the bracket-matching stack (step 01) is taught there.
- **Q16** Basic Calculator III: a stack while scanning parentheses.
- **Q11 / Q12** 1-D DP: "best answer ending at `i`", the same state as Approach 3.

## 10. One-line revision

> Unmatched brackets split the string into valid runs: stack of indices with base −1 (measure `i − stack[-1]`), DP on the run ending at `i`, or two counter passes (reset on extra `)` left→right, extra `(` right→left).

<!-- course-nav:start -->

---

### Course navigation

**Problem 13 of 30** · Stack (4 of 5)

[← Previous: Q15 · Largest Rectangle in Histogram](../0084-largest-rectangle-in-histogram/NOTES.md) · [Course index](../INDEX.md) · [Next: Q16 · Basic Calculator III →](../0772-basic-calculator-iii/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
