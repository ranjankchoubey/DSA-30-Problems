# Step 01 · 1. Two Sum (recap)

[← Roadmap](00-roadmap.md) · [Next: Two Sum II →](02-lc0167-two-sum-ii-input-array-is-sorted.md)

> ↩ **Taught in full in the Q1 ladder:** [Subarray Sum Equals K → Step 01 · Two Sum](../../0560-subarray-sum-equals-k/prerequisites/01-lc0001-two-sum.md)

> 📚 **New to this?** Primer: [complement lookup](../../course/primers/01-arrays-and-hashing.md#2-complement-lookup-two-sum)

## Recap

- For each `x`, the value that completes the pair is its **complement** `target − x`.
- A hash map of value → index answers "have I seen the complement?" in **O(1)**, so the whole scan is O(n).
- Look up **before** inserting `x`, so an element is never paired with itself.

## Why it matters here

3Sum asks for `a + b + c = 0`. Fix `a` and the rest is "find `b + c = −a`", which is Two Sum with target `−a`. The hash map version works, but removing duplicate triplets with it is messy. The next steps solve Two Sum on a **sorted** array instead, where duplicates sit side by side and are easy to skip.
