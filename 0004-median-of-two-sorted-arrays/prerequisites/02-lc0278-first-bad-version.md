# Step 02 · 278. First Bad Version (Easy)

[← Previous: Binary Search](01-lc0704-binary-search.md) · [Roadmap](00-roadmap.md) · [Next: Merge Sorted Array →](03-lc0088-merge-sorted-array.md)

**Practise:** [LeetCode 278](https://leetcode.com/problems/first-bad-version/)
**Skill it adds:** binary search on a **true/false condition**: find the first `true`.

## Problem in brief

Versions are numbered `1..n`. One version is bad, and every version after it is bad too. You can call `isBadVersion(v)`, which returns `True` or `False`. Find the first bad version using as few calls as possible.

## Key idea

The answers to `isBadVersion(1), …, isBadVersion(n)` look like `F F F T T T`. That's sorted (false before true), so binary search for the **first `T`**:

- If `mid` is bad, the first bad one is `mid` **or earlier**: `hi = mid` (keep `mid`).
- If `mid` is good, the first bad one is **after** `mid`: `lo = mid + 1`.
- Stop when `lo == hi`. That single version is the answer.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [binary search on a condition](../../course/primers/05-binary-search.md#binary-search-on-a-condition)
3. [LeetCode Editorial](https://leetcode.com/problems/first-bad-version/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+first+bad+version)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
# The isBadVersion API is already defined for you.
# def isBadVersion(version: int) -> bool:

class Solution:
    def firstBadVersion(self, n: int) -> int:
        lo, hi = 1, n
        while lo < hi:
            mid = (lo + hi) // 2
            if isBadVersion(mid):
                hi = mid          # mid might be the first bad one, so keep it
            else:
                lo = mid + 1      # mid is good, so the first bad one is to the right
        return lo
```

</details>

**Time:** O(log n) calls · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `n = 5`, first bad = `4` | `4` |
| `n = 1`, first bad = `1` | `1` |
| `n = 10`, first bad = `1` | `1` |

## Pitfall

`while lo < hi` goes with `hi = mid`. Since `mid` rounds down, `mid < hi`, so the range always shrinks. Using `lo = mid` instead of `lo = mid + 1` can loop forever when `hi = lo + 1`.

## What changes in the next step

The median target also binary searches a yes/no question that flips once. First, though, step 03 solves the simpler problem of combining two sorted arrays in O(m + n), the baseline we'll beat.
