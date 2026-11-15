# Step 01 · 739. Daily Temperatures (recap)

[← Roadmap](00-roadmap.md) · [Next: Next Greater Element I →](02-lc0496-next-greater-element-i.md)

> ↩ **Taught in full in the Q5 ladder:** [Sliding Window Maximum → Step 03 · Daily Temperatures](../../0239-sliding-window-maximum/prerequisites/03-lc0739-daily-temperatures.md)

> 📚 **New to this?** Primer: [monotonic stack](../../course/primers/04-stack.md#monotonic-stack)

## Recap

- Keep a stack of **indices** still waiting for a warmer day. Their temperatures decrease from bottom to top.
- When day `i` is warmer than the top, day `i` is the answer for the top: pop index `j`, record `i − j`, and repeat.
- Each index is pushed once and popped at most once, so the whole scan is **O(n)** despite the inner `while`.

## Why it matters here

Next Greater Element II asks the same question, "what is the first bigger value after me?". It wants the **value** instead of the distance, and the array is **circular**, so indices still waiting at the end may find their answer back at the start.
