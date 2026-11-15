# Roadmap → Q12 · 5. Longest Palindromic Substring (Medium)

**Goal:** return the longest substring of `s` that reads the same forwards and backwards (any one if there's a tie), in O(n²) time with O(1) extra space.

> 📚 **Before you start:** read [Primer 10 · 1-D DP](../../course/primers/10-1d-dp.md).

## Skills you need

- [ ] Check a palindrome with two pointers moving inwards
- [ ] Grow a palindrome outwards from a centre, for both odd and even lengths
- [ ] Describe the `dp[i][j]` "is `s[i..j]` a palindrome?" table and why it's DP

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Valid Palindrome](01-lc0125-valid-palindrome.md) | Two-pointer palindrome check | [LC 125](https://leetcode.com/problems/valid-palindrome/) |
| 02 | [Palindromic Substrings](02-lc0647-palindromic-substrings.md) | Expand around odd and even centres | [LC 647](https://leetcode.com/problems/palindromic-substrings/) |
| 03 | [The is-palindrome DP table](03-concept-dp-is-palindrome-table.md) | `dp[i][j]` alternative (why NeetCode files it under DP) | — |
| ★ | [**Longest Palindromic Substring** → NOTES](../NOTES.md) | Expand around every centre, keep the longest | [LC 5](https://leetcode.com/problems/longest-palindromic-substring/) |

## How the steps add up

- Step 01: a palindrome check compares the two ends and moves **inwards**.
- Step 02: turn it around: start at a centre and move **outwards**, so every palindrome is found while growing, in O(n²) total.
- Step 03: the same facts stored in a table, `dp[i][j] = s[i] == s[j] and dp[i+1][j-1]`. Also O(n²), but with O(n²) memory.
- **Target:** step 02's loop, recording the longest palindrome instead of counting them.

## Threads

- **Q10 / Q2**: two pointers moving towards each other.
- **Q11** Maximum Product Subarray: the other 1-D DP problem in the course.
- **Q4 / Q9**: other "best substring" problems, solved with a sliding window instead.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
