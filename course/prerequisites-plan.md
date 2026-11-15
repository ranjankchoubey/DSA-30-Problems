# Prerequisites Plan (DRAFT for review)

Each course problem folder will get a `prerequisites/` folder that walks from basics up to that problem.
**Status:** ✅ 1. Arrays & Hashing built (pilot: Q8, Q7, Q6, Q1). Other sections are still a plan. Edit this file freely: add, remove or reorder steps, then approve.

## Conventions

- **Folder:** `NNNN-slug/prerequisites/` inside the course problem folder. LeetHub never touches it.
- **Files:** `NN-type-slug.md`
  - `00-roadmap.md` (always): the goal, a skill checklist, the step map, and threads to other course problems
  - `lcNNNN`: a LeetCode problem step · `concept`: a technique with no single matching problem
- **One new idea per step.** The last step should be one idea away from the target.
  The ladder **ends at the target**; follow-ups from the course doc stay in the target's `NOTES.md` §8.
- **Size varies with difficulty:** Easy ≈ 3, Medium ≈ 3–4, Hard ≈ 3–5 steps.
- **↩ Recap step:** already taught in full in an earlier ladder. The file holds a 3-line recap plus a link to the full version.
- **Where to practise:** NeetCode editor (syncs to `neetcode-submissions`) or LeetCode **Run**, not **Submit**. The final code goes into the `.md` file.
- **Step file layout:** skill added · problem in brief · key idea · Python solution + complexity · test cases · "what changes in the next step"

Order below = teaching order (NeetCode roadmap). 🔒 = LeetCode Premium target.

---

## 1. Arrays & Hashing

### Q8 · 0645 Set Mismatch (Easy)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0217-contains-duplicate.md` | Hash set membership to detect a duplicate |
| 02 | `02-lc0268-missing-number.md` | Sum formula / XOR to find the missing value |
| 03 | `03-lc0448-find-all-numbers-disappeared-in-an-array.md` | Index marking (negate `nums[x-1]`) for O(1) space |

### Q7 · 0238 Product of Array Except Self (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc1480-running-sum-of-1d-array.md` | Prefix accumulation |
| 02 | `02-lc0724-find-pivot-index.md` | "Everything left of i" vs "everything right of i" |
| 03 | `03-concept-prefix-and-suffix-product-arrays.md` | Swap + for ×, run two passes into the output array for O(1) extra space |

### Q6 · 0128 Longest Consecutive Sequence (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0217-contains-duplicate.md` ↩ Q8 | Hash set membership |
| 02 | `02-lc0485-max-consecutive-ones.md` | Counting the longest run in one scan |
| 03 | `03-concept-sort-then-count-runs.md` | O(n log n) baseline (handles duplicates) |
| 04 | `04-concept-naive-hash-set-walk.md` | Hash-set walk from every value: O(n²) worst case (the target adds the `x-1 not in set` check) |

### Q1 · 0560 Subarray Sum Equals K (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0001-two-sum.md` | Complement lookup in a hash map |
| 02 | `02-lc0303-range-sum-query-immutable.md` | `sum(i..j) = P[j+1] − P[i]` |
| 03 | `03-concept-subarray-sum-as-prefix-difference.md` | O(n²) count using prefix pairs, and why a sliding window fails with negative numbers |

## 2. Two Pointers

### Q10 · 0015 3Sum (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0001-two-sum.md` ↩ Q1 | Complement idea |
| 02 | `02-lc0167-two-sum-ii-input-array-is-sorted.md` | Two pointers on a sorted array |
| 03 | `03-lc0026-remove-duplicates-from-sorted-array.md` | Skipping duplicates in a sorted array |
| 04 | `04-concept-all-unique-pairs-two-sum-ii.md` | All unique pairs with inner-level dedupe (the target adds the outer loop) |

### Q2 · 0042 Trapping Rain Water (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0724-find-pivot-index.md` ↩ Q7 | Left-side vs right-side aggregates |
| 02 | `02-lc0011-container-with-most-water.md` | Pointers at both ends; move the shorter side |
| 03 | `03-concept-water-at-index.md` | `min(maxL, maxR) − h[i]`, brute force O(n²) |
| 04 | `04-concept-prefix-suffix-max-arrays.md` | O(n) time / O(n) space (the target removes the arrays) |

## 3. Sliding Window

### Q9 · 0340 Longest Substring with At Most K Distinct 🔒 (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0643-maximum-average-subarray-i.md` | Fixed-size window |
| 02 | `02-lc0003-longest-substring-without-repeating-characters.md` | Variable window: expand, then shrink while invalid |
| 03 | `03-lc0904-fruit-into-baskets.md` | At most **2** distinct, i.e. the target with k = 2 |

