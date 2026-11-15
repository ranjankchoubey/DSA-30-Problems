# Roadmap → Q4 · 76. Minimum Window Substring (Hard)

**Goal:** find the **shortest** contiguous window of `s` that contains every character of `t`, **counting duplicates** (`t = "AABC"` needs two A's). Return the leftmost one on ties, or `""` if none exists. Required: **O(|s| + |t|)**.

> 📚 **Before you start:** read [Primer 03 · Sliding Window](../../course/primers/03-sliding-window.md).

## Skills you need

- [ ] Grow and shrink a variable window with two forward-only pointers
- [ ] Shrink **while the window is valid** to find a minimum
- [ ] Count characters with a frequency map
- [ ] Compare a sliding window's counts with a target's counts
- [ ] Check "is the window valid?" in O(1) with a `formed` counter

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Longest Substring Without Repeating Characters](01-lc0003-longest-substring-without-repeating-characters.md) ↩ recap | Variable window | [LC 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |
| 02 | [Minimum Size Subarray Sum](02-lc0209-minimum-size-subarray-sum.md) | Shrink **while valid** to find a minimum | [LC 209](https://leetcode.com/problems/minimum-size-subarray-sum/) |
| 03 | [Valid Anagram](03-lc0242-valid-anagram.md) | Character frequency maps | [LC 242](https://leetcode.com/problems/valid-anagram/) |
| 04 | [Find All Anagrams in a String](04-lc0438-find-all-anagrams-in-a-string.md) | A sliding window matched against a frequency map | [LC 438](https://leetcode.com/problems/find-all-anagrams-in-a-string/) |
| 05 | [Have vs need counter](05-concept-have-vs-need-counter.md) | `formed == required` gives an O(1) validity check | — |
| ★ | [**Minimum Window Substring** → NOTES](../NOTES.md) | Step 02's loop with step 05's check | [LC 76](https://leetcode.com/problems/minimum-window-substring/) |

## How the steps add up

- Step 01: two pointers that only move forward give an O(n) window.
- Step 02: to find the **shortest** valid window, grow until valid, then shrink **while still valid**, recording the length each time.
- Step 03: "contains these characters with these counts" is a question about frequency maps.
- Step 04: a window's frequency map can be updated in O(1) as it slides, and compared against a target map.
- Step 05: instead of comparing whole maps, keep one number `formed` that says how many characters are fully covered.
- **Target:** step 02's shrink-while-valid loop, with "valid" meaning `formed == required`.

## Threads

- **Q9** Longest Substring with At Most K Distinct: the "longest" version of the same loop (shrink while **invalid**).
- **Q5** Sliding Window Maximum: a fixed window with a deque instead of a count map.
- **Q1** Subarray Sum Equals K: why a window needs a monotonic rule. Here, adding characters can never make a valid window invalid.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
