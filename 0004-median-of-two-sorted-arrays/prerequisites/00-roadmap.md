# Roadmap → Q3 · 4. Median of Two Sorted Arrays (Hard)

**Goal:** given two sorted arrays of sizes `m` and `n`, return the median of all their values **without merging them**, in **O(log(min(m, n)))** time. Explain the partition logic, empty arrays, and how to adapt it to two machines that can only exchange small messages.

> 📚 **Before you start:** read [Primer 05 · Binary Search](../../course/primers/05-binary-search.md).

## Skills you need

- [ ] Classic binary search, and the loop invariant that makes it correct
- [ ] Binary search on a yes/no condition (find the first "true")
- [ ] Merging two sorted arrays with two pointers (the O(m + n) baseline)
- [ ] Finding the k-th smallest of two sorted arrays by dropping about `k/2` values per step

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Binary Search](01-lc0704-binary-search.md) | Classic binary search, loop invariants | [LC 704](https://leetcode.com/problems/binary-search/) |
| 02 | [First Bad Version](02-lc0278-first-bad-version.md) | Binary search on a true/false condition | [LC 278](https://leetcode.com/problems/first-bad-version/) |
| 03 | [Merge Sorted Array](03-lc0088-merge-sorted-array.md) | Merging two sorted arrays (the O(m + n) baseline) | [LC 88](https://leetcode.com/problems/merge-sorted-array/) |
| 04 | [K-th smallest of two sorted arrays](04-concept-kth-smallest-of-two-sorted-arrays.md) | Drop `k/2` values each step: O(log(m + n)) | — |
| ★ | [**Median of Two Sorted Arrays** → NOTES](../NOTES.md) | Binary search the cut in the shorter array: O(log(min(m, n))) | [LC 4](https://leetcode.com/problems/median-of-two-sorted-arrays/) |

## How the steps add up

- Step 01: keep the answer inside `[lo, hi]` and halve that range each step.
- Step 02: the thing being searched can be a yes/no question that flips from false to true once.
- Step 03: the simple answer. Merge (or just walk) to the middle in O(m + n).
- Step 04: the median is the k-th smallest value. Comparing the items at rank `k/2` in each array lets you drop about `k/2` values at once.
- **Target:** instead of dropping values, binary search **how many** values the left half takes from the shorter array. "Took too many from A?" is step 02's yes/no condition.

## Threads

- **Q19** Merge k Sorted Lists: merging sorted sequences (step 03) and divide & conquer.
- **Q10 / Q2** two pointers: step 03 and the O(m + n) walk use one pointer per array.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