### Q4 · 0076 Minimum Window Substring (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0003-longest-substring-without-repeating-characters.md` ↩ Q9 | Variable window |
| 02 | `02-lc0209-minimum-size-subarray-sum.md` | Shrink **while valid** to find a minimum |
| 03 | `03-lc0242-valid-anagram.md` | Character frequency maps |
| 04 | `04-lc0438-find-all-anagrams-in-a-string.md` | Window plus frequency matching |
| 05 | `05-concept-have-vs-need-counter.md` | `formed == required` gives an O(1) validity check |

### Q5 · 0239 Sliding Window Maximum (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0643-maximum-average-subarray-i.md` ↩ Q9 | Fixed-size window |
| 02 | `02-concept-brute-force-and-heap.md` | O(nk), then a heap with lazy deletion O(n log n) |
| 03 | `03-lc0739-daily-temperatures.md` | Monotonic stack (first full lesson) |
| 04 | `04-concept-monotonic-deque.md` | Pop from the back (smaller values) and the front (out of window) |

## 4. Stack

### Q20 · 0155 Min Stack (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0020-valid-parentheses.md` | Stack basics |
| 02 | `02-lc0682-baseball-game.md` | Stack operations that carry extra state |
| 03 | `03-concept-store-min-with-each-push.md` | `(value, min_so_far)` pairs |

### Q17 · 0503 Next Greater Element II (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0739-daily-temperatures.md` ↩ Q5 | Monotonic stack |
| 02 | `02-lc0496-next-greater-element-i.md` | Next greater element + hash map |
| 03 | `03-concept-circular-array-iterate-2n.md` | `i % n` and a two-pass loop |

### Q15 · 0084 Largest Rectangle in Histogram (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0739-daily-temperatures.md` ↩ Q5 | Monotonic stack |
| 02 | `02-concept-expand-each-bar.md` | Brute force O(n²): each bar as the shortest bar |
| 03 | `03-concept-previous-and-next-smaller-element.md` | Previous/next smaller element in two passes; `area = h × (next − prev − 1)` |

### Q14 · 0032 Longest Valid Parentheses (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0020-valid-parentheses.md` ↩ Q20 | Bracket matching |
| 02 | `02-lc0921-minimum-add-to-make-parentheses-valid.md` | Balance counter (leads to the O(1) two-pass method) |
| 03 | `03-lc1249-minimum-remove-to-make-valid-parentheses.md` | Stack of **indices** to find where valid runs break |

### Q16 · 0772 Basic Calculator III 🔒 (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0150-evaluate-reverse-polish-notation.md` | Evaluating with a stack |
| 02 | `02-lc0227-basic-calculator-ii.md` | `* /` precedence (no parentheses); truncation toward zero |
| 03 | `03-lc0224-basic-calculator.md` | Parentheses with `+ -` (push the context onto the stack) |
| 04 | `04-concept-two-stacks-operator-precedence.md` | Operand and operator stacks, no recursion |

## 5. Binary Search

### Q3 · 0004 Median of Two Sorted Arrays (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0704-binary-search.md` | Classic binary search, loop invariants |
| 02 | `02-lc0278-first-bad-version.md` | Binary search on a true/false condition |
| 03 | `03-lc0088-merge-sorted-array.md` | Merging two sorted arrays (the O(m+n) baseline) |
| 04 | `04-concept-kth-smallest-of-two-sorted-arrays.md` | Discard k/2 each step (also answers the two-machines follow-up) |

## 6. Linked List

### Q13 · 0146 LRU Cache (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0206-reverse-linked-list.md` | Pointer manipulation basics |
| 02 | `02-lc0707-design-linked-list.md` | Doubly linked list with dummy head/tail nodes |
| 03 | `03-concept-hash-map-to-node.md` | O(1) lookup plus O(1) unlink |

### Q18 · 0025 Reverse Nodes in k-Group (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0206-reverse-linked-list.md` ↩ Q13 | Reversal |
| 02 | `02-lc0092-reverse-linked-list-ii.md` | Reverse a sub-list and reconnect it, using a dummy node |
| 03 | `03-lc0024-swap-nodes-in-pairs.md` | The k = 2 case |

### Q19 · 0023 Merge k Sorted Lists (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0021-merge-two-sorted-lists.md` | Merging two lists |
| 02 | `02-lc0703-kth-largest-element-in-a-stream.md` | Heap basics with `heapq` (first full lesson) |
| 03 | `03-lc0148-sort-list.md` | Merge sort on a list, i.e. divide-and-conquer merging |

## 7. Trees

### Q24 · 0105 Construct Binary Tree from Preorder and Inorder (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0144-lc0094-preorder-and-inorder-traversal.md` | Traversal orders (first full lesson) |
| 02 | `02-lc0104-maximum-depth-of-binary-tree.md` | Recursion on trees (first full lesson) |
| 03 | `03-lc0108-convert-sorted-array-to-bst.md` | Building a tree recursively from index ranges |

