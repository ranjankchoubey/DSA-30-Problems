# Roadmap → Q25 · 210. Course Schedule II (Medium)

**Goal:** given `numCourses` and prerequisite pairs `[a, b]` ("take b before a"), return **one** valid order to take every course, or `[]` if a cycle makes it impossible. Solve it with Kahn's algorithm **and** with DFS cycle detection, then work out the minimum number of semesters.

> 📚 **Before you start:** read [Primer 08 · Graphs](../../course/primers/08-graphs.md).

## Skills you need

- [ ] Turn an edge list into an adjacency list, with the edge pointing the right way
- [ ] Count in-degree and out-degree for every node
- [ ] Detect a cycle in a directed graph with Kahn's algorithm and with 3-colour DFS
- [ ] Record the order in which nodes are removed (Kahn's) or finished (DFS)

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Adjacency list](01-concept-adjacency-list.md) | Edge list → adjacency list; direction matters | — |
| 02 | [Find the Town Judge](02-lc0997-find-the-town-judge.md) | In-degree and out-degree | [LC 997](https://leetcode.com/problems/find-the-town-judge/) |
| 03 | [Course Schedule](03-lc0207-course-schedule.md) | Cycle detection: Kahn's algorithm and 3-colour DFS | [LC 207](https://leetcode.com/problems/course-schedule/) |
| ★ | [**Course Schedule II** → NOTES](../NOTES.md) | Record the order instead of only answering yes/no | [LC 210](https://leetcode.com/problems/course-schedule-ii/) |

## How the steps add up

- Step 01: prerequisites are a **directed** graph; `[a, b]` becomes the edge `b → a`.
- Step 02: a node's **in-degree** counts what points into it. In-degree 0 means "nothing has to come first".
- Step 03: peel off in-degree-0 nodes (Kahn's) or look for a GREY node (DFS) to decide if **any** order exists.
- **Target:** the same two algorithms, but keep the order they produce: the removal order in Kahn's, the reversed finishing order in DFS.

## Threads

- **Q30** Alien Dictionary: builds its own graph from the word list, then runs exactly this topological sort.
- **Q26** Word Ladder: the same BFS queue, used for shortest paths instead of ordering.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
