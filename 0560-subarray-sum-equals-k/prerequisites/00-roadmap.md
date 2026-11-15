# Roadmap → Q1 · 560. Subarray Sum Equals K (Medium)

**Goal:** count the contiguous subarrays whose sum is exactly `k`. Values can be **negative**, so a sliding window doesn't work. Required: **O(n)**.

> 📚 **Before you start:** read [Primer 01 · Arrays and Hashing](../../course/primers/01-arrays-and-hashing.md).

## Skills you need

- [ ] "Look back for a complement" with a hash map
- [ ] Prefix sums: any subarray sum is the difference of two prefix sums
- [ ] Seeing that counting subarrays means counting pairs of prefix sums

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Two Sum](01-lc0001-two-sum.md) | Complement lookup in a hash map | [LC 1](https://leetcode.com/problems/two-sum/) |
| 02 | [Range Sum Query – Immutable](02-lc0303-range-sum-query-immutable.md) | `sum(i..j) = P[j+1] − P[i]` | [LC 303](https://leetcode.com/problems/range-sum-query-immutable/) |
| 03 | [Subarray sum as a prefix difference](03-concept-subarray-sum-as-prefix-difference.md) | O(n²) count over prefix pairs; why a sliding window fails | — |
| ★ | [**Subarray Sum Equals K** → NOTES](../NOTES.md) | Two Sum applied to prefix sums: O(n) | [LC 560](https://leetcode.com/problems/subarray-sum-equals-k/) |

## How the steps add up

- Step 01: "have I already seen `target − x`?" in O(1).
- Step 02: any subarray sum is `P[j] − P[i]`.
- Step 03: counting subarrays with sum `k` means counting pairs `i < j` with `P[i] = P[j] − k`. That's step 01's question asked about prefix sums.
- **Target:** keep a hash map of how many times each prefix sum has appeared.

## Threads

- **Q7** Product of Array Except Self: prefix (and suffix) accumulation.
- **Q10** 3Sum: builds on the same Two Sum complement idea.
- **Q4 / Q9** sliding window: works only when the window sum changes **in one direction** as it grows, which is why this problem can't use it.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