### Q23 · 1644 Lowest Common Ancestor of a Binary Tree II 🔒 (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0235-lowest-common-ancestor-of-a-bst.md` | LCA using the BST ordering |
| 02 | `02-lc0236-lowest-common-ancestor-of-a-binary-tree.md` | Classic LCA (assumes both nodes exist) |
| 03 | `03-concept-postorder-count-found-nodes.md` | Full traversal that counts found nodes, to verify both exist |

### Q22 · 0124 Binary Tree Maximum Path Sum (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0104-maximum-depth-of-binary-tree.md` ↩ Q24 | Tree recursion |
| 02 | `02-lc0112-path-sum.md` | Carrying sums along a path |
| 03 | `03-lc0543-diameter-of-binary-tree.md` | Return one value to the parent, record the global answer |
| 04 | `04-concept-drop-negative-branches.md` | `max(0, gain)` |

### Q21 · 0297 Serialize and Deserialize Binary Tree (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0144-lc0094-preorder-and-inorder-traversal.md` ↩ Q24 | Traversals |
| 02 | `02-lc0102-binary-tree-level-order-traversal.md` | BFS on a tree (also the test-case input format) |
| 03 | `03-lc0606-construct-string-from-binary-tree.md` | Tree to string, with markers for missing children |

## 8. Graphs

### Q25 · 0210 Course Schedule II (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-concept-adjacency-list.md` | Edge list to adjacency list; direction matters |
| 02 | `02-lc0997-find-the-town-judge.md` | In-degree and out-degree |
| 03 | `03-lc0207-course-schedule.md` | Cycle detection (Kahn's algorithm and 3-colour DFS) |

### Q26 · 0127 Word Ladder (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc1091-shortest-path-in-binary-matrix.md` | BFS level by level = shortest path on unweighted graphs |
| 02 | `02-lc0433-minimum-genetic-mutation.md` | A small version of Word Ladder |
| 03 | `03-concept-wildcard-buckets.md` | `h*t` buckets for fast neighbour lookup |

### Q27 · 0305 Number of Islands II 🔒 (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0200-number-of-islands.md` | Grid connectivity with DFS/BFS |
| 02 | `02-lc0547-number-of-provinces.md` | Introduction to Union-Find |
| 03 | `03-lc0684-redundant-connection.md` | Path compression + union by rank; `union()` returns whether it merged |

## 9. Advanced Graphs

### Q29 · 0743 Network Delay Time (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0703-kth-largest-element-in-a-stream.md` ↩ Q19 | Heap |
| 02 | `02-lc1091-shortest-path-in-binary-matrix.md` ↩ Q26 | BFS shortest path |
| 03 | `03-concept-why-bfs-fails-with-weights.md` | Leads into Dijkstra; what breaks with negative edges |

### Q28 · 0787 Cheapest Flights Within K Stops (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-q29-network-delay-time.md` ↩ Q29 | Dijkstra |
| 02 | `02-concept-bellman-ford-k-rounds.md` | Edge relaxation; copy the array each round to limit edge count |
| 03 | `03-concept-state-graph-city-stops.md` | Dijkstra over `(city, stops)` states |

### Q30 · 0269 Alien Dictionary 🔒 (Hard)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-q25-course-schedule-ii.md` ↩ Q25 | Topological sort |
| 02 | `02-lc0953-verifying-an-alien-dictionary.md` | Comparing adjacent words under a custom order |
| 03 | `03-concept-first-difference-gives-edge.md` | Building edges, plus the invalid prefix case |

## 10. 1-D DP

### Q12 · 0005 Longest Palindromic Substring (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0125-valid-palindrome.md` | Two-pointer palindrome check |
| 02 | `02-lc0647-palindromic-substrings.md` | Expand around odd and even centres |
| 03 | `03-concept-dp-is-palindrome-table.md` | The `dp[i][j]` alternative (why NeetCode files it under DP) |

### Q11 · 0152 Maximum Product Subarray (Medium)
| # | File | Skill it adds |
| --- | --- | --- |
| 01 | `01-lc0070-climbing-stairs.md` | DP state and recurrence (first full lesson) |
| 02 | `02-lc0053-maximum-subarray.md` | Kadane: best value ending at i |
| 03 | `03-lc1567-maximum-length-of-subarray-with-positive-product.md` | Track positive and negative together (one step from the target) |

---

## Totals

- 30 ladders, **99 step files** (84 written in full + 15 recaps) + 30 `00-roadmap.md`
- All prerequisite problems are free on LeetCode. Premium appears only at 5 targets: Q9, Q16, Q23, Q27, Q30.

## Threads (shown in each `00-roadmap.md` and the README)

- **Prefix/suffix precomputation:** Q1, Q2, Q7
- **Monotonic stack/deque:** Q2 (stack approach), Q5, Q15, Q17
- **Hash map + another structure:** Q1, Q6, Q13, Q24
- **Heap:** Q19, Q28, Q29
- **Topological sort:** Q25 → Q30
- **Recursion returns one value, records the global answer:** Q22, Q21, Q24
- **Binary search / divide & conquer:** Q3, Q19
- **Union-Find:** Q27
