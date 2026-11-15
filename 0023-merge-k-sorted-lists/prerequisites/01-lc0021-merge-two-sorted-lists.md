# Step 01 · 21. Merge Two Sorted Lists (Easy)

[← Roadmap](00-roadmap.md) · [Next: Kth Largest Element in a Stream →](02-lc0703-kth-largest-element-in-a-stream.md)

**Practise:** [LeetCode 21](https://leetcode.com/problems/merge-two-sorted-lists/)
**Skill it adds:** **merging two sorted lists** by relinking nodes, with a dummy node and a `tail` pointer.

## Problem in brief

You get the heads of two lists, each sorted in non-decreasing order. Splice their nodes together into one sorted list and return its head.

## Key idea

Start a new list at a `dummy` node and keep `tail` at its last node. While both lists have nodes, compare their heads, attach the smaller one after `tail`, and advance that list and `tail`. When one list runs out, the other is already sorted, so attach the rest in one step.

```
list1: 1 → 2 → 4      list2: 1 → 3 → 4
dummy → 1 → 1 → 2 → 3 → 4 → 4
```

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [dummy node](../../course/primers/06-linked-list.md#dummy-node)
3. [LeetCode Editorial](https://leetcode.com/problems/merge-two-sorted-lists/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+merge+two+sorted+lists)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode()
        tail = dummy
        while list1 and list2:
            if list1.val <= list2.val:
                tail.next = list1
                list1 = list1.next
            else:
                tail.next = list2
                list2 = list2.next
            tail = tail.next
        tail.next = list1 or list2             # attach whatever is left
        return dummy.next
```

</details>

**Time:** O(n + m) · **Space:** O(1) (nodes are reused, not copied)

## Test cases

| Input | Expected |
| --- | --- |
| `list1 = [1,2,4], list2 = [1,3,4]` | `[1,1,2,3,4,4]` |
| `list1 = [], list2 = []` | `[]` |
| `list1 = [], list2 = [0]` | `[0]` |

## Pitfall

- Forgetting the leftover nodes after the loop (`tail.next = list1 or list2`).
- Forgetting to move `tail` forward, which overwrites the same `next` again and again.

## What changes in the next step

With `k` lists you could compare all `k` heads to find the smallest, but that costs O(k) per node, O(N·k) in total. A **heap** finds the smallest of `k` items in O(log k). Step 02 is the first full lesson on `heapq`.
