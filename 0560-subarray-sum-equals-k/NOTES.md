# 0560. Subarray Sum Equals K

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q1 | [560](https://leetcode.com/problems/subarray-sum-equals-k/) | Medium | Arrays & Hashing | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0560-subarray-sum-equals-k.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Fraud Pattern Detector:** a stream of balance changes (credits positive, debits negative). Count the contiguous segments whose net sum is exactly `k`, a known money-laundering pattern. Values can be negative, so a sliding window won't work. Required: O(n).

## 2. Pattern recognition

- **Pattern:** Arrays & Hashing, prefix sum + hash map
- **Signals in the statement:** "contiguous", "sum equals k", **negative numbers allowed**, O(n). Together these point to prefix sums plus a hash map, not a sliding window.
- **Core idea in one line:** `sum(i+1..j) = P[j] − P[i] = k ⟺ P[i] = P[j] − k`, so count earlier prefix sums equal to `P[j] − k`.

## 3. Approaches

### Approach 1: Brute force over all subarrays

Two loops with a running sum. **Time:** O(n²) · **Space:** O(1)

### Approach 2: Prefix-sum pairs ([step 03](./prerequisites/03-concept-subarray-sum-as-prefix-difference.md))

**Time:** O(n²) · **Space:** O(n)

### Approach 3: Prefix sum + hash map of counts (optimal)

**Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = 0
        prefix = 0
        seen = {0: 1}                    # the empty prefix
        for x in nums:
            prefix += x
            count += seen.get(prefix - k, 0)
            seen[prefix] = seen.get(prefix, 0) + 1
        return count
```

</details>

## 4. Why it works

- Each subarray ending at `j` corresponds to an earlier prefix `P[i]` with `P[i] = P[j] − k`.
- `seen` stores how many times each prefix sum has occurred **before** `j`, so `seen[P[j] − k]` is exactly the number of valid starts for subarrays ending at `j`.
- `{0: 1}` stands for the empty prefix, so subarrays starting at index 0 are counted.
- Negative numbers don't matter, because nothing depends on the sum changing in one direction (unlike a sliding window).

## 5. Dry run: `nums = [1, 2, 3, -3, 3], k = 3` → 5

| x | prefix | look up prefix − k | found | count | seen after |
| --- | --- | --- | --- | --- | --- |
| 1 | 1 | −2 | 0 | 0 | {0:1, 1:1} |
| 2 | 3 | 0 | 1 | 1 | {0:1, 1:1, 3:1} |
| 3 | 6 | 3 | 1 | 2 | {…, 6:1} |
| −3 | 3 | 0 | 1 | 3 | {…, 3:2} |
| 3 | 6 | 3 | 2 | **5** | {…, 6:2} |

The 5 subarrays are `[1,2]`, `[3]`, `[1,2,3,-3]`, `[3,-3,3]`, and the final `[3]`.

## 6. Edge cases & pitfalls

- **Look up before inserting** the current prefix. Otherwise, with `k = 0`, each position counts itself as an empty subarray.
- Forgetting `seen = {0: 1}` misses every subarray that starts at index 0.
- `k = 0` with zeros (TC 3 `[0,0,0]` → 6): every pair of equal prefix sums counts, giving 1 + 2 + 3.
- In other languages, prefix sums can reach 2×10⁸ here, which fits in 32 bits, but use 64-bit integers in general.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums = [1, 1, 1], k = 2` | `2` |
| TC 2 | `nums = [1, 2, 3, -3, 3], k = 3` | `5` |
| TC 3 | `nums = [0, 0, 0], k = 0` | `6` |

## 8. Follow-up questions (from course doc)

- **Q: Why do prefix sums with a hash map solve it?**
  **A:** A subarray sum is the difference of two prefix sums, so "sum = k" becomes "an earlier prefix equals current − k". The hash map answers "how many earlier prefixes have that value?" in O(1), which brings O(n²) down to O(n). See §4.
- **Q: What changes if you must also return the start and end indices of every segment?**
  **A:** Store a **list of indices** for each prefix value instead of a count (use `{0: [-1]}` for the empty prefix). At position `j`, every stored index `i` in `seen[prefix − k]` gives the segment `(i + 1, j)`.
  Time becomes **O(n + number of segments)**, which can be **O(n²)** (e.g. all zeros with `k = 0` has n(n+1)/2 segments), and space grows to match. That cost is unavoidable when the output itself is that large.

<details>
<summary><b>Show the version that returns indices</b></summary>

```python
def subarray_sum_segments(nums, k):
    seen = {0: [-1]}
    prefix = 0
    segments = []
    for j, x in enumerate(nums):
        prefix += x
        for i in seen.get(prefix - k, []):
            segments.append((i + 1, j))
        seen.setdefault(prefix, []).append(j)
    return segments
```

</details>

## 9. Threads

- **Q7** Product of Array Except Self: prefix accumulation.
- **Q10** 3Sum: the Two Sum complement idea again.
- **Q4 / Q9** sliding window: compare when a window works (sum changes in one direction) with when you need prefix sums (negative values).

## 10. One-line revision

> Count earlier prefix sums equal to current − k with a hash map that starts as {0: 1}; look up before inserting.

<!-- course-nav:start -->

---

### Course navigation

**Problem 4 of 30** · Arrays & Hashing (4 of 4)

[← Previous: Q6 · Longest Consecutive Sequence](../0128-longest-consecutive-sequence/NOTES.md) · [Course index](../INDEX.md) · [Next: Q10 · 3Sum →](../0015-3sum/prerequisites/00-roadmap.md)

> 🧭 **Next pattern: Two Pointers.** Read [02 · Two Pointers primer](../course/primers/02-two-pointers.md) before starting Q10.

<!-- course-nav:end -->
