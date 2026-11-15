# 0238. Product of Array Except Self

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q7 | [238](https://leetcode.com/problems/product-of-array-except-self/) | Medium | Arrays & Hashing | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0238-product-of-array-except-self.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Revenue Contribution Without Division:** for each product line, finance needs the product of every *other* line's revenue multiplier. Division isn't allowed (some multipliers are 0), and extra space must be O(1) apart from the output.

## 2. Pattern recognition

- **Pattern:** Arrays & Hashing, prefix/suffix
- **Signals in the statement:** "all **other** elements", "no division", O(n). Together these point to computing left and right parts separately.
- **Core idea in one line:** `answer[i] = (product of everything left of i) × (product of everything right of i)`.

## 3. Approaches

### Approach 1: Brute force

For every `i`, multiply all `j ≠ i`. **Time:** O(n²) · **Space:** O(1)

### Approach 2: Total product ÷ nums[i] (not allowed here)

Breaks on zeros: with one zero only that index is non-zero, and with two or more zeros everything is 0. Handling it needs zero-counting, and the doc forbids division anyway. **Time:** O(n) · **Space:** O(1)

### Approach 3: Prefix and suffix arrays ([step 03](./prerequisites/03-concept-prefix-and-suffix-product-arrays.md))

**Time:** O(n) · **Space:** O(n)

### Approach 4: Output array + running suffix (optimal)

Pass 1 (left to right) writes the prefix products into `out`. Pass 2 (right to left) multiplies in a running `suffix`. **Time:** O(n) · **Space:** O(1) extra

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        out = [1] * n
        prefix = 1
        for i in range(n):
            out[i] = prefix              # product of nums[0 .. i-1]
            prefix *= nums[i]
        suffix = 1
        for i in range(n - 1, -1, -1):
            out[i] *= suffix             # times product of nums[i+1 .. n-1]
            suffix *= nums[i]
        return out
```

</details>

## 4. Why it works

- After pass 1, `out[i]` holds the product of `nums[0..i−1]`, because `prefix` is updated **after** it's written (an exclusive prefix).
- In pass 2, `suffix` holds the product of `nums[i+1..n−1]` when it's multiplied in, also exclusive.
- So `out[i] = left × right`, which is exactly "everything except `i`". No division is used, so zeros need no special handling.

## 5. Dry run: `[1, 2, 3, 4]` → `[24, 12, 8, 6]`

| Pass | i | value written | running var after |
| --- | --- | --- | --- |
| left to right | 0 | out[0] = 1 | prefix = 1 |
| left to right | 1 | out[1] = 1 | prefix = 2 |
| left to right | 2 | out[2] = 2 | prefix = 6 |
| left to right | 3 | out[3] = 6 | prefix = 24 |
| right to left | 3 | out[3] = 6 × 1 = **6** | suffix = 4 |
| right to left | 2 | out[2] = 2 × 4 = **8** | suffix = 12 |
| right to left | 1 | out[1] = 1 × 12 = **12** | suffix = 24 |
| right to left | 0 | out[0] = 1 × 24 = **24** | suffix = 24 |

## 6. Edge cases & pitfalls

- Write `out[i]` **before** updating `prefix`/`suffix`. Doing it the other way includes `nums[i]` itself.
- **One zero** at index `z`: every other position gets 0, and position `z` gets the product of the rest (TC 2 → `[0,0,9,0,0]`).
- **Two or more zeros:** every position gets 0 (TC 3).
- Negative numbers need no special handling.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [1,2,3,4]` | `[24,12,8,6]` |
| TC 2 | `nums = [-1,1,0,-3,3]` | `[0,0,9,0,0]` |
| TC 3 | `nums = [0,0,2]` | `[0,0,0]` |

## 8. Follow-up questions (from course doc)

- **Q: Explain the prefix and suffix passes.**
  **A:** See §4. The first pass stores "left of i" in the output, the second multiplies in "right of i" using one variable. Both are exclusive of `i`.
- **Q: How do zeros change the output?**
  **A:** With 0 zeros every position is non-zero. With exactly 1 zero, only the zero's position is non-zero. With 2 or more zeros, every position is 0. The prefix/suffix method handles all three automatically.

## 9. Threads

- **Q2** Trapping Rain Water: the same left pass and right pass, using **max** instead of product.
- **Q1** Subarray Sum Equals K: prefix **sums** combined with a hash map.

## 10. One-line revision

> Exclusive prefix products into `out`, then multiply in an exclusive running suffix from the right. Two passes, no division.

<!-- course-nav:start -->

---

### Course navigation

**Problem 2 of 30** · Arrays & Hashing (2 of 4)

[← Previous: Q8 · Set Mismatch](../0645-set-mismatch/NOTES.md) · [Course index](../INDEX.md) · [Next: Q6 · Longest Consecutive Sequence →](../0128-longest-consecutive-sequence/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
