# Step 03 · 148. Sort List (Medium)

[← Previous: Kth Largest Element in a Stream](02-lc0703-kth-largest-element-in-a-stream.md) · [Roadmap](00-roadmap.md) · [Target: Merge k Sorted Lists →](../NOTES.md)

**Practise:** [LeetCode 148](https://leetcode.com/problems/sort-list/)
**Skill it adds:** **merge sort on a linked list**, i.e. divide and conquer: split in half, sort each half, merge.

## Problem in brief

Sort a linked list in ascending order and return its head. Aim for O(n log n) time.

## Key idea

Merge sort fits linked lists well, because merging needs no extra array (step 01).

1. **Split:** find the middle with fast and slow pointers and cut the list there (`slow.next = None`).
2. **Conquer:** sort each half recursively. A list of 0 or 1 nodes is already sorted.
3. **Merge:** merge the two sorted halves with step 01.

There are about log n levels of splitting, and at each level the merges touch every node once, so the total is O(n log n).

```
[4,2,1,3] → [4,2] + [1,3] → [4]+[2] , [1]+[3]
merge up:  [2,4] , [1,3] → [1,2,3,4]
```

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [fast and slow pointers](../../course/primers/06-linked-list.md#fast-and-slow-pointers) · [dummy node](../../course/primers/06-linked-list.md#dummy-node)
3. [LeetCode Editorial](https://leetcode.com/problems/sort-list/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+sort+list)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def sortList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        slow, fast = head, head.next           # slow stops at the end of the left half
        while fast and fast.next:
            slow, fast = slow.next, fast.next.next
        right = slow.next
        slow.next = None                       # cut the list in two
        return self.merge(self.sortList(head), self.sortList(right))

    def merge(self, a: Optional[ListNode], b: Optional[ListNode]) -> Optional[ListNode]:
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

**Time:** O(n log n) · **Space:** O(log n) for the recursion stack

## Test cases

| Input | Expected |
| --- | --- |
| `[4,2,1,3]` | `[1,2,3,4]` |
| `[-1,5,3,4,0]` | `[-1,0,3,4,5]` |
| `[]` | `[]` |

## Pitfall

- Starting `fast` at `head` instead of `head.next`. For a 2-node list, `slow` would then stop on the second node and the split would never shrink, which recurses forever.
- Forgetting `slow.next = None`, so the "left half" is still the whole list.

## What changes in the target

Sort List starts from **one** unsorted list and splits it all the way down to single nodes. Merge k Sorted Lists starts with **k lists that are already sorted**, so only the merging half is needed: merge lists `0+1`, `2+3`, …, then merge those results in pairs, and so on. That's log k rounds, and each round touches all N nodes at most once: **O(N log k)** → [NOTES](../NOTES.md).
