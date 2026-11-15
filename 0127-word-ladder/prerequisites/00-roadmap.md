# Roadmap → Q26 · 127. Word Ladder (Hard)

**Goal:** change `beginWord` into `endWord` one letter at a time, where every word on the way must be in the dictionary. Return the number of words in the **shortest** chain (both ends included), or `0` if there's none. Solve it with BFS, speed it up with bidirectional BFS, and explain how to return every shortest chain.

> 📚 **Before you start:** read [Primer 08 · Graphs](../../course/primers/08-graphs.md).

## Skills you need

- [ ] BFS level by level, and why it gives the shortest path when every step costs the same
- [ ] Seeing a "change one thing at a time" puzzle as a graph whose nodes are states
- [ ] Generating neighbours of a word quickly instead of comparing it with every other word
- [ ] Bidirectional BFS: growing two frontiers until they meet

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Shortest Path in Binary Matrix](01-lc1091-shortest-path-in-binary-matrix.md) | BFS level by level = shortest path on an unweighted graph | [LC 1091](https://leetcode.com/problems/shortest-path-in-binary-matrix/) |
| 02 | [Minimum Genetic Mutation](02-lc0433-minimum-genetic-mutation.md) | Strings as nodes, one-letter changes as edges: a small Word Ladder | [LC 433](https://leetcode.com/problems/minimum-genetic-mutation/) |
| 03 | [Wildcard buckets](03-concept-wildcard-buckets.md) | `h*t` buckets for fast neighbour lookup | — |
| ★ | [**Word Ladder** → NOTES](../NOTES.md) | BFS over buckets, then bidirectional BFS | [LC 127](https://leetcode.com/problems/word-ladder/) |

## How the steps add up

- Step 01: BFS reaches every cell in the fewest moves, one ring (level) at a time.
- Step 02: the same BFS, but a node is a **string** and a move changes one letter (only 4 letters, tiny bank).
- Step 03: with 26 letters and 5000 words, find neighbours through shared patterns like `h*t` instead of trying every letter.
- **Target:** BFS from `beginWord` counting words, then search from **both** ends to explore far fewer words.

## Threads

- **Q29** Network Delay Time: recaps step 01, then swaps the BFS queue for a heap when edges have weights (Dijkstra).
- **Q25** Course Schedule II: the same BFS queue, used for ordering instead of distance.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
