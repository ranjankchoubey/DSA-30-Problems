# Step 01 · Q25 · 210. Course Schedule II (recap)

[← Roadmap](00-roadmap.md) · [Next: Verifying an Alien Dictionary →](02-lc0953-verifying-an-alien-dictionary.md)

> ↩ **Taught in full in the Q25 ladder:** [Course Schedule II → NOTES](../../0210-course-schedule-ii/NOTES.md)

> 📚 **New to this?** Primer: [topological sort](../../course/primers/08-graphs.md#topological-sort)

## Recap

- An edge `a → b` means "`a` must come before `b`". A **topological order** lists every node so that all edges point forward.
- **Kahn's algorithm:** count in-degrees, queue every node with in-degree 0, pop one into the output, and decrement its neighbours, queueing any that hit 0.
- If the output is **shorter** than the number of nodes, the rest are stuck in a **cycle**, so no valid order exists.

## Why it matters here

In Alien Dictionary the "courses" are letters and the "prerequisites" are "letter `x` comes before letter `y`". Once the edges are built (steps 02 and 03), the answer is exactly a topological order of the letters, and a cycle means the dictionary contradicts itself, so you return `""`.
