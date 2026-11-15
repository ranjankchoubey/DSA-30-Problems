# Roadmap → Q18 · 25. Reverse Nodes in k-Group (Hard)

**Goal:** reverse a singly linked list in consecutive groups of `k` nodes. If the last group has fewer than `k` nodes, leave it as it is. Only relink nodes (never change values), with **O(1)** extra space.

> 📚 **Before you start:** read [Primer 06 · Linked List](../../course/primers/06-linked-list.md).

## Skills you need

- [ ] Reverse a whole list with `prev / curr / nxt`
- [ ] Reverse just part of a list and reconnect both ends, using a dummy node
- [ ] Repeat a group operation along the list, keeping a pointer to the node before each group

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Reverse Linked List](01-lc0206-reverse-linked-list.md) ↩ recap | Reversal | [LC 206](https://leetcode.com/problems/reverse-linked-list/) |
| 02 | [Reverse Linked List II](02-lc0092-reverse-linked-list-ii.md) | Reverse a sub-list and reconnect it, using a dummy node | [LC 92](https://leetcode.com/problems/reverse-linked-list-ii/) |
| 03 | [Swap Nodes in Pairs](03-lc0024-swap-nodes-in-pairs.md) | The k = 2 case, group after group | [LC 24](https://leetcode.com/problems/swap-nodes-in-pairs/) |
| ★ | [**Reverse Nodes in k-Group** → NOTES](../NOTES.md) | Check that k nodes remain, reverse them, reconnect, repeat | [LC 25](https://leetcode.com/problems/reverse-nodes-in-k-group/) |

## How the steps add up

- Step 01: flip every pointer in one pass.
- Step 02: flip only a range, then reconnect the node before the range and the node after it.
- Step 03: walk the list group by group (k = 2), moving a `prev` pointer to the end of each finished group.
- **Target:** steps 02 and 03 combined. For each group, check that k nodes exist, reverse exactly those k, reconnect, and jump to the next group.

## Threads

- **Q13** LRU Cache: pointer relinking and dummy nodes (and the full reversal lesson).
- **Q19** Merge k Sorted Lists: a dummy node plus a `tail` pointer to build the answer.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
