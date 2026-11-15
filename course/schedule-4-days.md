# 4-Day Course Schedule (≈ 22 hours)

All 30 problems in 4 days, in teaching order. Each of the 10 patterns gets:

- a **primer** (15 min): the pattern, its template, and when to use it
- **1 ★ anchor problem** (50–60 min): taught in full, from brute force to optimal, with the code written on the board
- **walkthroughs** (20 min each) for its other problems: trigger, key insight, dry run, core code

This is a **lecture, not a lab**: students don't code in class. They think, answer your questions, and predict what comes next. All coding happens at home: the **prerequisite steps** and **re-solving the problems on LeetCode** using the repo. Mastering all 30 takes about **40–50 hours of self-study** after the course.

> Times assume **09:30–12:30** (forenoon) and **13:30–16:30** (afternoon). Shift them to fit the venue.

## How each slot runs

| ★ Anchor (60 min) | | Walkthrough (20 min) | |
| --- | --- | --- | --- |
| Hook: scenario + "how would you do it by hand?" | 5 | Hook + what in the problem points to the pattern | 3 |
| Brute force together | 10 | Key insight (box it on the board) | 5 |
| Guided questions to the insight | 15 | Dry run on the board example | 7 |
| Dry run on the board | 10 | Core code + complexity | 5 |
| **Code on the board**: you write it line by line; before each key line, ask *"what comes next?"* | 10 | | |
| Edge cases + common bugs: *"what breaks if we remove this line?"* | 5 | | |
| One follow-up question from the course doc | 5 | | |

Board layout every time: **Problem & insight | Dry run | Code & complexity**.

**Keeping students active without coding:** ask a question and wait 10 seconds before answering it yourself · have them predict the next row of the dry run · ask *"which pattern?"* before revealing it · ask for a show of hands on A/B choices (*"move left or right pointer?"*).

## Before Day 1

- [ ] Send students the repo link and ask them to read [START_HERE](../START_HERE.md) and [Primer 00](primers/00-how-to-approach-a-new-problem.md).
- [ ] Students need basic Python (lists, dicts, loops, functions) to follow the board code, and a **LeetCode account** for homework.
- [ ] Ask the venue: whiteboard / digital board / projector + HDMI? Wi-Fi?
- [ ] Bring: charged tablet, HDMI adapter, printed lesson cards, 3 marker colours.

---

## Day 1: Arrays & Hashing · Two Pointers · start Sliding Window

### Forenoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 09:30 | 30 | Welcome, course map, how to use the repo. [Primer 00](primers/00-how-to-approach-a-new-problem.md) demo: Contains Duplicate, from brute force to a set | Write the brute force, find the repeated question, pick a tool | `[3,1,4,1]` → True |
| 10:00 | 15 | [Primer 01 · Arrays & Hashing](primers/01-arrays-and-hashing.md) | set/dict give O(1) lookup; prefix sums | — |
| 10:15 | 20 | [Q8 · Set Mismatch](../0645-set-mismatch/NOTES.md) | Value x owns index x−1: flip its sign. Already negative → duplicate; still positive → missing | `[1,2,2,4]` → `[2,3]` |
| 10:35 | 20 | [Q7 · Product of Array Except Self](../0238-product-of-array-except-self/NOTES.md) | Answer = product on the left × product on the right; two passes, no division | `[1,2,3,4]` → `[24,12,8,6]` |
| 10:55 | 10 | *Break* | | |
| 11:05 | 20 | [Q6 · Longest Consecutive Sequence](../0128-longest-consecutive-sequence/NOTES.md) | Set; only start counting at x when x−1 is missing | `[100,4,200,1,3,2]` → 4 |
| 11:25 | 60 | ★ [Q1 · Subarray Sum Equals K](../0560-subarray-sum-equals-k/NOTES.md) | Count earlier prefix sums equal to `prefix − k`; start with `{0: 1}`; negatives rule out sliding window | `[1,2,3,-3,3], k=3` → 5 |
| 12:25 | 5 | Questions | | |

### Afternoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 13:30 | 15 | [Primer 02 · Two Pointers](primers/02-two-pointers.md) | Sorted data → pointers at both ends | — |
| 13:45 | 60 | ★ [Q10 · 3Sum](../0015-3sum/NOTES.md) | Sort; fix `nums[i]`; two pointers on the rest for `−nums[i]`; skip duplicates at both levels | `[-1,0,1,2,-1,-4]` → `[[-1,-1,2],[-1,0,1]]` |
| 14:45 | 20 | [Q2 · Trapping Rain Water](../0042-trapping-rain-water/NOTES.md) | `water = min(maxL, maxR) − h`; move the side with the smaller max | `[4,2,0,3,2,5]` → 9 |
| 15:05 | 10 | *Break* | | |
| 15:15 | 15 | [Primer 03 · Sliding Window](primers/03-sliding-window.md) | Expand right; shrink left while invalid | — |
| 15:30 | 20 | [Q9 · Longest Substring with At Most K Distinct](../0340-longest-substring-with-at-most-k-distinct-characters/NOTES.md) 🔒 | Count letters in the window; shrink while distinct > k | `"eceba", k=2` → 3 |
| 15:50 | 30 | **Pattern drill + doubts:** read 6 one-line problem descriptions; students call out the pattern and key insight; then open questions on today's problems | | |
| 16:20 | 10 | Recap + exit question: *"Count subarrays of `[3,-3,1,-1]` with sum 0?"* (answer 3: prefix + hash map) | | |

**Homework:** re-solve Q1 and Q10 from memory · prerequisite steps for [Q8](../0645-set-mismatch/prerequisites/00-roadmap.md), [Q7](../0238-product-of-array-except-self/prerequisites/00-roadmap.md), [Q4](../0076-minimum-window-substring/prerequisites/00-roadmap.md)

---

## Day 2: Sliding Window · Stack · Binary Search

### Forenoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 09:30 | 10 | Recap quiz: name the pattern for 5 one-line problems | | |
| 09:40 | 60 | ★ [Q4 · Minimum Window Substring](../0076-minimum-window-substring/NOTES.md) | Need-counts for `t`; expand until all covered (`formed == required`), shrink while valid, record the minimum | `"ADOBECODEBANC", "ABC"` → `"BANC"` |
| 10:40 | 20 | [Q5 · Sliding Window Maximum](../0239-sliding-window-maximum/NOTES.md) | Deque of indices with decreasing values; pop smaller from the back and expired from the front; the front is the max | `[1,3,-1,-3,5,3,6,7], k=3` → `[3,3,5,5,6,7]` |
| 11:00 | 10 | *Break* | | |
| 11:10 | 15 | [Primer 04 · Stack](primers/04-stack.md) | Last in, first out; monotonic stack for "next greater / smaller" | — |
| 11:25 | 20 | [Q20 · Min Stack](../0155-min-stack/NOTES.md) | Push `(value, min so far)` together | `push -2, 0, -3; getMin; pop; top; getMin` → -3, 0, -2 |
| 11:45 | 20 | [Q17 · Next Greater Element II](../0503-next-greater-element-ii/NOTES.md) | Decreasing stack of indices; loop 2n times with `i % n` for the wrap-around | `[1,2,1]` → `[2,-1,2]` |
| 12:05 | 25 | Questions + recap of monotonic stack | | |

### Afternoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 13:30 | 60 | ★ [Q15 · Largest Rectangle in Histogram](../0084-largest-rectangle-in-histogram/NOTES.md) | Each bar is the shortest bar of its best rectangle: width = next smaller − previous smaller − 1; increasing stack | `[2,1,5,6,2,3]` → 10 |
| 14:30 | 20 | [Q14 · Longest Valid Parentheses](../0032-longest-valid-parentheses/NOTES.md) | Stack of indices starting with −1; an unmatched `)` becomes the new base | `")()())"` → 4 |
| 14:50 | 20 | [Q16 · Basic Calculator III](../0772-basic-calculator-iii/NOTES.md) 🔒 | Number stack + operator stack; apply equal-or-higher precedence before pushing; `(` and `)`; truncate toward zero | `"3+2*2"` → 7 |
| 15:10 | 10 | *Break* | | |
| 15:20 | 15 | [Primer 05 · Binary Search](primers/05-binary-search.md) | Halve the search space; search on a yes/no condition | — |
| 15:35 | 50 | ★ [Q3 · Median of Two Sorted Arrays](../0004-median-of-two-sorted-arrays/NOTES.md) | Binary search a cut in the shorter array so left halves total `(m+n+1)/2` and `aLeft ≤ bRight`, `bLeft ≤ aRight` | `[1,3], [2]` → 2.0 |
| 16:25 | 5 | Exit question: *"Next greater, circular, for `[2,1,2]`?"* (answer `[-1,2,-1]`) | | |

