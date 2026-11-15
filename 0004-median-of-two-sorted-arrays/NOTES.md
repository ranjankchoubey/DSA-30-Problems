# 0004. Median of Two Sorted Arrays

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q3 | [4](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Hard | Binary Search | ✅ (submitted O(m+n); resubmit the O(log) version below) |

> **Code:** [0004-median-of-two-sorted-arrays.py](./0004-median-of-two-sorted-arrays.py) (auto-synced by LeetHub) · **Problem statement:** [README.md](./README.md) (auto-synced by LeetHub)

## 1. Case scenario

**Merging Two Sorted Sensor Feeds:** two data centres each hold a sorted list of response times. Find the median of the combined data **without merging**, in **O(log(min(m, n)))**.

## 2. Pattern recognition

- **Pattern:** Binary Search on a *partition*, not on a value
- **Signals in the statement:** both inputs are sorted and a log-time bound is required, so you must discard half of the search space each step.
- **Core idea in one line:** cut both arrays so the left halves together hold `(m+n+1)//2` elements and every left element is ≤ every right element. The median sits at the cut.

## 3. Approaches

### Approach 1: Merge, then pick the middle

Merge like merge sort into a new array. **Time:** O(m+n) · **Space:** O(m+n)

### Approach 2: Two-pointer walk to the middle (the currently submitted solution)

Advance two pointers, always taking the smaller head, until you reach the middle element(s). See [0004-median-of-two-sorted-arrays.py](./0004-median-of-two-sorted-arrays.py). **Time:** O(m+n) · **Space:** O(1)

> ⚠️ This is accepted on LeetCode but does **not** meet the course's O(log(min(m, n))) requirement. Use it as the "better" step when teaching.

### Approach 3: Binary search the partition (optimal)

```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        A, B = nums1, nums2
        if len(A) > len(B):            # binary search the shorter array
            A, B = B, A
        m, n = len(A), len(B)
        half = (m + n + 1) // 2        # size of the combined left half
        lo, hi = 0, m
        while lo <= hi:
            i = (lo + hi) // 2         # elements taken from A
            j = half - i               # elements taken from B
            a_left  = A[i - 1] if i > 0 else float("-inf")
            a_right = A[i]     if i < m else float("inf")
            b_left  = B[j - 1] if j > 0 else float("-inf")
            b_right = B[j]     if j < n else float("inf")
            if a_left <= b_right and b_left <= a_right:
                if (m + n) % 2:
                    return float(max(a_left, b_left))
                return (max(a_left, b_left) + min(a_right, b_right)) / 2
            elif a_left > b_right:     # took too many from A
                hi = i - 1
            else:                      # took too few from A
                lo = i + 1
```

**Time:** O(log(min(m, n))) · **Space:** O(1)

## 4. Why it works

- Choose `i` items from `A` and `j = half - i` items from `B` for the left half. Then the size condition always holds.
- Both arrays are sorted, so `a_left ≤ a_right` and `b_left ≤ b_right` are automatic. The partition is valid only if **`a_left ≤ b_right` and `b_left ≤ a_right`**.
- If `a_left > b_right`, too many items came from `A`, so move left. Otherwise move right. That's monotonic, so binary search applies.
- Valid partition: the median is `max(left side)` for odd totals, or `(max(left) + min(right)) / 2` for even totals.
- Searching the **shorter** array keeps `j` in `[0, n]` and gives the `min(m, n)` bound.

## 5. Dry run: `nums1 = [1,3]`, `nums2 = [2]` → 2.0

After the swap, `A = [2]` and `B = [1,3]`, so `m = 1`, `n = 2`, `half = 2`.

| lo | hi | i | j | a_left | a_right | b_left | b_right | decision |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 1 | 0 | 2 | −∞ | 2 | 3 | +∞ | b_left 3 > a_right 2 → lo = 1 |
| 1 | 1 | 1 | 1 | 2 | +∞ | 1 | 3 | valid, odd → max(2, 1) = **2.0** |

## 6. Edge cases & pitfalls

- **One array empty** (TC 3): after the swap `A = []`, so `i = 0` and `j = half`. The ±∞ sentinels handle the missing sides.
- Always binary search the **shorter** array. Otherwise `j` can go negative or past `n`.
- Use `(m + n + 1) // 2` so the left half holds the extra element on odd totals.
- Return a `float` on odd totals too (`2.0`, not `2`).
- Duplicates across arrays work, because the conditions use `≤`.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `nums1 = [1, 3]`, `nums2 = [2]` | 2.0 |
| TC 2 | `nums1 = [1, 2]`, `nums2 = [3, 4]` | 2.5 |
| TC 3 | `nums1 = []`, `nums2 = [5]` | 5.0 |

## 8. Follow-up questions (from course doc)

- **Q: How do you handle empty arrays?**
  **A:** Swap so the empty one is `A`. Then the loop runs once with `i = 0` and the sentinels (`−∞`/`+∞`) remove the missing neighbours. The constraint `m + n ≥ 1` guarantees an answer.
- **Q: What if the data lives on two machines that can only exchange small messages?**
  **A:** Turn it into "find the k-th smallest" with k = (m+n)/2. Each round, machine 1 sends its element at rank k/2 and machine 2 compares it with its own element at rank k/2. The side with the smaller value discards its first k/2 elements, and k halves. That's O(log k) rounds with **one number per message**. Another option is to binary search on the *value* range: each machine replies "how many of my values are ≤ x" (one count), giving O(log(value range)) rounds.

## 9. One-line revision

> Binary search the cut in the shorter array so that left halves total (m+n+1)/2 and a_left ≤ b_right and b_left ≤ a_right. The median comes from the four border values.
