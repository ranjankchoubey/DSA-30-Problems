# Step 01 · 206. Reverse Linked List (recap)

[← Roadmap](00-roadmap.md) · [Next: Reverse Linked List II →](02-lc0092-reverse-linked-list-ii.md)

> ↩ **Taught in full in the Q13 ladder:** [LRU Cache → Step 01 · Reverse Linked List](../../0146-lru-cache/prerequisites/01-lc0206-reverse-linked-list.md)

> 📚 **New to this?** Primer: [reversing a list](../../course/primers/06-linked-list.md#reversing-a-list)

## Recap

- Three pointers: `prev` (reversed part), `curr` (node being moved), `nxt` (saved rest of the list).
- Each step: `nxt = curr.next`, `curr.next = prev`, `prev = curr`, `curr = nxt`. O(n) time, O(1) space.
- When the loop ends, `prev` is the new head and `curr` is whatever came **after** the reversed part.

## Why it matters here

Reverse Nodes in k-Group runs this exact loop once per group, but for only `k` steps. After those `k` steps, `prev` is the group's new first node and `curr` is the first node of the **next** group. Those are the two ends you need to reconnect.
