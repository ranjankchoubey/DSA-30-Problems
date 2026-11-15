# Step 03 · 24. Swap Nodes in Pairs (Medium)

[← Previous: Reverse Linked List II](02-lc0092-reverse-linked-list-ii.md) · [Roadmap](00-roadmap.md) · [Target: Reverse Nodes in k-Group →](../NOTES.md)

**Practise:** [LeetCode 24](https://leetcode.com/problems/swap-nodes-in-pairs/)
**Skill it adds:** the **k = 2 case**. Process the list group by group, keeping a pointer to the node just before the current group.

## Problem in brief

Swap every two adjacent nodes and return the new head. If one node is left over at the end, it stays where it is. Relink nodes; don't swap values.

## Key idea

Keep `prev` = the node just **before** the current pair (start at a `dummy`). While a full pair exists after `prev`:

1. `first, second = prev.next, prev.next.next`
2. `first.next = second.next` (first will point past the pair)
3. `second.next = first`
4. `prev.next = second` (connect the previous part to the new front)
5. `prev = first`, because `first` is now the pair's **last** node, i.e. the node before the next pair

```
dummy → 1 → 2 → 3 → 4          prev = dummy
dummy → 2 → 1 → 3 → 4          prev = 1
dummy → 2 → 1 → 4 → 3          prev = 3, no pair left
```

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [dummy node](../../course/primers/06-linked-list.md#dummy-node)
3. [LeetCode Editorial](https://leetcode.com/problems/swap-nodes-in-pairs/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+swap+nodes+in+pairs)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(0, head)
        prev = dummy                           # node just before the current pair
        while prev.next and prev.next.next:    # a full pair exists
            first, second = prev.next, prev.next.next
            first.next = second.next
            second.next = first
            prev.next = second
            prev = first                       # first is now the pair's last node
        return dummy.next
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,3,4]` | `[2,1,4,3]` |
| `[]` | `[]` |
| `[1,2,3]` | `[2,1,3]` (the leftover node stays) |

## Pitfall

- Moving `prev` to `second` instead of `first`. After the swap, `first` is the node at the **end** of the pair.
- Checking only `prev.next`: a single leftover node would then crash on `prev.next.next.next`.

## What changes in the target

`k` is no longer 2, so three things grow:

1. "Does a full group exist?" becomes "walk `k` steps and check none of them is `None`".
2. The three-pointer swap becomes the step-01/02 reversal of exactly `k` nodes.
3. `prev` still jumps to the group's old first node, which is now its last → [NOTES](../NOTES.md).
