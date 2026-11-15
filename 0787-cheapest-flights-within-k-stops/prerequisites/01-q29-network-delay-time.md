# Step 01 · Q29 · 743. Network Delay Time (recap)

[← Roadmap](00-roadmap.md) · [Next: Bellman-Ford in k rounds →](02-concept-bellman-ford-k-rounds.md)

> ↩ **Taught in full in the Q29 ladder:** [Network Delay Time → NOTES](../../0743-network-delay-time/NOTES.md)

> 📚 **New to this?** Primer: [Dijkstra](../../course/primers/09-advanced-graphs.md#dijkstra)

## Recap

- **Dijkstra:** keep a min-heap of `(cost, node)`. Pop the cheapest; the first pop of a node is its final cost (all weights ≥ 0). Then push its neighbours.
- Skip a popped node that is already settled, so each node is expanded **once**: O(E log E).
- **Bellman-Ford** relaxes every edge in rounds instead. It is slower, O(V · E), but each round has a clear meaning.

## Why it matters here

Flights are a weighted directed graph, and "cheapest price" is a shortest path, so Dijkstra is the natural first try. But settling each **city** once is exactly what breaks when the number of flights is limited: the cheapest way into a city may use too many flights. Steps 02 and 03 show two ways to fix that.
