# Roadmap → Q2 · 42. Trapping Rain Water (Hard)

**Goal:** given bar heights (each bar 1 wide), return the total rain water trapped between them. Required: **O(n)** time and **O(1)** extra space with two pointers, plus a proof that moving the pointer on the smaller side is always safe.

> 📚 **Before you start:** read [Primer 02 · Two Pointers](../../course/primers/02-two-pointers.md).

## Skills you need

- [ ] Splitting a question about index `i` into "everything left of `i`" and "everything right of `i`"
- [ ] Moving two pointers inward from both ends, always moving the side that can't do better
- [ ] The water formula for one bar: `min(maxLeft, maxRight) − h[i]`
- [ ] Precomputing prefix and suffix maximums in O(n)

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Find Pivot Index](01-lc0724-find-pivot-index.md) ↩ recap | Left-side vs right-side aggregates | [LC 724](https://leetcode.com/problems/find-pivot-index/) |
| 02 | [Container With Most Water](02-lc0011-container-with-most-water.md) | Pointers at both ends; move the shorter side | [LC 11](https://leetcode.com/problems/container-with-most-water/) |
| 03 | [Water at one index](03-concept-water-at-index.md) | `min(maxL, maxR) − h[i]`, brute force O(n²) | — |
| 04 | [Prefix and suffix max arrays](04-concept-prefix-suffix-max-arrays.md) | O(n) time, O(n) space | — |
| ★ | [**Trapping Rain Water** → NOTES](../NOTES.md) | Two pointers replace both arrays: O(1) space | [LC 42](https://leetcode.com/problems/trapping-rain-water/) |

## How the steps add up

- Step 01: an answer at `i` can depend on a **left** aggregate and a **right** aggregate.
- Step 02: two pointers from the ends; the **smaller** side is the limit, so it's the one to move.
- Step 03: the water above bar `i` is set by the tallest bar on each side: `min(maxL, maxR) − h[i]`.
- Step 04: compute every `maxL` and `maxR` once, with one pass from each side.
- **Target:** keep only `leftMax` and `rightMax` as two pointers move inward. The side with the smaller max already knows its water level, so process that side. The arrays disappear.

## Threads

- **Q7** Product of Array Except Self: the same left pass / right pass, with products instead of maximums.
- **Q10** 3Sum: opposite-end pointers on a sorted array.
- **Q5 / Q15 / Q17** monotonic stack/deque: the stack approach in the NOTES fills water layer by layer.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
