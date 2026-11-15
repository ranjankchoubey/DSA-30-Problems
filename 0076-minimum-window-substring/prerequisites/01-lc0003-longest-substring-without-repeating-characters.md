# Step 01 · 3. Longest Substring Without Repeating Characters (recap)

[← Roadmap](00-roadmap.md) · [Next: Minimum Size Subarray Sum →](02-lc0209-minimum-size-subarray-sum.md)

> ↩ **Taught in full in the Q9 ladder:** [Longest Substring with At Most K Distinct Characters → Step 02 · Longest Substring Without Repeating Characters](../../0340-longest-substring-with-at-most-k-distinct-characters/prerequisites/02-lc0003-longest-substring-without-repeating-characters.md)

> 📚 **New to this?** Primer: [variable size window](../../course/primers/03-sliding-window.md#variable-size-window)

## Recap

- Move `right` forward one step at a time and add `s[right]` to the window.
- While the window breaks the rule, remove `s[left]` and move `left` forward.
- Each pointer moves at most n times, so the scan is **O(n)**.

## Why it matters here

Minimum Window Substring uses the same two forward-only pointers, but **flips** the loop: it grows until the window is **valid** (contains all of `t`), then shrinks **while it stays valid**, recording the smallest length. Step 02 practises that flipped loop on numbers first.
