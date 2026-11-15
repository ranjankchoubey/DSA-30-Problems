# Roadmap → Q22 · 124. Binary Tree Maximum Path Sum (Hard)

**Goal:** find the largest sum of any path in a binary tree. A path joins connected nodes without revisiting any, may start and end anywhere, and node values can be negative. O(n) time.

> 📚 **Before you start:** read [Primer 07 · Trees](../../course/primers/07-trees.md).

## Skills you need

- [ ] Write a tree recursion: base case, ask the children, combine
- [ ] Carry a running sum along a downward path
- [ ] Return one value to the parent while recording a different global answer
- [ ] Drop a branch whose sum is negative with `max(0, gain)`

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Maximum Depth of Binary Tree](01-lc0104-maximum-depth-of-binary-tree.md) ↩ | Tree recursion | [LC 104](https://leetcode.com/problems/maximum-depth-of-binary-tree/) |
| 02 | [Path Sum](02-lc0112-path-sum.md) | Carrying sums along a path | [LC 112](https://leetcode.com/problems/path-sum/) |
| 03 | [Diameter of Binary Tree](03-lc0543-diameter-of-binary-tree.md) | Return one value to the parent, record the global answer | [LC 543](https://leetcode.com/problems/diameter-of-binary-tree/) |
| 04 | [Drop negative branches](04-concept-drop-negative-branches.md) | `max(0, gain)` | — |
| ★ | [**Binary Tree Maximum Path Sum** → NOTES](../NOTES.md) | Diameter's shape with sums instead of edge counts, plus `max(0, ·)` | [LC 124](https://leetcode.com/problems/binary-tree-maximum-path-sum/) |

## How the steps add up

- Step 01: a node's answer is built from its children's answers.
- Step 02: sums along a path, but only straight down from the root to a leaf.
- Step 03: a path can **bend** at a node. The helper returns one arm to the parent and records the bent path (both arms) globally.
- Step 04: with negative values, an arm can hurt the sum, so take `max(0, arm)`.
- **Target:** step 03's return-vs-record split, using sums and step 04's `max(0, ·)`.

## Threads

- **Q23** LCA II: the helper returns a count and records the answer node, the same shape.
- **Q11** Maximum Product Subarray / Kadane (LC 53): "drop a negative running sum" is the same idea as `max(0, gain)`.
- **Q21, Q24**: recursion on trees.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
