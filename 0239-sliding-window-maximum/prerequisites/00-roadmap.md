# Roadmap → Q5 · 239. Sliding Window Maximum (Hard)

**Goal:** given per-second request counts `nums` and a window size `k`, return the **maximum of every window** of `k` consecutive values, in **O(n)**. Be ready to explain the monotonic deque invariant, why each element is pushed and popped at most once, and what changes if the window size varies.

> 📚 **Before you start:** read [Primer 03 · Sliding Window](../../course/primers/03-sliding-window.md).

## Skills you need

- [ ] Slide a fixed-size window and know which element leaves at each step
- [ ] Know the O(nk) and O(n log n) baselines, and why a max can't be "subtracted" out
- [ ] Use a monotonic stack to throw away values that can never matter again
- [ ] Pop from both ends of a deque: smaller values from the back, expired ones from the front

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Maximum Average Subarray I](01-lc0643-maximum-average-subarray-i.md) ↩ recap | Fixed-size window | [LC 643](https://leetcode.com/problems/maximum-average-subarray-i/) |
| 02 | [Brute force and heap](02-concept-brute-force-and-heap.md) | O(nk), then a heap with lazy deletion, O(n log n) | — |
| 03 | [Daily Temperatures](03-lc0739-daily-temperatures.md) | Monotonic stack (first full lesson) | [LC 739](https://leetcode.com/problems/daily-temperatures/) |
| 04 | [Monotonic deque](04-concept-monotonic-deque.md) | Pop from the back (smaller values) and the front (out of the window) | — |
| ★ | [**Sliding Window Maximum** → NOTES](../NOTES.md) | Store indices so one deque does both jobs: O(n) | [LC 239](https://leetcode.com/problems/sliding-window-maximum/) |

## How the steps add up

- Step 01: a fixed window slides by adding `nums[i]` and removing `nums[i − k]`. That works for sums, but not for a max.
- Step 02: the straightforward fixes cost O(nk) (rescan) or O(n log n) (heap), and the heap keeps values that can never win again.
- Step 03: a monotonic stack throws such values away the moment a bigger one arrives. Each element is pushed and popped once. (It also has its own primer: [Primer 04 · Stack](../../course/primers/04-stack.md#monotonic-stack).)
- Step 04: a deque does the same from the back **and** lets old values leave from the front.
- **Target:** keep **indices** in the deque, so the front tells you when it has left the window.

## Threads

- **Q17** Next Greater Element II and **Q15** Largest Rectangle in Histogram: both start from step 03's monotonic stack.
- **Q9** Longest Substring with At Most K Distinct and **Q4** Minimum Window Substring: variable-size windows with count maps.
- **Q19** Merge K Sorted Lists: heaps, as in step 02.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
