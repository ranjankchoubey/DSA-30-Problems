# DSA-30-Problems

Teaching repo for **30 case-based Data Structure interview problems** (Python).
Course material: [questions (Markdown)](course/1.%20Data%20Structure%20-%20Technical_Interview_Case_Questions_With_Test_Cases.md) · [questions (PDF)](course/1.%20Data%20Structure%20-%20Technical_Interview_Case_Questions_With_Test_Cases.pdf) · [LeetCode list](https://leetcode.com/problem-list/00allxmd/)

## Repo layout

```
DSA-30-Problems/
├── README.md                  # this file: course index (top) + LeetHub topics (bottom, auto)
├── course/                    # course question docs (md + pdf)
├── templates/NOTES_TEMPLATE.md
└── NNNN-problem-slug/          # folder name = LeetHub's name (4-digit number + LeetCode URL slug)
    ├── NNNN-problem-slug.py   # LeetHub (auto): your latest accepted submission
    ├── README.md              # LeetHub (auto): problem statement
    ├── NOTES.md               # handwritten: pattern, approaches, proof, dry run, test cases, follow-ups
    │                          #   (optimal solution is inside a collapsed "spoiler" block)
    └── prerequisites/         # handwritten ladder from basics up to this problem
        ├── 00-roadmap.md      # start here: goal, skills, step map, threads
        ├── 01-lcNNNN-slug.md  # a LeetCode problem step
        └── 02-concept-slug.md # a technique with no single matching problem
```

Course problem folders are created **before** you solve them (with `NOTES.md` + `prerequisites/`).
When you submit on LeetCode, LeetHub adds its `.py` and `README.md` to the **same** folder; the file names differ, so nothing clashes.

## Workflow per problem

1. Open `NNNN-slug/prerequisites/00-roadmap.md` and work through the steps in order.
   Practise steps in the **NeetCode editor** (syncs to the separate `neetcode-submissions` repo) or with LeetCode **Run**, not **Submit**.
2. Solve the course problem on **LeetCode** in Python and **Submit**. LeetHub-3.0 pushes `NNNN-slug.py` + `README.md` into the folder.
   Use the spoiler in `NOTES.md` only after a real attempt.
3. `git pull` (always pull first, because LeetHub pushes from the browser).
4. Mark the problem ✅ in the [Course Index](#course-index) and add anything you learned to `NOTES.md`.
5. `git add -A && git commit -m "notes: NNNN-slug" && git push`

**Adding a new problem:** `mkdir -p NNNN-slug/prerequisites && cp templates/NOTES_TEMPLATE.md NNNN-slug/NOTES.md`
(use exactly the LeetCode URL slug with a 4-digit number, e.g. `0015-3sum`).

**Rules**
- Don't edit LeetHub-owned files (`NNNN-slug.py`, the problem's `README.md`) or anything between the `LeetCode Topics` markers below. LeetHub overwrites them.
- Put alternative or brute-force code in `NOTES.md`. Resubmitting on LeetCode replaces the synced `.py`.
- 🔒 Premium problems can't be synced without LeetCode Premium. Create `NNNN-slug/NNNN-slug.py` manually with the same naming, or practise the listed free alternative.

## Course Index

| Q# | Case | LeetCode | Difficulty | NeetCode Pattern | Status | Study |
| --- | --- | --- | --- | --- | --- | --- |
| **Arrays & Strings** | | | | | | |
| Q1 | Fraud Pattern Detector | [560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/) | Medium | Arrays & Hashing | ⬜ | [prerequisites](0560-subarray-sum-equals-k/prerequisites/00-roadmap.md) · [notes](0560-subarray-sum-equals-k/NOTES.md) |
| Q2 | Flood Water Storage Planner | [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Hard | Two Pointers | ✅ | [notes](0042-trapping-rain-water/NOTES.md) |
| Q3 | Merging Two Sorted Sensor Feeds | [4. Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Hard | Binary Search | ✅ | [notes](0004-median-of-two-sorted-arrays/NOTES.md) |
| Q4 | Shortest Log Window | [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | Hard | Sliding Window | ⬜ | |
| Q5 | Peak Traffic Monitor | [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | Hard | Sliding Window | ⬜ | |
| Q6 | Login Session Analyzer | [128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/) | Medium | Arrays & Hashing | ⬜ | [prerequisites](0128-longest-consecutive-sequence/prerequisites/00-roadmap.md) · [notes](0128-longest-consecutive-sequence/NOTES.md) |
| Q7 | Revenue Contribution Without Division | [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/) | Medium | Arrays & Hashing | ⬜ | [prerequisites](0238-product-of-array-except-self/prerequisites/00-roadmap.md) · [notes](0238-product-of-array-except-self/NOTES.md) |
| Q8 | Corrupted Record Repair | [645. Set Mismatch](https://leetcode.com/problems/set-mismatch/) | Easy | Arrays & Hashing | ✅ | [prerequisites](0645-set-mismatch/prerequisites/00-roadmap.md) · [notes](0645-set-mismatch/NOTES.md) |
| Q9 | Limited-Variety Playlist | [340. Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) 🔒 | Medium | Sliding Window | ⬜ | free alt: [904](https://leetcode.com/problems/fruit-into-baskets/) (k = 2) |
| Q10 | Budget Triplets | [15. 3Sum](https://leetcode.com/problems/3sum/) | Medium | Two Pointers | ⬜ | |
| Q11 | Best Profit Window | [152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/) | Medium | 1-D DP | ⬜ | |
| Q12 | DNA Motif Finder | [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) | Medium | 1-D DP | ⬜ | |
| **Linked Lists, Stacks & Queues** | | | | | | |
| Q13 | Mobile App Cache | [146. LRU Cache](https://leetcode.com/problems/lru-cache/) | Medium | Linked List | ⬜ | |
| Q14 | Longest Valid Parentheses in a Compiler | [32. Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/) | Hard | Stack | ⬜ | |
| Q15 | Skyline Billboard | [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/) | Hard | Stack | ⬜ | |
| Q16 | Scientific Calculator Engine | [772. Basic Calculator III](https://leetcode.com/problems/basic-calculator-iii/) 🔒 | Hard | Stack | ⬜ | free alts: [224](https://leetcode.com/problems/basic-calculator/) + [227](https://leetcode.com/problems/basic-calculator-ii/) |
| Q17 | Circular Temperature Alerts | [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/) | Medium | Stack | ⬜ | |
| Q18 | Batch Reversal in Playlist | [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) | Hard | Linked List | ⬜ | |
| Q19 | Merging Sorted Log Files | [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) | Hard | Linked List | ⬜ | |
| Q20 | Undo Stack With Instant Minimum | [155. Min Stack](https://leetcode.com/problems/min-stack/) | Medium | Stack | ⬜ | |
| **Trees & Graphs** | | | | | | |
| Q21 | Saving a Decision Tree to Disk | [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | Hard | Trees | ⬜ | |
| Q22 | Most Profitable Route in an Org Tree | [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/) | Hard | Trees | ⬜ | |
| Q23 | Common Manager Finder | [1644. Lowest Common Ancestor of a Binary Tree II](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/) 🔒 | Medium | Trees | ⬜ | free alt: [236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) |
| Q24 | Rebuilding an Org Chart | [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | Medium | Trees | ⬜ | |
| Q25 | University Course Planner | [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) | Medium | Graphs | ⬜ | |
| Q26 | Spell-Correction Chain | [127. Word Ladder](https://leetcode.com/problems/word-ladder/) | Hard | Graphs | ⬜ | |
| Q27 | Growing Islands on a Map | [305. Number of Islands II](https://leetcode.com/problems/number-of-islands-ii/) 🔒 | Hard | Graphs (Union-Find) | ⬜ | free alts: [200](https://leetcode.com/problems/number-of-islands/) + [684](https://leetcode.com/problems/redundant-connection/) |
| Q28 | Budget Airline Router | [787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/) | Medium | Advanced Graphs | ⬜ | |
| Q29 | Signal Propagation in a Network | [743. Network Delay Time](https://leetcode.com/problems/network-delay-time/) | Medium | Advanced Graphs | ⬜ | |
| Q30 | Decoding an Alien Alphabet | [269. Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) 🔒 | Hard | Advanced Graphs | ⬜ | free alt: [NeetCode Foreign Dictionary](https://neetcode.io/problems/foreign-dictionary) |

## Teaching order by pattern

Follows the NeetCode roadmap, so each pattern builds on the previous one:

1. **Arrays & Hashing**: Q8 → Q7 → Q6 → Q1
2. **Two Pointers**: Q10 → Q2
3. **Sliding Window**: Q9 → Q4 → Q5
4. **Stack**: Q20 → Q17 → Q15 → Q14 → Q16
5. **Binary Search**: Q3
6. **Linked List**: Q13 → Q18 → Q19
7. **Trees**: Q24 → Q23 → Q22 → Q21
8. **Graphs**: Q25 → Q26 → Q27
9. **Advanced Graphs**: Q29 → Q28 → Q30
10. **1-D DP**: Q12 → Q11

Full step list for every ladder: [course/prerequisites-plan.md](course/prerequisites-plan.md).

## Threads (ideas that connect the 30 problems)

| Thread | Problems | Shared idea |
| --- | --- | --- |
| Prefix / suffix precomputation | Q1, Q2, Q7 | One pass from each side; combine left and right of `i` |
| Monotonic stack / deque | Q2 (stack approach), Q5, Q15, Q17 | Keep a stack/deque in sorted order; each element pushed and popped once |
| Hash map + another structure | Q1, Q6, Q13, Q24 | O(1) lookup combined with prefix sums, a set, a linked list, or index ranges |
| Heap | Q19, Q28, Q29 | Always process the current smallest item next |
| Topological sort | Q25 → Q30 | Order nodes by dependencies; a cycle means no valid order |
| Return one value, record the global answer | Q22, Q21, Q24 | A recursive helper returns one thing to its parent and updates a separate global result |
| Binary search / divide & conquer | Q3, Q19 | Throw away half the search space, or merge halves |
| Union-Find | Q27 | Merge connected parts as cells are added |

---

<!---LeetCode Topics Start-->
# LeetCode Topics
## Array
| Problem Name | Difficulty |
| ------- | ------- |
| [0004-median-of-two-sorted-arrays](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0004-median-of-two-sorted-arrays/) | Hard |
| [0042-trapping-rain-water](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0042-trapping-rain-water/) | Hard |
## Two Pointers
| Problem Name | Difficulty |
| ------- | ------- |
| [0042-trapping-rain-water](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0042-trapping-rain-water/) | Hard |
## Dynamic Programming
| Problem Name | Difficulty |
| ------- | ------- |
| [0042-trapping-rain-water](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0042-trapping-rain-water/) | Hard |
## Stack
| Problem Name | Difficulty |
| ------- | ------- |
| [0042-trapping-rain-water](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0042-trapping-rain-water/) | Hard |
## Monotonic Stack
| Problem Name | Difficulty |
| ------- | ------- |
| [0042-trapping-rain-water](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0042-trapping-rain-water/) | Hard |
## Binary Search
| Problem Name | Difficulty |
| ------- | ------- |
| [0004-median-of-two-sorted-arrays](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0004-median-of-two-sorted-arrays/) | Hard |
## Divide and Conquer
| Problem Name | Difficulty |
| ------- | ------- |
| [0004-median-of-two-sorted-arrays](https://github.com/ranjankchoubey/DSA-30-Problems/tree/main/0004-median-of-two-sorted-arrays/) | Hard |
<!---LeetCode Topics End-->