# Roadmap → Q15 · 84. Largest Rectangle in Histogram (Hard)

**Goal:** bars of width 1 have the given heights. Return the area of the largest rectangle that fits entirely inside them, in **O(n)** with a monotonic stack. Then use the same idea for the largest rectangle of 1s in a binary matrix.

> 📚 **Before you start:** read [Primer 04 · Stack](../../course/primers/04-stack.md).

## Skills you need

- [ ] Use a monotonic stack to find "the first item after me that is bigger / smaller"
- [ ] See that the best rectangle is limited by its **shortest** bar
- [ ] Find each bar's previous and next smaller bar, and turn them into a width
- [ ] Compute the area at the moment a bar is popped

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Daily Temperatures](01-lc0739-daily-temperatures.md) ↩ recap | Monotonic stack | [LC 739](https://leetcode.com/problems/daily-temperatures/) |
| 02 | [Expand each bar](02-concept-expand-each-bar.md) | Brute force O(n²): each bar as the shortest bar | — |
| 03 | [Previous and next smaller element](03-concept-previous-and-next-smaller-element.md) | Both boundaries in two O(n) passes; `area = h × (next − prev − 1)` | — |
| ★ | [**Largest Rectangle in Histogram** → NOTES](../NOTES.md) | One pass: popping a bar reveals both of its boundaries | [LC 84](https://leetcode.com/problems/largest-rectangle-in-histogram/) |

## How the steps add up

- Step 01: a monotonic stack finds the next bigger item for every index in O(n).
- Step 02: try every bar as the shortest bar of the rectangle and stretch it left and right. Correct, but O(n²).
- Step 03: the stretching stops at the previous and next **smaller** bar. Two stack passes find these for every bar.
- **Target:** when the increasing stack pops a bar, the bar doing the popping is its next smaller, and the bar underneath is its previous smaller. So compute the area right there, in one pass.

## Threads

- **Q17** Next Greater Element II: the same stack with the comparison flipped.
- **Q2** Trapping Rain Water: another bar-chart problem; it has a monotonic-stack solution too.
- **Q5** Sliding Window Maximum: a monotonic deque.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
