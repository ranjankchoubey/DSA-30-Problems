# Step 01 · 643. Maximum Average Subarray I (recap)

[← Roadmap](00-roadmap.md) · [Next: Brute force and heap →](02-concept-brute-force-and-heap.md)

> ↩ **Taught in full in the Q9 ladder:** [Longest Substring with At Most K Distinct Characters → Step 01 · Maximum Average Subarray I](../../0340-longest-substring-with-at-most-k-distinct-characters/prerequisites/01-lc0643-maximum-average-subarray-i.md)

> 📚 **New to this?** Primer: [fixed size window](../../course/primers/03-sliding-window.md#fixed-size-window)

## Recap

- The window of size `k` ending at index `i` is `nums[i − k + 1 .. i]`. The first one ends at `i = k − 1`, so there are `n − k + 1` windows.
- Slide it in O(1): add `nums[i]` (entering) and subtract `nums[i − k]` (leaving).
- Over the whole array that is **O(n)** instead of O(nk).

## Why it matters here

Sliding Window Maximum has exactly the same windows, and its output has one value per window. But a **max** can't be "subtracted": when the current maximum leaves, nothing in a single number tells you the next one. Steps 02–04 build a summary that can handle that.
