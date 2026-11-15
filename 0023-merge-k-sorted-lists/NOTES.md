# 0023. Merge k Sorted Lists

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q19 | [23](https://leetcode.com/problems/merge-k-sorted-lists/) | Hard | Linked List | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0023-merge-k-sorted-lists.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Merging Sorted Log Files:** a log aggregator gets `k` sorted linked lists of timestamps, one per server, and must produce one sorted list. Implement it with a **min-heap** and again with **divide and conquer**, compare them, and say which is better when `k` is very large and the lists are short. Constraints: 0 ≤ k ≤ 10,000; total nodes N ≤ 100,000.

## 2. Pattern recognition

- **Pattern:** Linked List + Heap / divide and conquer
- **Signals in the statement:** "k sorted lists", "one sorted list". At every moment the next output is the smallest of the k current heads, so a min-heap fits. Merging sorted lists in pairs is the merge half of merge sort, so divide and conquer fits too.
- **Core idea in one line:** pick the smallest of k heads in O(log k) with a heap, or merge in pairs so each node is moved only log k times. Both are O(N log k).

## 3. Approaches

### Approach 1: Collect, sort, rebuild

Put every value in an array, sort it, build a new list. Ignores the fact that the lists are already sorted. **Time:** O(N log N) · **Space:** O(N)

### Approach 2: Merge one list at a time

`result = merge(result, lists[i])` for each `i` (step 01). The result keeps growing, so early nodes are walked again in every merge. **Time:** O(N·k) in the worst case · **Space:** O(1)

### Approach 3: Min-heap of the k heads (optimal)

Push `(val, i, node)` for each non-empty list. Repeatedly pop the smallest, attach it to the answer, and push its `next`. Each of the N nodes is pushed and popped once, and the heap never holds more than k items. **Time:** O(N log k) · **Space:** O(k) for the heap

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
import heapq

class Solution:
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        heap = [(node.val, i, node) for i, node in enumerate(lists) if node]
        heapq.heapify(heap)                    # O(k)
        dummy = tail = ListNode()
        while heap:
            val, i, node = heapq.heappop(heap) # smallest current head
            tail.next = node
            tail = node
            if node.next:
                heapq.heappush(heap, (node.next.val, i, node.next))
        return dummy.next
```

</details>

### Approach 4: Divide and conquer, merging in pairs (also optimal)

Round 1 merges lists `0+1`, `2+3`, …; round 2 merges the results in pairs, and so on, until one list remains. There are ⌈log₂ k⌉ rounds, and each round moves every node at most once. Done bottom-up in the input array, it needs no recursion. **Time:** O(N log k) · **Space:** O(1) extra (O(log k) if written recursively)

<details>
<summary><b>Show the divide-and-conquer solution</b></summary>

```python
class Solution:
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists:
            return None
        step = 1
        while step < len(lists):               # each round halves the number of lists
            for i in range(0, len(lists) - step, step * 2):
                lists[i] = self.mergeTwo(lists[i], lists[i + step])
            step *= 2
        return lists[0]

    def mergeTwo(self, a: Optional[ListNode], b: Optional[ListNode]) -> Optional[ListNode]:
        dummy = tail = ListNode()              # step 01
        while a and b:
            if a.val <= b.val:
                tail.next, a = a, a.next
            else:
                tail.next, b = b, b.next
            tail = tail.next
        tail.next = a or b
        return dummy.next
```

</details>

### Comparison

| Approach | Time | Extra space | Notes |
| --- | --- | --- | --- |
| Collect, sort, rebuild | O(N log N) | O(N) | ignores the sorted input |
| One at a time | O(N·k) | O(1) | too slow for k = 10,000 |
| **Min-heap** | **O(N log k)** | **O(k)** | streams output; works on iterators and files |
| **Divide and conquer** | **O(N log k)** | **O(1)** iterative · O(log k) recursive | relinks in place; needs all lists up front |

## 4. Why it works

- **Heap:** the next node of the merged output must be the smallest of the k current heads, because every list is sorted, so nothing behind a head can be smaller than it. The heap holds exactly those heads, so `heappop` always gives the right next node. The index `i` breaks ties so Python never compares two `ListNode`s (each list has at most one node in the heap, so `(val, i)` is unique).
- **Divide and conquer:** merging two sorted lists gives a sorted list (step 01). After round r, each surviving list is the merge of up to 2ʳ original lists. After ⌈log₂ k⌉ rounds only `lists[0]` remains, and it holds all N nodes in order. In each round, each node is in at most one merge, so a round costs O(N).

## 5. Dry run (heap): TC 1, `lists = [[1,4,5],[1,3,4],[2,6]]`

Heap entries are shown as `value(list index)`, sorted for readability.

| Pop | Output so far | Push | Heap after |
| --- | --- | --- | --- |
| (start) | | 1(0), 1(1), 2(2) | 1(0), 1(1), 2(2) |
| 1(0) | 1 | 4(0) | 1(1), 2(2), 4(0) |
| 1(1) | 1, 1 | 3(1) | 2(2), 3(1), 4(0) |
| 2(2) | 1, 1, 2 | 6(2) | 3(1), 4(0), 6(2) |
| 3(1) | 1, 1, 2, 3 | 4(1) | 4(0), 4(1), 6(2) |
| 4(0) | 1, 1, 2, 3, 4 | 5(0) | 4(1), 5(0), 6(2) |
| 4(1) | 1, 1, 2, 3, 4, 4 | — (list 1 done) | 5(0), 6(2) |
| 5(0) | 1, 1, 2, 3, 4, 4, 5 | — (list 0 done) | 6(2) |
| 6(2) | **1, 1, 2, 3, 4, 4, 5, 6** | — (list 2 done) | empty |

Ties (`1(0)` vs `1(1)`, `4(0)` vs `4(1)`) are broken by the list index.

## 6. Edge cases & pitfalls

- **`lists = []`** (TC 2): return `None`. The heap starts empty; the D&C version checks `if not lists`.
- **Empty lists inside** (TC 3 `[[], [1]]`): skip `None` heads when building the heap. `mergeTwo` already handles `None`.
- Pushing `(node.val, node)` without the index raises `TypeError` when two values tie, because `ListNode` has no `<`.
- The D&C version writes into the `lists` array. Copy it first (`lists = list(lists)`) if the caller still needs it.
- Approach 2 looks like D&C but is O(N·k): always merge lists of **similar** size.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `lists = [[1,4,5],[1,3,4],[2,6]]` | `[1,1,2,3,4,4,5,6]` |
| TC 2 | `lists = []` | `[]` |
| TC 3 | `lists = [[], [1]]` | `[1]` |

## 8. Follow-up questions (from course doc)

- **Q: Implement it with a min-heap and again with divide and conquer.**
  **A:** Approaches 3 and 4 in §3.
- **Q: Compare the time complexity and space of both.**
  **A:** Both are **O(N log k)** time. The heap does one push and one pop of O(log k) per node. Divide and conquer has log k rounds, each moving every node at most once. Space differs: the heap holds up to **k** entries, **O(k)**. Bottom-up divide and conquer relinks nodes in place with a few pointers, **O(1)** extra (O(log k) stack if written recursively). See the comparison table in §3.
- **Q: Which is better when k is very large and the lists are short?**
  **A:** **Divide and conquer.** The time is the same O(N log k), but the heap must hold one entry per list, so with k = 10,000 lists of 1–2 nodes the heap is about as big as the data itself, while bottom-up divide and conquer needs O(1) extra memory. Two honest caveats:
  - In CPython the heap is not slower here, because `heapq` is written in C. In a quick test with k = 10,000 lists of 1–10 nodes it was about 20–40% faster. So the win is memory, not speed. In languages where both are compiled, the plain pointer merges usually have the lower constant factor.
  - When lists are that short, N ≈ k, so log k ≈ log N. Simply collecting all nodes, sorting them by value and relinking is also O(N log N) and was about 3× faster than both in Python, at the cost of O(N) extra space.

  The heap is the better choice in the opposite case: few, **long** or **streaming** lists (e.g. huge log files read from disk). It needs only the k current heads in memory, emits the first output immediately, and works on iterators that can't be relinked.

## 9. Threads

- **Q29** Network Delay Time and **Q28** Cheapest Flights: the same "pop the smallest, push its neighbours" heap loop (Dijkstra).
- **Q3** Median of Two Sorted Arrays: divide and conquer on sorted data.
- **Q13** LRU Cache and **Q18** Reverse Nodes in k-Group: dummy nodes and pointer relinking.

## 10. One-line revision

> Heap of (val, i, node) for the k heads: pop smallest, append, push its next, O(N log k) time and O(k) space; or merge lists in pairs for log k rounds, O(N log k) time and O(1) space.

<!-- course-nav:start -->

---

### Course navigation

**Problem 18 of 30** · Linked List (3 of 3)

[← Previous: Q18 · Reverse Nodes in k-Group](../0025-reverse-nodes-in-k-group/NOTES.md) · [Course index](../INDEX.md) · [Next: Q24 · Construct Binary Tree from Preorder and Inorder Traversal →](../0105-construct-binary-tree-from-preorder-and-inorder-traversal/prerequisites/00-roadmap.md)

> 🧭 **Next pattern: Trees.** Read [07 · Trees primer](../course/primers/07-trees.md) before starting Q24.

<!-- course-nav:end -->
