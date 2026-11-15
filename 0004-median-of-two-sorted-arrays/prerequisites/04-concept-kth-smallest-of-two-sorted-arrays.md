# Step 04 · Concept: k-th smallest of two sorted arrays

[← Previous: Merge Sorted Array](03-lc0088-merge-sorted-array.md) · [Roadmap](00-roadmap.md) · [Target: Median of Two Sorted Arrays →](../NOTES.md)

> 📚 **New to this?** Primer: [working with two sorted arrays](../../course/primers/05-binary-search.md#working-with-two-sorted-arrays)

**Skill it adds:** throwing away about `k/2` values per step, so the k-th smallest of two sorted arrays takes O(log k) steps instead of k.

## Idea

The median is the k-th smallest value of both arrays together, with `k = (m + n) // 2 + 1` (odd total) or the average of the `k`-th and `(k+1)`-th smallest with `k = (m + n) // 2` (even total). So solve "k-th smallest" fast.

Take up to `k // 2` values from the front of each array and compare the **last** one taken from each:

- If `A`'s is smaller or equal, only the rest of `A`'s block (fewer than `k/2` values) and part of `B`'s block (fewer than `k/2` values) can come before it. So it ranks **`k − 1` or better**, and so does everything earlier in its block. None of them can be the k-th smallest. Drop that block from `A` and reduce `k` by its size.
- Otherwise drop `B`'s block the same way.

Stop when `k == 1` (take the smaller front value) or one array runs out (index straight into the other).

## Code

```python
def kth_smallest(A, B, k):
    """k is 1-based: k = 1 means the smallest value."""
    i = j = 0                                  # A[:i] and B[:j] have been dropped
    while True:
        if i == len(A):
            return B[j + k - 1]
        if j == len(B):
            return A[i + k - 1]
        if k == 1:
            return min(A[i], B[j])
        ni = min(i + k // 2, len(A))           # end of A's block
        nj = min(j + k // 2, len(B))           # end of B's block
        if A[ni - 1] <= B[nj - 1]:
            k -= ni - i                        # drop A[i:ni]
            i = ni
        else:
            k -= nj - j                        # drop B[j:nj]
            j = nj


def median_via_kth(A, B):
    total = len(A) + len(B)
    if total % 2:
        return float(kth_smallest(A, B, total // 2 + 1))
    return (kth_smallest(A, B, total // 2) + kth_smallest(A, B, total // 2 + 1)) / 2
```

**Time:** O(log k) = O(log(m + n)) · **Space:** O(1)

## Walk-through: `A = [1,3,5,7,9]`, `B = [2,4,6,8]`, `k = 5` → 5

Nine values in total, so the median is the 5th smallest.

| i | j | k | A's block | B's block | compare last values | drop |
| --- | --- | --- | --- | --- | --- | --- |
| 0 | 0 | 5 | `[1,3]` | `[2,4]` | 3 ≤ 4 | `[1,3]` from A, k = 3 |
| 2 | 0 | 3 | `[5]` | `[2]` | 5 > 2 | `[2]` from B, k = 2 |
| 2 | 1 | 2 | `[5]` | `[4]` | 5 > 4 | `[4]` from B, k = 1 |
| 2 | 2 | 1 | | | k = 1 → min(5, 6) | answer **5** |

## Test cases (`median_via_kth`)

| Input | Expected |
| --- | --- |
| `A = [1,3], B = [2]` | `2.0` |
| `A = [1,2], B = [3,4]` | `2.5` |
| `A = [], B = [5]` | `5.0` |

## What changes in the target

This is O(log(m + n)) and needs two searches for an even total. The target asks for O(log(min(m, n))). Instead of dropping blocks, it binary searches **one number**: how many values the left half of the median takes from the shorter array, `i`. Then `j = half − i` values come from the other one. The check "is `A[i − 1] > B[j]`?" is step 02's yes/no question: took too many from A.

The same block-dropping also answers the **two machines** follow-up: each round, each machine sends only one value (its item at rank `k/2`).

→ [NOTES](../NOTES.md)
