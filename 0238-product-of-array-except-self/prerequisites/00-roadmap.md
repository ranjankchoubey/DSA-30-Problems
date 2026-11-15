# Roadmap → Q7 · 238. Product of Array Except Self (Medium)

**Goal:** for every index `i`, return the product of all **other** elements. No division allowed (some values are zero), O(n) time, O(1) extra space apart from the output.

## Skills you need

- [ ] Build a running (prefix) total in one pass
- [ ] Split "everything except i" into "everything left of i" plus "everything right of i"
- [ ] Combine a left-to-right pass with a right-to-left pass

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Running Sum of 1d Array](01-lc1480-running-sum-of-1d-array.md) | Prefix accumulation | [LC 1480](https://leetcode.com/problems/running-sum-of-1d-array/) |
| 02 | [Find Pivot Index](02-lc0724-find-pivot-index.md) | Sum left of i vs sum right of i | [LC 724](https://leetcode.com/problems/find-pivot-index/) |
| 03 | [Prefix and suffix product arrays](03-concept-prefix-and-suffix-product-arrays.md) | Swap + for ×; two arrays, O(n) space | — |
| ★ | [**Product of Array Except Self** → NOTES](../NOTES.md) | Fold both arrays into the output: O(1) extra space | [LC 238](https://leetcode.com/problems/product-of-array-except-self/) |

## How the steps add up

- Step 01: a prefix value summarises everything **up to** `i`.
- Step 02: "everything except `i`" is the **left part** plus the **right part**.
- Step 03: the same split with products and two helper arrays.
- **Target:** keep the left products in the output array and replace the right array with one running variable.

## Threads

- **Q2** Trapping Rain Water uses the same left-pass / right-pass idea, with prefix and suffix **max** instead of product.
- **Q1** Subarray Sum Equals K builds on prefix sums.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
