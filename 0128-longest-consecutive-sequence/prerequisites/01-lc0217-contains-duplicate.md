# Step 01 · 217. Contains Duplicate (recap)

[← Roadmap](00-roadmap.md) · [Next: Max Consecutive Ones →](02-lc0485-max-consecutive-ones.md)

> ↩ **Taught in full in the Q8 ladder:** [Set Mismatch → Step 01 · Contains Duplicate](../../0645-set-mismatch/prerequisites/01-lc0217-contains-duplicate.md)

> 📚 **New to this?** Primer: [hash set and hash map](../../course/primers/01-arrays-and-hashing.md#hash-set-and-hash-map)

## Recap

- A Python `set` answers "have I seen `x`?" in **O(1)** average time.
- Build it in one pass: `seen = set(nums)` takes O(n) time and O(n) space.
- It also removes duplicates for free.

## Why it matters here

Longest Consecutive Sequence can't sort, so to know whether `x + 1` exists it needs an O(1) lookup, which is exactly this set. Building `set(nums)` also removes duplicate values, so they can't cause repeated work.
