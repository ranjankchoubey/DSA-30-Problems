# Step 02 · 1091. Shortest Path in Binary Matrix (recap)

[← Previous: Kth Largest Element in a Stream](01-lc0703-kth-largest-element-in-a-stream.md) · [Roadmap](00-roadmap.md) · [Next: Why BFS fails with weights →](03-concept-why-bfs-fails-with-weights.md)

> ↩ **Taught in full in the Q26 ladder:** [Word Ladder → Step 01 · Shortest Path in Binary Matrix](../../0127-word-ladder/prerequisites/01-lc1091-shortest-path-in-binary-matrix.md)

> 📚 **New to this?** Primer: [BFS](../../course/primers/08-graphs.md#bfs)

## Recap

- BFS explores the graph **level by level**: everything 1 edge away, then 2 edges away, and so on.
- So the first time BFS reaches a node, it has used the **fewest possible edges**, and that number is final.
- Mark a node as visited when you **enqueue** it, so it's never added twice: O(V + E) overall.

## Why it matters here

Network Delay Time is "shortest path from `k` to every node", which is BFS's job. The catch is that edges now have **different** travel times, so fewest edges is no longer the same as least time. Step 03 shows exactly where BFS goes wrong, and the target keeps BFS's loop but swaps the queue for a heap.