**Homework:** re-solve Q4 and Q15 · prerequisite steps for [Q5](../0239-sliding-window-maximum/prerequisites/00-roadmap.md), [Q17](../0503-next-greater-element-ii/prerequisites/00-roadmap.md), [Q13](../0146-lru-cache/prerequisites/00-roadmap.md)

---

## Day 3: Linked List · Trees · start Graphs

### Forenoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 09:30 | 10 | Recap quiz | | |
| 09:40 | 15 | [Primer 06 · Linked List](primers/06-linked-list.md) | Dummy node; relink pointers carefully, in order | — |
| 09:55 | 60 | ★ [Q13 · LRU Cache](../0146-lru-cache/NOTES.md) | dict key → node + doubly linked list with dummy head/tail; move to front on use; evict from the tail | cap 2: `put 1, put 2, get 1, put 3, get 2…` → 1, −1, −1, 3, 4 |
| 10:55 | 10 | *Break* | | |
| 11:05 | 20 | [Q18 · Reverse Nodes in k-Group](../0025-reverse-nodes-in-k-group/NOTES.md) | Check k nodes exist; reverse the group; link the previous group's tail to the new head | `[1,2,3,4,5], k=2` → `[2,1,4,3,5]` |
| 11:25 | 25 | [Q19 · Merge k Sorted Lists](../0023-merge-k-sorted-lists/NOTES.md) + 5-min heap intro ([Primer 11](primers/11-heap-priority-queue.md)) | Min-heap of list heads; pop smallest, push its next: O(N log k) | `[[1,4,5],[1,3,4],[2,6]]` |
| 11:50 | 15 | [Primer 07 · Trees](primers/07-trees.md) | Recursion: solve children, combine at the node | — |
| 12:05 | 20 | [Q24 · Construct Tree from Preorder + Inorder](../0105-construct-binary-tree-from-preorder-and-inorder-traversal/NOTES.md) | `preorder[0]` is the root; its inorder position splits left/right; hash map value → index | `pre [3,9,20,15,7]`, `in [9,3,15,20,7]` |
| 12:25 | 5 | Questions | | |

### Afternoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 13:30 | 20 | [Q23 · LCA of a Binary Tree II](../1644-lowest-common-ancestor-of-a-binary-tree-ii/NOTES.md) 🔒 | Classic LCA recursion, but count how many of p, q were found; return the LCA only if both | tree `[3,5,1,6,2,0,8,null,null,7,4]`, `p=5, q=10` → null |
| 13:50 | 60 | ★ [Q22 · Binary Tree Maximum Path Sum](../0124-binary-tree-maximum-path-sum/NOTES.md) | Return the best **one-sided** gain `max(0, …)` to the parent; record `left + right + node` globally | `[-10,9,20,null,null,15,7]` → 42 |
| 14:50 | 20 | [Q21 · Serialize and Deserialize Binary Tree](../0297-serialize-and-deserialize-binary-tree/NOTES.md) | Preorder with `#` for null; rebuild by reading tokens in the same order | `[1,2,3,null,null,4,5]` round trip |
| 15:10 | 10 | *Break* | | |
| 15:20 | 15 | [Primer 08 · Graphs](primers/08-graphs.md) | Adjacency list; BFS / DFS; topological sort | — |
| 15:35 | 50 | ★ [Q25 · Course Schedule II](../0210-course-schedule-ii/NOTES.md) | Kahn: in-degrees; queue nodes with in-degree 0; if fewer than n come out → cycle → `[]` | `4, [[1,0],[2,0],[3,1],[3,2]]` → `[0,1,2,3]` |
| 16:25 | 5 | Exit question: *"n=2, prerequisites `[[0,1]]`: an order?"* (answer `[1,0]`) | | |

**Homework:** re-solve Q13 and Q22 · prerequisite steps for [Q24](../0105-construct-binary-tree-from-preorder-and-inorder-traversal/prerequisites/00-roadmap.md), [Q26](../0127-word-ladder/prerequisites/00-roadmap.md), [Q29](../0743-network-delay-time/prerequisites/00-roadmap.md)

---

