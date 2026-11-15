# 0645. Set Mismatch

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q8 | [645](https://leetcode.com/problems/set-mismatch/) | Easy | Arrays & Hashing | ✅ (submitted: Approach 4, sum / sum of squares) |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0645-set-mismatch.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Corrupted Record Repair:** a table should hold record IDs `1..n` exactly once. A bug duplicated one ID and lost another. Return `[duplicate, missing]` in O(n) time and O(1) extra space.

## 2. Pattern recognition

- **Pattern:** Arrays & Hashing, with in-place index marking
- **Signals in the statement:** values lie in `1..n` (so a value can be used as an index), and the doc asks for O(1) extra space.
- **Core idea in one line:** use each value as an index and flip that cell's sign. A cell that's already negative reveals the duplicate, and the cell left positive reveals the missing ID.

## 3. Approaches

### Approach 1: Sort and scan

After sorting, the duplicate sits next to itself and the missing number is a gap. **Time:** O(n log n) · **Space:** O(1) to O(n)

### Approach 2: Hash set + sum formula ([steps 01 + 02](./prerequisites/00-roadmap.md))

Find the duplicate with a set, then `missing = duplicate + n(n+1)/2 − sum(nums)`. **Time:** O(n) · **Space:** O(n)

### Approach 3: Index marking (optimal, modifies the input)

[Step 03](./prerequisites/03-lc0448-find-all-numbers-disappeared-in-an-array.md) plus a duplicate check. **Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def findErrorNums(self, nums: List[int]) -> List[int]:
        dup = missing = -1
        for x in nums:
            i = abs(x) - 1
            if nums[i] < 0:
                dup = abs(x)
            else:
                nums[i] = -nums[i]
        for i, v in enumerate(nums):
            if v > 0:
                missing = i + 1
        return [dup, missing]
```

</details>

### Approach 4: Math with sum and sum of squares (read-only input)

Let `D` be the duplicate and `M` the missing number.
- `S = sum(nums) − n(n+1)/2 = D − M`
- `Q = sum(x²) − n(n+1)(2n+1)/6 = D² − M² = (D − M)(D + M)`, so `D + M = Q / S`.
- Therefore `D = (S + Q/S) / 2` and `M = D − S`.

**Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show sum / sum-of-squares solution</b></summary>

```python
class Solution:
    def findErrorNums(self, nums: List[int]) -> List[int]:
        n = len(nums)
        s = sum(nums) - n * (n + 1) // 2                                # D - M
        q = sum(x * x for x in nums) - n * (n + 1) * (2 * n + 1) // 6   # D² - M²
        d_plus_m = q // s
        dup = (s + d_plus_m) // 2
        return [dup, dup - s]
```

</details>

### Approach 5: XOR (read-only, no overflow)

XOR all of `1..n` with all of `nums`. Everything cancels except `D ^ M`. Split all numbers by one set bit of `D ^ M` into two groups; XOR each group to recover `D` and `M`, then check which one appears in `nums`. **Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show XOR solution</b></summary>

```python
class Solution:
    def findErrorNums(self, nums: List[int]) -> List[int]:
        xor = 0
        for i, x in enumerate(nums, 1):
            xor ^= i ^ x                     # = D ^ M
        bit = xor & -xor                     # lowest bit where D and M differ
        a = b = 0
        for i, x in enumerate(nums, 1):
            for v in (i, x):
                if v & bit:
                    a ^= v
                else:
                    b ^= v
        return [a, b] if a in nums else [b, a]
```

</details>

## 4. Why it works (index marking)

- Every value `x` in `1..n` owns exactly one cell, `x − 1`. A negative cell means "x has been seen".
- The second time the duplicate shows up, its cell is **already negative**, so that's `D`.
- There are `n − 1` distinct values, so exactly `n − 1` cells get flipped. The one cell still positive belongs to `M`.

## 5. Dry run: `nums = [3, 1, 3]` → `[3, 2]`

| x | index | cell before | action | array after |
| --- | --- | --- | --- | --- |
| 3 | 2 | 3 | flip | `[3, 1, -3]` |
| 1 | 0 | 3 | flip | `[-3, 1, -3]` |
| -3 | 2 | -3 | already negative → **dup = 3** | `[-3, 1, -3]` |

Index 1 is still positive, so **missing = 2**.

## 6. Edge cases & pitfalls

- Always use `abs(x)`, because earlier flips may have made `x` negative.
- `[1, 1]` → `[1, 2]`: the missing value can be `n` itself.
- The sum of squares grows to about `n³/3` (≈ 3.3 × 10¹¹ for n = 10⁴), which overflows 32-bit integers in Java/C++. Use `long` there; Python is fine.
- Index marking **mutates the input**. Restore it with a final `abs` pass if needed.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [1, 2, 2, 4]` | `[2, 3]` |
| TC 2 | `nums = [3, 1, 3]` | `[3, 2]` |
| TC 3 | `nums = [1, 1]` | `[1, 2]` |

## 8. Follow-up questions (from course doc)

- **Q: Describe at least two O(1)-space approaches.**
  **A:** Index marking (Approach 3) and sum / sum-of-squares (Approach 4). XOR (Approach 5) is a third option with no overflow risk.
- **Q: When is modifying the input acceptable?**
  **A:** When the function owns the data or the contract allows it (e.g. a scratch copy in a batch job). It's **not** acceptable when the array is shared, read-only (memory-mapped, cached), or read by other threads at the same time. In those cases use the math or XOR approach, or restore the signs afterwards.

## 9. Threads

- **Q6** Longest Consecutive Sequence uses the same "hash set for O(1) membership" idea.

## 10. One-line revision

> Value x owns index x−1: flip its sign. Already negative means duplicate; still positive at the end means missing.

<!-- course-nav:start -->

---

### Course navigation

**Problem 1 of 30** · Arrays & Hashing (1 of 4)

[← Start Here](../START_HERE.md) · [Course index](../INDEX.md) · [Next: Q7 · Product of Array Except Self →](../0238-product-of-array-except-self/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
