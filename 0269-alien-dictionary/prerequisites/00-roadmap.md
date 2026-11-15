# Roadmap → Q30 · 269. Alien Dictionary 🔒 (Hard)

**Goal:** given a list of words already sorted in an unknown alphabet, return **any** valid order of its letters, or `""` if the list is inconsistent (a cycle such as `a < b < a`, or a longer word placed before its own prefix).

> 📚 **Before you start:** read [Primer 09 · Advanced Graphs](../../course/primers/09-advanced-graphs.md), and the [topological sort](../../course/primers/08-graphs.md#topological-sort) section of Primer 08 if it isn't fresh.

> 🔒 Premium target. Free version of the same problem: [NeetCode Foreign Dictionary](https://neetcode.io/problems/foreign-dictionary).

## Skills you need

- [ ] Topological sort with Kahn's algorithm, and detecting a cycle when it can't finish
- [ ] Comparing two words letter by letter under a custom order
- [ ] Turning "word A comes before word B" into one edge between two letters, and spotting the invalid prefix case

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Course Schedule II](01-q25-course-schedule-ii.md) ↩ recap | Topological sort | [LC 210](https://leetcode.com/problems/course-schedule-ii/) |
| 02 | [Verifying an Alien Dictionary](02-lc0953-verifying-an-alien-dictionary.md) | Comparing adjacent words under a custom order | [LC 953](https://leetcode.com/problems/verifying-an-alien-dictionary/) |
| 03 | [First difference gives an edge](03-concept-first-difference-gives-edge.md) | Building the letter graph, plus the invalid prefix case | — |
| ★ | [**Alien Dictionary** → NOTES](../NOTES.md) | Build the edges, then topologically sort the letters | [LC 269](https://leetcode.com/problems/alien-dictionary/) 🔒 · [NeetCode](https://neetcode.io/problems/foreign-dictionary) |

## How the steps add up

- Step 01: given "X must come before Y" edges, topological sort produces an order, or reports a cycle.
- Step 02: with the alphabet **known**, two adjacent words are compared at their first different letter, and a longer word can't precede its prefix.
- Step 03: run step 02 **backwards**: the first different letter of each adjacent pair tells you one edge of the unknown alphabet.
- **Target:** step 03's edges fed into step 01's topological sort.

## Threads

- **Q25** Course Schedule II: the same topological sort, with courses instead of letters.
- **Q29 / Q28**: the other Advanced Graphs problems, where the graph is given directly instead of being derived.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
