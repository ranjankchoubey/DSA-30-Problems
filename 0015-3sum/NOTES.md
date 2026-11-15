# 0015. 3Sum

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q10 | [15](https://leetcode.com/problems/3sum/) | Medium | Two Pointers | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0015-3sum.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Budget Triplets:** a procurement system has a list of line-item adjustments (positive and negative). Find every **unique** set of three items whose adjustments add up to exactly 0. Order inside a triplet doesn't matter, and no triplet may appear twice. Required: O(n²) with sorting and two pointers, skipping duplicates at each level.

## 2. Pattern recognition

- **Pattern:** Two Pointers (sort, then opposite ends)
- **Signals in the statement:** "triplets that sum to 0" (Two Sum with one more value), "unique triplets" (sort so that copies sit together), O(n²) (one loop around an O(n) pair search).
- **Core idea in one line:** sort; for each distinct `nums[i]`, find every unique pair to its right that sums to `−nums[i]` with two pointers.

## 3. Approaches

### Approach 1: Brute force over all triples

Try every `i < j < k` and store each zero-sum triple **sorted** in a set to remove repeats. **Time:** O(n³) · **Space:** O(number of triplets)

<details>
<summary><b>Show brute force</b></summary>

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        found = set()
        for i in range(n):
            for j in range(i + 1, n):
                for k in range(j + 1, n):
                    if nums[i] + nums[j] + nums[k] == 0:
                        found.add(tuple(sorted((nums[i], nums[j], nums[k]))))
        return [list(t) for t in found]
```

</details>

### Approach 2: Fix one value + hash-set Two Sum ([step 01](./prerequisites/01-lc0001-two-sum.md))

Sort, then for each `i` run Two Sum with a hash set on the part to its right, target `−nums[i]`. Repeats are removed with a set of tuples, not by skipping. **Time:** O(n²) · **Space:** O(n)

<details>
<summary><b>Show hash-set version</b></summary>

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        found = set()
        for i in range(len(nums)):
            seen = set()
            for j in range(i + 1, len(nums)):
                need = -nums[i] - nums[j]
                if need in seen:
                    found.add((nums[i], need, nums[j]))   # already in sorted order
                seen.add(nums[j])
        return [list(t) for t in found]
```

</details>

### Approach 3: Sort + two pointers, skip duplicates at both levels (optimal)

Sort. For each `i`, run [step 04](./prerequisites/04-concept-all-unique-pairs-two-sum-ii.md)'s all-unique-pairs pass on `nums[i+1:]` with target `−nums[i]`. Skip repeated `nums[i]` in the outer loop and repeated `nums[l]` after each match. **Time:** O(n²) · **Space:** O(1) extra apart from the output (Python's sort itself can use up to O(n))

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        n = len(nums)
        res = []
        for i in range(n - 2):
            if nums[i] > 0:
                break                          # everything from here on is positive
            if i > 0 and nums[i] == nums[i - 1]:
                continue                       # outer dedupe: this first value was already tried
            l, r = i + 1, n - 1
            while l < r:
                s = nums[i] + nums[l] + nums[r]
                if s < 0:
                    l += 1
                elif s > 0:
                    r -= 1
                else:
                    res.append([nums[i], nums[l], nums[r]])
                    l += 1
                    r -= 1
                    while l < r and nums[l] == nums[l - 1]:
                        l += 1                 # inner dedupe: skip copies of the value just used
        return res
