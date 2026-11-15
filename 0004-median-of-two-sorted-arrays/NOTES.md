# 0004. Median of Two Sorted Arrays

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q3 | [4](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Hard | Binary Search | ✅ (submitted O(m+n); resubmit the O(log) version below) |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** [0004-median-of-two-sorted-arrays.py](./0004-median-of-two-sorted-arrays.py) (auto-synced by LeetHub) · **Problem statement:** [README.md](./README.md) (auto-synced by LeetHub)

## 1. Case scenario

**Merging Two Sorted Sensor Feeds:** two data centres each hold a sorted list of response times. Find the median of the combined data **without merging**, in **O(log(min(m, n)))**.

## 2. Pattern recognition

- **Pattern:** Binary Search on a *partition*, not on a value
- **Signals in the statement:** both inputs are sorted and a log-time bound is required, so you must discard half of the search space each step.
- **Core idea in one line:** cut both arrays so the left halves together hold `(m+n+1)//2` elements and every left element is ≤ every right element. The median sits at the cut.

## 3. Approaches

### Approach 1: Merge, then pick the middle ([step 03](./prerequisites/03-lc0088-merge-sorted-array.md))

Merge like merge sort into a new array. **Time:** O(m+n) · **Space:** O(m+n)

<details>
<summary><b>Show merge solution</b></summary>

```python
def median_by_merge(nums1, nums2):
    merged, i, j = [], 0, 0
    while i < len(nums1) and j < len(nums2):
        if nums1[i] <= nums2[j]:
            merged.append(nums1[i])
            i += 1
        else:
            merged.append(nums2[j])
            j += 1
    merged += nums1[i:] + nums2[j:]
    t = len(merged)
    if t % 2:
        return float(merged[t // 2])
    return (merged[t // 2 - 1] + merged[t // 2]) / 2
```

</details>

### Approach 2: Two-pointer walk to the middle (the currently submitted solution)

Advance two pointers, always taking the smaller head, until you reach the middle element(s). See [0004-median-of-two-sorted-arrays.py](./0004-median-of-two-sorted-arrays.py). **Time:** O(m+n) · **Space:** O(1)

> ⚠️ This is accepted on LeetCode but does **not** meet the course's O(log(min(m, n))) requirement. Use it as the "better" step when teaching. On odd totals it returns an `int` (e.g. `2`), which LeetCode accepts as `2.0`.

<details>
<summary><b>Show two-pointer walk</b> (the submitted code)</summary>

```python
class Solution:
    def findMedianSortedArrays(
        self, nums1: List[int], nums2: List[int]
    ) -> float:
        m, n = len(nums1), len(nums2)
        p1, p2 = 0, 0

        # Get the smaller value between nums1[p1] and nums2[p2].
        def get_min():
            nonlocal p1, p2
            if p1 < m and p2 < n:
                if nums1[p1] < nums2[p2]:
                    ans = nums1[p1]
                    p1 += 1
                else:
                    ans = nums2[p2]
                    p2 += 1
            elif p2 == n:
                ans = nums1[p1]
                p1 += 1
            else:
                ans = nums2[p2]
                p2 += 1
            return ans

        if (m + n) % 2 == 0:
            for _ in range((m + n) // 2 - 1):
                _ = get_min()
            return (get_min() + get_min()) / 2
        else:
            for _ in range((m + n) // 2):
                _ = get_min()
            return get_min()
```

</details>

### Approach 3: K-th smallest, drop k/2 values per step ([step 04](./prerequisites/04-concept-kth-smallest-of-two-sorted-arrays.md))

The median is the k-th smallest value (or the average of two). Compare the items at rank `k/2` in each array and drop the block with the smaller one. **Time:** O(log(m+n)) · **Space:** O(1)

<details>
<summary><b>Show k-th smallest solution</b></summary>

```python
def kth_smallest(A, B, k):
    i = j = 0
    while True:
        if i == len(A):
            return B[j + k - 1]
        if j == len(B):
            return A[i + k - 1]
        if k == 1:
            return min(A[i], B[j])
        ni = min(i + k // 2, len(A))
        nj = min(j + k // 2, len(B))
        if A[ni - 1] <= B[nj - 1]:
            k -= ni - i
            i = ni
        else:
            k -= nj - j
            j = nj


def median_via_kth(A, B):
    total = len(A) + len(B)
    if total % 2:
        return float(kth_smallest(A, B, total // 2 + 1))
    return (kth_smallest(A, B, total // 2) + kth_smallest(A, B, total // 2 + 1)) / 2
```

</details>

### Approach 4: Binary search the partition (optimal)

Binary search `i`, the number of values the combined left half takes from the **shorter** array; the other array supplies `j = half − i`. Stop when every left value is ≤ every right value. **Time:** O(log(min(m, n))) · **Space:** O(1)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

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

</details>

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

- **Q: Explain the partition logic.**
  **A:** Cut `A` after `i` items and `B` after `j = (m+n+1)//2 − i` items, so the two left parts always hold half the values (one extra on odd totals). The cut is correct when `a_left ≤ b_right` and `b_left ≤ a_right`, because then every left value is ≤ every right value. If `a_left > b_right` the cut in `A` is too far right; otherwise it's too far left. That yes/no answer flips once as `i` grows, so binary search on `i` over `[0, m]` finds the cut. See §4 and the dry run in §5.
- **Q: How do you handle empty arrays?**
  **A:** Swap so the empty one is `A`. Then the loop runs once with `i = 0` and the sentinels (`−∞`/`+∞`) remove the missing neighbours. The constraint `m + n ≥ 1` guarantees an answer.
- **Q: What if the data lives on two machines that can only exchange small messages?**
  **A:** Turn it into "find the k-th smallest" with k = (m+n)/2. Each round, machine 1 sends its element at rank k/2 and machine 2 compares it with its own element at rank k/2. The side with the smaller value discards its first k/2 elements, and k halves. That's O(log k) rounds with **one number per message** (this is Approach 3 / [step 04](./prerequisites/04-concept-kth-smallest-of-two-sorted-arrays.md), with each array on its own machine). Another option is to binary search on the *value* range: each machine replies "how many of my values are ≤ x" (one count), giving O(log(value range)) rounds.

## 9. Threads

- **Q19** Merge k Sorted Lists: merging sorted sequences (Approach 1) and divide & conquer.
- **Q10 / Q2** two pointers: Approach 2 walks one pointer per array.

## 10. One-line revision

> Binary search the cut in the shorter array so that left halves total (m+n+1)/2 and a_left ≤ b_right and b_left ≤ a_right. The median comes from the four border values.
