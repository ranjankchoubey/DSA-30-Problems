# Step 01 · 206. Reverse Linked List (Easy)

[← Roadmap](00-roadmap.md) · [Next: Design Linked List →](02-lc0707-design-linked-list.md)

**Practise:** [LeetCode 206](https://leetcode.com/problems/reverse-linked-list/)
**Skill it adds:** **pointer manipulation**. Change `next` links one at a time without losing the rest of the list.

## Problem in brief

You get the head of a singly linked list. Relink the same nodes so they run in the opposite order, and return the new head.

## Key idea

Walk the list once with three pointers:

- `prev`: the head of the part that is already reversed (starts as `None`)
- `curr`: the node you are moving now
- `nxt`: `curr.next`, **saved first**, because the next line overwrites it

Each step does four things: save `nxt`, point `curr.next` back at `prev`, then move `prev` and `curr` one node forward.

```
start:          None    1 → 2 → 3 → None        prev=None, curr=1
after node 1:   None ← 1    2 → 3 → None        prev=1,    curr=2
after node 2:   None ← 1 ← 2    3 → None        prev=2,    curr=3
after node 3:   None ← 1 ← 2 ← 3    None        prev=3,    curr=None
```

When `curr` becomes `None`, every pointer has been flipped and `prev` is the old tail, which is the new head.

There is also a recursive version: reverse everything after `head`, then hook `head` on at the end. It is shorter, but it uses one stack frame per node.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [reversing a list](../../course/primers/06-linked-list.md#reversing-a-list)
3. [LeetCode Editorial](https://leetcode.com/problems/reverse-linked-list/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+reverse+linked+list)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

Iterative (the one to learn):

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        prev, curr = None, head
        while curr:
            nxt = curr.next          # save the rest of the list
            curr.next = prev         # flip one pointer
            prev = curr
            curr = nxt
        return prev
```

Recursive:

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        new_head = self.reverseList(head.next)   # reverse 2..n
        head.next.next = head                    # old second node now points back at head
        head.next = None                         # head is the new tail
        return new_head
```

</details>

**Time:** O(n) · **Space:** O(1) iterative, O(n) recursive (one stack frame per node)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,3,4,5]` | `[5,4,3,2,1]` |
| `[1,2]` | `[2,1]` |
| `[]` | `[]` |

## Pitfall

- Writing `curr.next = prev` **before** saving `curr.next` loses the rest of the list.
- Returning `curr` (which is `None` at the end) instead of `prev`.
- In the recursive version, forgetting `head.next = None` leaves a two-node cycle at the end.

## What changes in the next step

Each node here has only a `next` pointer, so removing a node from the middle needs its predecessor, which means walking from the head (O(n)). An LRU cache must pull **any** node out of the list in O(1). Step 02 adds a `prev` pointer to every node and dummy nodes at both ends.