## Day 4: Graphs · Advanced Graphs · 1-D DP · wrap-up

### Forenoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 09:30 | 10 | Recap quiz | | |
| 09:40 | 20 | [Q26 · Word Ladder](../0127-word-ladder/NOTES.md) | BFS level by level = fewest steps; neighbours via wildcard buckets like `h*t`; bidirectional BFS to speed up | `hit → cog` → 5 |
| 10:00 | 20 | [Q27 · Number of Islands II](../0305-number-of-islands-ii/NOTES.md) 🔒 | Union-Find: each new land +1 island, each successful union −1; ignore repeats | 3×3, `[[0,0],[0,1],[1,2],[2,1]]` → `[1,1,2,3]` |
| 10:20 | 15 | [Primer 09 · Advanced Graphs](primers/09-advanced-graphs.md) | Weighted shortest path: Dijkstra; with limits or negative edges: Bellman-Ford | — |
| 10:35 | 60 | ★ [Q29 · Network Delay Time](../0743-network-delay-time/NOTES.md) | Dijkstra with a heap from k; answer = the largest distance, −1 if any node is unreached | `[[2,1,1],[2,3,1],[3,4,1]], n=4, k=2` → 2 |
| 11:35 | 10 | *Break* | | |
| 11:45 | 20 | [Q28 · Cheapest Flights Within K Stops](../0787-cheapest-flights-within-k-stops/NOTES.md) | Plain Dijkstra fails (a cheaper path may use too many stops); Bellman-Ford for k+1 rounds, copying the array each round | course graph, `k=1` → 700, `k=2` → 400 |
| 12:05 | 20 | [Q30 · Alien Dictionary](../0269-alien-dictionary/NOTES.md) 🔒 | The first differing letters of adjacent words give an edge; a longer word before its prefix → invalid; topological sort | `["wrt","wrf","er","ett","rftt"]` → `"wertf"` |
| 12:25 | 5 | Questions | | |

### Afternoon

| Time | Min | Session | Key insight to land | Board example |
| --- | --- | --- | --- | --- |
| 13:30 | 15 | [Primer 10 · 1-D DP](primers/10-1d-dp.md) | Define the state ("best answer ending at i"), then the recurrence | — |
| 13:45 | 20 | [Q12 · Longest Palindromic Substring](../0005-longest-palindromic-substring/NOTES.md) | Expand around every centre (odd and even): O(n²) | `"babad"` → `"bab"` |
| 14:05 | 60 | ★ [Q11 · Maximum Product Subarray](../0152-maximum-product-subarray/NOTES.md) | Track both max **and** min ending here (a negative flips them); a zero resets | `[2,3,-2,4]` → 6, `[-2,3,-4]` → 24 |
| 15:05 | 10 | *Break* | | |
| 15:15 | 45 | **Pattern-recognition test:** read 10 scenarios from the course PDF in random order; students name the pattern and key insight for each (no coding) | | |
| 16:00 | 30 | **Wrap-up:** how to self-study with the repo ([START_HERE](../START_HERE.md) → roadmap → steps → NOTES), the 40–50 h plan below, Q&A | | |

---

## After the course: self-study plan (≈ 40–50 h)

| Week | Do |
| --- | --- |
| 1 | Prerequisite steps + re-solve: Arrays & Hashing, Two Pointers, Sliding Window |
| 2 | Stack, Binary Search, Linked List |
| 3 | Trees, Graphs |
| 4 | Advanced Graphs, 1-D DP · re-solve all 10 ★ anchors from memory |

Rule: re-solve each problem from scratch **3 days later** without looking. If you can't, it isn't learned yet.

## Summary

| Day | Patterns | ★ Anchors | Walkthroughs |
| --- | --- | --- | --- |
| 1 | Arrays & Hashing · Two Pointers · Sliding Window (start) | Q1, Q10 | Q8, Q7, Q6, Q2, Q9 |
| 2 | Sliding Window · Stack · Binary Search | Q4, Q15, Q3 | Q5, Q20, Q17, Q14, Q16 |
| 3 | Linked List · Trees · Graphs (start) | Q13, Q22, Q25 | Q18, Q19, Q24, Q23, Q21 |
| 4 | Graphs · Advanced Graphs · 1-D DP | Q29, Q11 | Q26, Q27, Q28, Q30, Q12 |
| **Total** | **10 patterns** | **10** | **20** |
