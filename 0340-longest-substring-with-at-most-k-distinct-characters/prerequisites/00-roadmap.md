# Roadmap → Q9 · 340. Longest Substring with At Most K Distinct Characters (Medium)

**Goal:** return the length of the longest contiguous stretch of `s` that contains **at most `k` distinct** characters, in **O(n)**. 🔒 LeetCode Premium: the free [LC 904 Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) is the same problem with k = 2.

> 📚 **Before you start:** read [Primer 03 · Sliding Window](../../course/primers/03-sliding-window.md).

## Skills you need

- [ ] Slide a fixed-size window: add on the right, drop on the left
- [ ] Grow a variable window, and shrink it while it breaks the rule
- [ ] Track the number of distinct values in the window with a count map

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Maximum Average Subarray I](01-lc0643-maximum-average-subarray-i.md) | Fixed-size window | [LC 643](https://leetcode.com/problems/maximum-average-subarray-i/) |
| 02 | [Longest Substring Without Repeating Characters](02-lc0003-longest-substring-without-repeating-characters.md) | Variable window: expand, then shrink while invalid | [LC 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |
| 03 | [Fruit Into Baskets](03-lc0904-fruit-into-baskets.md) | At most **2** distinct: the target with k = 2 | [LC 904](https://leetcode.com/problems/fruit-into-baskets/) |
| ★ | [**Longest Substring with At Most K Distinct Characters** → NOTES](../NOTES.md) | Replace 2 with `k` | [LC 340](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) 🔒 |

## How the steps add up

- Step 01: a window slides by adding the element on the right and removing the one on the left, O(1) per step.
- Step 02: the window size is no longer fixed. Grow it on the right; when it breaks the rule ("no repeats"), shrink it from the left.
- Step 03: the rule becomes "at most 2 distinct". A count map tells you how many distinct values are inside: `len(count)`.
- **Target:** the same loop with `len(count) > k`.

## Threads

- **Q4** Minimum Window Substring: the same variable window, but it shrinks **while valid** to find a minimum.
- **Q5** Sliding Window Maximum: a fixed-size window whose summary (the max) can't be undone by subtraction.
- **Q1** Subarray Sum Equals K: why a window needs a monotonic rule, and what to do when it doesn't have one.
- **Q13** LRU Cache: the O(k)-memory stream follow-up evicts the **least recently seen** character, the same idea as an LRU cache.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
