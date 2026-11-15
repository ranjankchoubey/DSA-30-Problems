# Roadmap → Q13 · 146. LRU Cache (Medium)

**Goal:** design a cache with a fixed capacity and `get(key)` / `put(key, value)` in **O(1)** average time. When it is over capacity, evict the **least recently used** key. A `get`, or a `put` on a key that already exists, counts as a use.

> 📚 **Before you start:** read [Primer 06 · Linked List](../../course/primers/06-linked-list.md).

## Skills you need

- [ ] Relink `next` pointers without losing the rest of the list
- [ ] Build a doubly linked list with dummy head and tail nodes, so insert and remove need no special cases
- [ ] Store each key's node in a dict, so you can find a node **and** unlink it in O(1)

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Reverse Linked List](01-lc0206-reverse-linked-list.md) | Pointer manipulation basics | [LC 206](https://leetcode.com/problems/reverse-linked-list/) |
| 02 | [Design Linked List](02-lc0707-design-linked-list.md) | Doubly linked list with dummy head/tail nodes | [LC 707](https://leetcode.com/problems/design-linked-list/) |
| 03 | [Hash map to node](03-concept-hash-map-to-node.md) | O(1) lookup plus O(1) unlink | — |
| ★ | [**LRU Cache** → NOTES](../NOTES.md) | Move a node to the back on every use; evict from the front | [LC 146](https://leetcode.com/problems/lru-cache/) |

## How the steps add up

- Step 01: change pointers one at a time without losing nodes.
- Step 02: a doubly linked list can unlink any node it holds in O(1); dummy nodes remove the edge cases.
- Step 03: a dict `key → node` gives O(1) "where is this key?", so the list stays ordered by recency.
- **Target:** keep values in the nodes. `get` and `put` move the node to the back (most recent). When over capacity, remove the front node (least recent) and delete its key from the dict.

## Threads

- **Q18** Reverse Nodes in k-Group and **Q19** Merge k Sorted Lists: the same pointer work and dummy nodes.
- **Q1**, **Q6**, **Q24**: a hash map combined with another structure to make every operation O(1).
- **Q20** Min Stack: another "design a class where every operation is O(1)" problem.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
