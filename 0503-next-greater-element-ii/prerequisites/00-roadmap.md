# Roadmap → Q17 · 503. Next Greater Element II (Medium)

**Goal:** for each element of a **circular** array, return the first larger value met when moving forward and wrapping around, or `-1` if there is none. O(n) with a monotonic stack.

> 📚 **Before you start:** read [Primer 04 · Stack](../../course/primers/04-stack.md).

## Skills you need

- [ ] Find the next greater element for every index with a monotonic stack
- [ ] Answer many lookups from one stack pass (hash map, or an answer array by index)
- [ ] Walk a circle with `i % n`, and see why two laps are enough

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Daily Temperatures](01-lc0739-daily-temperatures.md) ↩ recap | Monotonic stack | [LC 739](https://leetcode.com/problems/daily-temperatures/) |
| 02 | [Next Greater Element I](02-lc0496-next-greater-element-i.md) | Next greater element + hash map | [LC 496](https://leetcode.com/problems/next-greater-element-i/) |
| 03 | [Circular array: iterate 2n](03-concept-circular-array-iterate-2n.md) | `i % n` and unrolling the circle into two laps | — |
| ★ | [**Next Greater Element II** → NOTES](../NOTES.md) | Two laps with one stack and no copied array: O(n) | [LC 503](https://leetcode.com/problems/next-greater-element-ii/) |

## How the steps add up

- Step 01: a decreasing stack of waiting indices; the value that pops an index is its next greater.
- Step 02: the same pass, storing the **value** found instead of the distance.
- Step 03: a circle read twice in a row (`nums + nums`) contains every clockwise path, so a linear algorithm works on it.
- **Target:** read `nums[i % n]` for `i` in `0..2n−1` instead of copying, and push indices only in the first lap.

## Threads

- **Q15** Largest Rectangle in Histogram: the same stack, finding next and previous **smaller** bars.
- **Q5** Sliding Window Maximum: the same pop rule, in a deque.
- **Q2** Trapping Rain Water: has a monotonic-stack solution too.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
