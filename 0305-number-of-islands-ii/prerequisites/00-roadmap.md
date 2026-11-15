# Roadmap → Q27 · 305. Number of Islands II (Hard)

**Goal:** an `m × n` grid starts as all water. Land cells are added one at a time; after **each** addition, report how many islands (4-directionally connected land groups) there are. Use Union-Find with path compression and union by rank, and state the cost per operation.

> 📚 **Before you start:** read [Primer 08 · Graphs](../../course/primers/08-graphs.md).

> 🔒 The target is LeetCode Premium. Every step below is free; LC 200 and LC 684 are the free alternatives listed in [INDEX.md](../../INDEX.md).

## Skills you need

- [ ] Treat a grid as a graph and count connected groups with DFS/BFS
- [ ] Union-Find: `find` the root of a group, `union` two groups, keep a running group count
- [ ] Path compression and union by rank, and why together they make each operation almost O(1)
- [ ] Use `union()`'s return value to know whether two groups actually merged

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Number of Islands](01-lc0200-number-of-islands.md) | Grid connectivity with DFS/BFS | [LC 200](https://leetcode.com/problems/number-of-islands/) |
| 02 | [Number of Provinces](02-lc0547-number-of-provinces.md) | Introduction to Union-Find: count drops by 1 per real merge | [LC 547](https://leetcode.com/problems/number-of-provinces/) |
| 03 | [Redundant Connection](03-lc0684-redundant-connection.md) | Path compression + union by rank; `union()` returns whether it merged | [LC 684](https://leetcode.com/problems/redundant-connection/) |
| ★ | [**Number of Islands II** → NOTES](../NOTES.md) | Union-Find on grid cells that arrive one at a time | [LC 305](https://leetcode.com/problems/number-of-islands-ii/) 🔒 |

## How the steps add up

- Step 01: counting islands **once** is a flood fill, O(m·n). Re-running it after every addition would be far too slow.
- Step 02: Union-Find keeps the group count up to date as connections arrive: start with one group per node, subtract 1 per merge.
- Step 03: make `find` fast (path compression, union by rank) and let `union` report whether two nodes were already connected.
- **Target:** each new land cell is a new group (+1), then `union` it with each land neighbour (−1 per real merge).

## Threads

- **Q6** Longest Consecutive Sequence can also be solved with Union-Find (union `x` with `x + 1`), a nice exercise after this ladder.
- **Q26** Word Ladder: another graph you never build explicitly (words instead of cells).

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