```

</details>

## 4. Why it works

- **Every triplet is found.** After sorting, write a triplet as `a ≤ b ≤ c` at indices `i < l < r`. When the outer loop reaches the first index holding `a`, the two-pointer pass over `nums[i+1:]` finds every pair summing to `−a`. Each move throws away a value that can't be in any remaining pair (the argument from [step 02](./prerequisites/02-lc0167-two-sum-ii-input-array-is-sorted.md)).
- **No triplet is found twice.**
  - Outer level: each distinct `a` is tried **once**, at its first copy. Comparing with the **previous** item keeps that first copy, so `[-1,-1,2]` (which needs two `−1`s) is still found.
  - Inner level: for a fixed `a`, after recording `(b, c)`, `l` skips every other copy of `b`. Once `a` and `b` are fixed, `c = −a − b` is fixed too, so that pair can't appear again.
- **Early break:** if `nums[i] > 0`, all three values are positive and can't sum to 0.
- **O(n²):** the sort is O(n log n). The outer loop runs n times and each pass moves `l` and `r` inward at most n times in total.

## 5. Dry run: TC 1 `nums = [-1,0,1,2,-1,-4]` → `[[-1,-1,2],[-1,0,1]]`

After sorting: `[-4,-1,-1,0,1,2]` (indices 0–5).

| i | nums[i] | l | r | sum | action |
| --- | --- | --- | --- | --- | --- |
| 0 | −4 | 1 | 5 | −4 − 1 + 2 = −3 | too small → l = 2 |
| 0 | −4 | 2 | 5 | −4 − 1 + 2 = −3 | too small → l = 3 |
| 0 | −4 | 3 | 5 | −4 + 0 + 2 = −2 | too small → l = 4 |
| 0 | −4 | 4 | 5 | −4 + 1 + 2 = −1 | too small → l = 5, stop |
| 1 | −1 | 2 | 5 | −1 − 1 + 2 = 0 | record **`[-1,-1,2]`**; l = 3, r = 4 (`nums[3] ≠ nums[2]`, no skip) |
| 1 | −1 | 3 | 4 | −1 + 0 + 1 = 0 | record **`[-1,0,1]`**; l = 4, r = 3, stop |
| 2 | −1 | | | | same as `nums[1]` → skip (outer dedupe) |
| 3 | 0 | 4 | 5 | 0 + 1 + 2 = 3 | too big → r = 4, stop |

The loop ends at `i = n − 3 = 3`. Result: `[[-1,-1,2],[-1,0,1]]`.

## 6. Edge cases & pitfalls

- **Sort first.** Both the pointer moves and the "same as previous" checks depend on sorted order.
- Skip outer duplicates with `nums[i] == nums[i − 1]`, **not** `nums[i] == nums[i + 1]`. The second form drops `[-1,-1,2]` in TC 1.
- Keep `l < r` inside the inner skip loop, or it can run past `r`.
- **All zeros** (TC 3 `[0,0,0]`): `i = 0` records `[0,0,0]` once, then the loop ends. With more zeros, the inner skip and outer skip stop repeats.
- **No answer** (TC 2 `[0,1,1]`): return `[]`, not `None`.
- LeetCode accepts the triplets in any order. The course table lists them sorted, which is the order this code produces.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [-1,0,1,2,-1,-4]` | `[[-1,-1,2],[-1,0,1]]` |
| TC 2 | `nums = [0,1,1]` | `[]` |
| TC 3 | `nums = [0,0,0]` | `[[0,0,0]]` |

## 8. Follow-up questions (from course doc)

- **Q: Give an O(n²) solution using sorting and two pointers. Why is it O(n²)?**
  **A:** Approach 3. Sorting is O(n log n). For each of the n values of `i`, the two pointers only move toward each other, so each pass is O(n). Total O(n log n + n²) = **O(n²)**. A hash-set version (Approach 2) is also O(n²) but uses O(n) extra space and needs a set of tuples to remove repeats.
- **Q: Show clearly how you skip duplicates at each level.**
  **A:**
  - **Level 1 (first value):** `if i > 0 and nums[i] == nums[i − 1]: continue`. Each distinct first value is tried once, at its first copy. In TC 1, `i = 2` (the second `−1`) is skipped.
  - **Level 2 (second value):** after recording a triplet, move `l` and `r` inward, then `while l < r and nums[l] == nums[l − 1]: l += 1`.
  - **Level 3 (third value):** no separate skip. The first two values fix the third, and a leftover copy of the old `nums[r]` now gives a sum that's too big, so the normal `r -= 1` steps past it.

## 9. Threads

- **Q1** Subarray Sum Equals K: the same Two Sum complement idea, with a hash map of prefix sums.
- **Q2** Trapping Rain Water: opposite-end pointers again; each move discards the side that can't do better.

## 10. One-line revision

> Sort; for each distinct nums[i] (compare with the previous one), two pointers on the right find pairs summing to −nums[i]; after a match move both and skip copies of nums[l].

<!-- course-nav:start -->

---

### Course navigation

**Problem 5 of 30** · Two Pointers (1 of 2)

[← Previous: Q1 · Subarray Sum Equals K](../0560-subarray-sum-equals-k/NOTES.md) · [Course index](../INDEX.md) · [Next: Q2 · Trapping Rain Water →](../0042-trapping-rain-water/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
