# 0152. Maximum Product Subarray

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q11 | [152](https://leetcode.com/problems/maximum-product-subarray/) | Medium | 1-D DP | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0152-maximum-product-subarray.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Best Profit Window:** a trading desk models daily multiplicative returns, which can be negative or zero. Find the contiguous period with the largest product of returns. Explain why you must track both the running maximum and the running minimum, and what happens when a zero appears.

## 2. Pattern recognition

- **Pattern:** 1-D DP (Kadane's idea for products)
- **Signals in the statement:** "contiguous", "largest product", values that can be **negative** or **zero**, and `n ≤ 20,000` (so O(n²) is borderline; aim for O(n)).
- **Core idea in one line:** for each `i`, keep the largest **and** smallest product of a subarray ending at `i`, because multiplying by a negative swaps them.

## 3. Approaches

### Approach 1: Brute force

Fix a start, extend to every end with a running product. **Time:** O(n²) · **Space:** O(1)

<details>
<summary><b>Show brute force</b></summary>

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        best = nums[0]
        for i in range(len(nums)):
            product = 1
            for j in range(i, len(nums)):
                product *= nums[j]
                best = max(best, product)
        return best
```

</details>

### Approach 2: Prefix and suffix products

Inside a stretch with no zeros, the best subarray either starts at the stretch's left end or finishes at its right end (with an odd number of negatives, drop everything up to the first one or from the last one). So scan from both sides with a running product, and restart it after a zero. **Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show prefix and suffix version</b></summary>

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        n = len(nums)
        best = nums[0]
        prefix = suffix = 0
        for i in range(n):
            prefix = (prefix or 1) * nums[i]          # "or 1" restarts after a 0
            suffix = (suffix or 1) * nums[n - 1 - i]
            best = max(best, prefix, suffix)
        return best
```

</details>

### Approach 3: Track max and min ending at `i` (optimal)

Kadane ([step 02](./prerequisites/02-lc0053-maximum-subarray.md)) with two states ([step 03](./prerequisites/03-lc1567-maximum-length-of-subarray-with-positive-product.md)). For each `x`, the best and worst products ending here are among `x`, `x × cur_max`, and `x × cur_min`. **Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        best = cur_max = cur_min = nums[0]
        for x in nums[1:]:
            candidates = (x, x * cur_max, x * cur_min)
            cur_max, cur_min = max(candidates), min(candidates)
            best = max(best, cur_max)
        return best
```

</details>

## 4. Why it works

- **State:** `cur_max` / `cur_min` = the largest / smallest product of a subarray **ending at** the current index.
- A subarray ending at `i` is either `[x]` alone or a subarray ending at `i − 1` extended by `x`. Extending multiplies by `x`.
- If `x ≥ 0`, multiplying keeps the order, so the largest extension comes from `cur_max`. If `x < 0`, it **reverses** the order, so the largest comes from `cur_min` (the most negative). Taking the max and min of all three candidates covers both cases without an `if`.
- `best` is the largest `cur_max` ever seen, because every subarray ends somewhere.

## 5. Dry run: TC 3 `nums = [-2, 3, -4]` → 24

| x | candidates (x, x·max, x·min) | cur_max | cur_min | best |
| --- | --- | --- | --- | --- |
| −2 (start) | — | −2 | −2 | −2 |
| 3 | (3, −6, −6) | 3 | −6 | 3 |
| −4 | (−4, −12, **24**) | 24 | −12 | **24** |

At `x = 3` the best so far is 3, but `cur_min = −6` (`[-2, 3]`) is kept. At `x = −4` that "worst" value becomes the best: `−6 × −4 = 24`, the whole array.

## 6. Edge cases & pitfalls

- **Tracking only the max fails:** on TC 3 it would forget −6 and return 3.
- **Update both at once.** `cur_max` and `cur_min` must be computed from the **old** values; a tuple assignment (or a temporary) does that.
- **Zero:** all three candidates are 0, so `cur_max = cur_min = 0`. At the next element, the candidate `x` alone wins over `0`, so the subarray **restarts**. That's how a zero splits the array (TC 2).
- **Single element / all negative:** start `best` at `nums[0]`, not 0 or 1. `[-3]` must return −3.
- In fixed-width languages, intermediate products can overflow even when the answer fits; Python integers don't overflow.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [2, 3, -2, 4]` | `6` (`[2, 3]`) |
| TC 2 | `nums = [-2, 0, -1]` | `0` (any period containing the zero gives 0; `[-2]` and `[-1]` alone are negative) |
| TC 3 | `nums = [-2, 3, -4]` | `24` (the whole array) |

## 8. Follow-up questions (from course doc)

- **Q: Why must you track both the running maximum and the running minimum?**
  **A:** Multiplying by a negative number flips the order: the largest product becomes the most negative, and the most negative becomes the largest. So today's smallest product may become tomorrow's largest. In TC 3, `[-2, 3]` has the **smallest** product (−6) at index 1, and one more negative day turns it into the answer, 24. Keeping only the maximum would have thrown it away (§5).
- **Q: What happens when a zero appears?**
  **A:** Any period that includes a zero day has product 0, so a zero **splits** the array into independent parts. In the code, `x = 0` makes every candidate 0, so `cur_max = cur_min = 0`; on the next day the candidate `x` alone wins, which restarts the period. The 0 itself is still a valid answer when everything else is negative (TC 2 → 0). In Approach 2, the same reset is `prefix or 1`.

## 9. Threads

- **Q7** Product of Array Except Self: products, zeros, and prefix/suffix passes (Approach 2 here).
- **Q12** Longest Palindromic Substring: the other 1-D DP problem.
- **Q1** Subarray Sum Equals K: another contiguous-subarray problem where negative values change the technique.

## 10. One-line revision

> Keep max and min product ending at i: candidates (x, x·max, x·min); a negative swaps them, a zero resets both; answer = largest max seen.

<!-- course-nav:start -->

---

### Course navigation

**Problem 30 of 30** · 1-D DP (2 of 2)

[← Previous: Q12 · Longest Palindromic Substring](../0005-longest-palindromic-substring/NOTES.md) · [Course index](../INDEX.md) · 🎉 **End of the course!**

> 🧭 **What next?** Continue with the patterns this course doesn't cover, in NeetCode roadmap order: Tries, Backtracking, Intervals, Greedy, 2-D DP, Bit Manipulation, Math & Geometry (<https://neetcode.io/roadmap>).

<!-- course-nav:end -->
