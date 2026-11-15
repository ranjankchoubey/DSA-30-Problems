# Roadmap → Q28 · 787. Cheapest Flights Within K Stops (Medium)

**Goal:** find the cheapest price from city `src` to city `dst` using **at most `k` stops** (so at most `k + 1` flights), or `-1` if no such route exists.

> 📚 **Before you start:** read [Primer 09 · Advanced Graphs](../../course/primers/09-advanced-graphs.md).

## Skills you need

- [ ] Run Dijkstra with a min-heap on a weighted graph
- [ ] Relax edges in rounds (Bellman-Ford), where round `i` means "using at most `i` edges"
- [ ] Add "how many edges used" to the state when the problem limits it

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Network Delay Time](01-q29-network-delay-time.md) ↩ recap | Dijkstra | [LC 743](https://leetcode.com/problems/network-delay-time/) |
| 02 | [Bellman-Ford in k rounds](02-concept-bellman-ford-k-rounds.md) | Edge relaxation; copy the array each round to limit the edge count | — |
| 03 | [State graph: (city, stops)](03-concept-state-graph-city-stops.md) | Dijkstra over `(city, flights used)` states | — |
| ★ | [**Cheapest Flights Within K Stops** → NOTES](../NOTES.md) | Both methods, and why plain Dijkstra on cities fails | [LC 787](https://leetcode.com/problems/cheapest-flights-within-k-stops/) |

## How the steps add up

- Step 01: Dijkstra finds the cheapest route when there is **no** limit on the number of flights.
- Step 02: Bellman-Ford's round `i` gives the cheapest price using at most `i` flights, so stopping after `k + 1` rounds enforces the limit.
- Step 03: alternatively, make the flight count part of the node, so Dijkstra can't throw away a pricier route that used fewer flights.
- **Target:** explain why plain Dijkstra on cities fails, then solve it both ways.

## Threads

- **Q29** Network Delay Time: Dijkstra and Bellman-Ford without the stop limit.
- **Q19** Merge k Sorted Lists: the heap loop again.
- **Q26** Word Ladder: BFS by levels, where level = number of edges used, just like Bellman-Ford's rounds.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
