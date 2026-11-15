# Primer 05 · Binary Search

Read this **before** the Binary Search ladder (Q3), or when a step's key idea doesn't make sense yet.

## What binary search is

Binary search finds something in a **sorted** (or otherwise ordered) search space by checking the middle and throwing away the half that can't hold the answer. Each step halves the space, so `n` items need only about `log₂ n` steps: 1,000,000 items take about 20 checks.

Python's `bisect` module already does binary search on sorted lists:

```python
from bisect import bisect_left, bisect_right, insort

a = [1, 3, 3, 5]
bisect_left(a, 3)     # 1: first index where a[i] >= 3
bisect_right(a, 3)    # 3: first index where a[i] > 3
insort(a, 4)          # a == [1, 3, 3, 4, 5]
```

| Operation | Code | Time |
| --- | --- | --- |
| Find the first index with `a[i] >= x` | `bisect_left(a, x)` | O(log n) |
| Find the first index with `a[i] > x` | `bisect_right(a, x)` | O(log n) |
| Insert and keep sorted | `insort(a, x)` | O(n) (the shift dominates) |
| Linear search (what binary search replaces) | `x in a`, `a.index(x)` | O(n) |
| Sort first, if the data isn't sorted | `a.sort()` | O(n log n) |

In interviews you're usually expected to write the loop yourself, so learn the two templates below.

## Binary search template

Find `target` in a sorted list, or return `-1`.

```python
def binary_search(nums, target):
    lo, hi = 0, len(nums) - 1          # the answer, if any, is in nums[lo..hi]
    while lo <= hi:                    # the range is not empty
        mid = (lo + hi) // 2
        if nums[mid] == target:
            return mid
        if nums[mid] < target:
            lo = mid + 1               # target is right of mid
        else:
            hi = mid - 1               # target is left of mid
    return -1
```

**Loop invariant:** "if `target` is in the list, it is inside `nums[lo..hi]`." Every update keeps this true and makes the range strictly smaller (`mid ± 1`), so the loop always ends. When `lo > hi` the range is empty, so `target` isn't there.

## Binary search on a condition

Often there's no value to compare against. Instead there's a yes/no question that is **false, false, …, false, true, true, …, true** across the range, and you want the **first true**.

```python
def first_true(lo, hi, condition):
    """Smallest x in [lo, hi] with condition(x) True. Assumes condition(hi) is True."""
    while lo < hi:
        mid = (lo + hi) // 2
        if condition(mid):
            hi = mid          # mid could be the answer, so keep it
        else:
            lo = mid + 1      # mid is false, so the answer is to the right
    return lo
```

- The loop is `lo < hi` and ends when `lo == hi`, which is the answer.
- `hi = mid` (not `mid − 1`) because `mid` might be the first true.
- `mid` rounds down, so `lo = mid + 1` always makes progress and the loop can't get stuck.

Examples: First Bad Version (`is_bad(v)`), the smallest speed that finishes in time, the smallest capacity that ships everything, and Median of Two Sorted Arrays ("did I take too many items from the first array?").

## Working with two sorted arrays

| Question | Technique | Time |
| --- | --- | --- |
| Merge them | two pointers, always take the smaller head ([Primer 02](02-two-pointers.md#same-direction)) | O(m + n) |
| k-th smallest overall | compare the items at rank `k/2` in each array; drop the smaller block | O(log k) |
| Median | binary search a **cut** in the shorter array | O(log(min(m, n))) |

The k-th smallest trick: if `A[k/2 − 1] ≤ B[k/2 − 1]`, then only the `k/2 − 1` items before it in `A` and at most `k/2 − 1` items of `B` can come before `A[k/2 − 1]`. So it ranks `k − 1` or better, the first `k/2` items of `A` can't be the k-th smallest, and they can be dropped. Then look for the `(k − k/2)`-th smallest of what's left.

```python
def kth_smallest(A, B, k):
    """k is 1-based."""
    i = j = 0                                  # A[:i] and B[:j] are dropped
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
```

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "sorted array", "find x / insert position" | classic binary search or `bisect` |
| O(log n) or O(log(min(m, n))) required | binary search; throw away half each step |
| "first / smallest / minimum value such that …" | binary search on a condition (first true) |
| answer is a number in a range, and "is X enough?" is easy to check | binary search on the answer |
| two sorted arrays, "median" or "k-th" | k-th smallest by halves, or binary search the cut |

## Common pitfalls

- Mixing templates: `while lo <= hi` goes with `mid ± 1`; `while lo < hi` goes with `hi = mid`.
- `lo = mid` together with `mid = (lo + hi) // 2` can loop forever when `hi = lo + 1`.
- Binary searching data that isn't sorted, or a condition that flips more than once.
- Off-by-one in what `lo` / `hi` mean. Write the invariant down before coding.
- In Java or C++, `(lo + hi) / 2` can overflow. Use `lo + (hi − lo) / 2` there. Python ints don't overflow.

## Learn more

- NeetCode roadmap (Binary Search follows Stack): <https://neetcode.io/roadmap>
- Python docs: [bisect](https://docs.python.org/3/library/bisect.html)
- Visualise the same halving idea on a binary search tree: <https://visualgo.net/en/bst>
