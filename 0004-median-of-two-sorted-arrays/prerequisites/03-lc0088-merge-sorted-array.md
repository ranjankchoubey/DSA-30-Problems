# Step 03 · 88. Merge Sorted Array (Easy)

[← Previous: First Bad Version](02-lc0278-first-bad-version.md) · [Roadmap](00-roadmap.md) · [Next: K-th smallest of two sorted arrays →](04-concept-kth-smallest-of-two-sorted-arrays.md)

**Practise:** [LeetCode 88](https://leetcode.com/problems/merge-sorted-array/)
**Skill it adds:** merging two sorted arrays with **one pointer per array**, the O(m + n) baseline for the median.

## Problem in brief

`nums1` holds `m` sorted values followed by `n` zeros (empty slots). `nums2` holds `n` sorted values. Merge `nums2` into `nums1` **in place** so that all `m + n` slots are sorted. Return nothing.

## Key idea

Fill `nums1` **from the back**. The largest remaining value is either `nums1[i]` or `nums2[j]` (the last unused item of each). Write it at slot `k` and move that pointer left. Writing from the back never overwrites a `nums1` value that hasn't been used yet, because the empty slots are at the end.

Stop when `nums2` is used up. Whatever is left of `nums1` is already in place.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [working with two sorted arrays](../../course/primers/05-binary-search.md#working-with-two-sorted-arrays)
3. [LeetCode Editorial](https://leetcode.com/problems/merge-sorted-array/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+merge+sorted+array)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        i, j, k = m - 1, n - 1, m + n - 1
        while j >= 0:
            if i >= 0 and nums1[i] > nums2[j]:
                nums1[k] = nums1[i]
                i -= 1
            else:
                nums1[k] = nums2[j]
                j -= 1
            k -= 1
```

</details>

**Time:** O(m + n) · **Space:** O(1)

## Test cases

| Input | Expected `nums1` |
| --- | --- |
| `nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3` | `[1,2,2,3,5,6]` |
| `nums1 = [1], m = 1, nums2 = [], n = 0` | `[1]` |
| `nums1 = [0], m = 0, nums2 = [1], n = 1` | `[1]` |

## What changes in the next step

For a median you don't need the whole merge, only the middle one or two values. Walking two pointers forward from the front to position `(m + n) // 2` gives an O(m + n) time, O(1) space answer (this is the version first submitted for Q3). To reach log time, step 04 drops **many** values per step instead of one.
