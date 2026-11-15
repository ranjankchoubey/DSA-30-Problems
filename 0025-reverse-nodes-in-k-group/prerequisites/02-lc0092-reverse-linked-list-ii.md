# Step 02 · 92. Reverse Linked List II (Medium)

[← Previous: Reverse Linked List](01-lc0206-reverse-linked-list.md) · [Roadmap](00-roadmap.md) · [Next: Swap Nodes in Pairs →](03-lc0024-swap-nodes-in-pairs.md)

**Practise:** [LeetCode 92](https://leetcode.com/problems/reverse-linked-list-ii/)
**Skill it adds:** reversing **part** of a list and **reconnecting** both ends, with a dummy node so the front needs no special case.

## Problem in brief

You get the head of a list and two 1-based positions `left ≤ right`. Reverse only the nodes from position `left` to position `right`, keep the rest in place, and return the head.

## Key idea

1. Put a `dummy` in front of `head`, so even `left = 1` has a node before it.
2. Walk `before` to the node at position `left − 1`. It stays fixed.
3. Run the step-01 loop for exactly `right − left + 1` nodes, starting at `before.next`.
4. Now `prev` is the new front of the reversed piece and `curr` is the first node after it. `before.next` still points at the piece's **old** first node, which is now its last.
5. Reconnect: `before.next.next = curr`, then `before.next = prev`.

```
[1, 2, 3, 4, 5], left=2, right=4
before = 1, reverse 2 → 3 → 4      prev = 4, curr = 5
reconnect: 2.next = 5, 1.next = 4  → 1 → 4 → 3 → 2 → 5
```

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [reversing a list](../../course/primers/06-linked-list.md#reversing-a-list) · [dummy node](../../course/primers/06-linked-list.md#dummy-node)
3. [LeetCode Editorial](https://leetcode.com/problems/reverse-linked-list-ii/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+reverse+linked+list+ii)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        dummy = ListNode(0, head)
        before = dummy
        for _ in range(left - 1):              # node just before position left
            before = before.next
        prev, curr = None, before.next
        for _ in range(right - left + 1):      # step 01, but only for this range
            nxt = curr.next
            curr.next = prev
            prev, curr = curr, nxt
        before.next.next = curr                # old first node is now the last
        before.next = prev                     # new first node
        return dummy.next
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `head = [1,2,3,4,5], left = 2, right = 4` | `[1,4,3,2,5]` |
| `head = [5], left = 1, right = 1` | `[5]` |
| `head = [3,5], left = 1, right = 2` | `[5,3]` (the head changes, so `dummy` is needed) |

## Pitfall

- Returning `head` instead of `dummy.next`. When `left = 1`, the old head is no longer first.
- Reconnecting in the wrong order. Use `before.next` (the old first node) **before** you overwrite it with `prev`.

## What changes in the next step

Here we reverse one range. The target reverses range after range, every `k` nodes, so it needs a loop over groups that remembers where the previous group ended. Step 03 practises that loop on the simplest case, `k = 2`.
