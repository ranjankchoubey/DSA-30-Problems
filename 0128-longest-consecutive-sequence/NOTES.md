# 0128. Longest Consecutive Sequence

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q6 | [128](https://leetcode.com/problems/longest-consecutive-sequence/) | Medium | Arrays & Hashing | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0128-longest-consecutive-sequence.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Login Session Analyzer:** given an unsorted list of user IDs that logged in today, find the length of the longest run of consecutive IDs (a sign of bot-created accounts). It must run in O(n), so sorting is not allowed.

## 2. Pattern recognition

- **Pattern:** Arrays & Hashing, using a hash set
- **Signals in the statement:** "consecutive" but **unsorted**, O(n) required, and no sorting. That means you need O(1) "is x+1 present?" checks.
- **Core idea in one line:** only start counting from values whose `x − 1` is missing. Those are the starts of runs.

## 3. Approaches

### Approach 1: Sort, then count runs ([step 03](./prerequisites/03-concept-sort-then-count-runs.md))

**Time:** O(n log n) · **Space:** O(1) to O(n). Not allowed by the course constraints.

### Approach 2: Walk from every value ([step 04](./prerequisites/04-concept-naive-hash-set-walk.md))

**Time:** O(n²) worst case · **Space:** O(n)

### Approach 3: Walk only from the starts of runs (optimal)

**Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        s = set(nums)
        best = 0
        for x in s:
            if x - 1 not in s:           # x starts a run
                length = 1
                while x + length in s:
                    length += 1
                best = max(best, length)
        return best
```

</details>

## 4. Why it works (and why it's linear)

- Every run has exactly one start, the value with no `x − 1`. Walking from that start counts the whole run.
- Values in the middle of a run fail the `x − 1 not in s` check in O(1) and are skipped.
- So the `while` loop moves over each value **once in total** across all runs. The outer loop is n checks, so the total is **O(n)**.

## 5. Dry run: `[100, 4, 200, 1, 3, 2]` → 4

| x | x−1 in set? | action | best |
| --- | --- | --- | --- |
| 100 | 99 no | walk 100 → length 1 | 1 |
| 4 | 3 yes | skip | 1 |
| 200 | 199 no | walk 200 → length 1 | 1 |
| 1 | 0 no | walk 1,2,3,4 → length 4 | **4** |
| 3 | 2 yes | skip | 4 |
| 2 | 1 yes | skip | 4 |

(The order of values in a set may differ; the result doesn't.)

## 6. Edge cases & pitfalls

- **Loop over `s`, not `nums`.** If `nums` repeats a run-start many times (e.g. `[1, 1, 1, …, 2, 3, …, k]`), looping over `nums` re-walks the same run each time and goes back to O(n²).
- Empty input returns `0` (TC 3).
- Duplicates (TC 2 has two `0`s) are removed by the set.
- Negative values and very large values (±10⁹) are fine, since it's set membership and not indexing.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [100, 4, 200, 1, 3, 2]` | `4` (sequence 1,2,3,4) |
| TC 2 | `nums = [0,3,7,2,5,8,4,6,0,1]` | `9` |
| TC 3 | `nums = []` | `0` |

## 8. Follow-up questions (from course doc)

- **Q: Describe the hash-set approach.**
  **A:** Build a set, then for each run start (`x−1` not present) count upward while `x+k` is present. See §3 and §4.
- **Q: Why does starting only from run beginnings keep it linear?**
  **A:** Each value is walked over only as part of its own run, and each run is walked exactly once, from its start. The total walking is at most n steps, plus n O(1) start checks.
- **Q: What is its worst-case behaviour?**
  **A:** O(n) **expected** time, assuming O(1) average hash operations. With adversarial inputs that all land in the same hash slot, set operations slow to O(n) each and the total becomes O(n²). Memory is always O(n) for the set. If memory is tight, sorting in place (O(n log n), O(1) extra) is the fallback. Union-Find is another O(n·α(n)) option.

## 9. Threads

- **Q8** Set Mismatch: hash set membership.
- **Q1** Subarray Sum Equals K: a hash map turns "look back over everything" into O(1).

## 10. One-line revision

> Put everything in a set; only start counting at x when x−1 is missing; each value is walked once, so O(n).

<!-- course-nav:start -->

---

### Course navigation

**Problem 3 of 30** · Arrays & Hashing (3 of 4)

[← Previous: Q7 · Product of Array Except Self](../0238-product-of-array-except-self/NOTES.md) · [Course index](../INDEX.md) · [Next: Q1 · Subarray Sum Equals K →](../0560-subarray-sum-equals-k/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
