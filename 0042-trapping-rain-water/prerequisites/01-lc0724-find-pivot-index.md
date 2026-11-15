# Step 01 · 724. Find Pivot Index (recap)

[← Roadmap](00-roadmap.md) · [Next: Container With Most Water →](02-lc0011-container-with-most-water.md)

> ↩ **Taught in full in the Q7 ladder:** [Product of Array Except Self → Step 02 · Find Pivot Index](../../0238-product-of-array-except-self/prerequisites/02-lc0724-find-pivot-index.md)

> 📚 **New to this?** Primer: [prefix sums](../../course/primers/01-arrays-and-hashing.md#3-prefix-sums)

## Recap

- Split "everything except `i`" into a **left** part (indices `< i`) and a **right** part (indices `> i`).
- Keep the left part as a running total that **excludes** `i`: compare first, then add `nums[i]`.
- For sums, the right part comes for free as `total − left − nums[i]`.

## Why it matters here

The water above bar `i` depends on the tallest bar to its **left** and the tallest bar to its **right**: the same left/right split, with **max** instead of sum. A maximum can't be "subtracted" out of a total, so the right side has to be built directly: by a right-to-left pass (step 04) or by a second pointer moving in from the right (the target).
