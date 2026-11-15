# Step 02 · 707. Design Linked List (Medium)

[← Previous: Reverse Linked List](01-lc0206-reverse-linked-list.md) · [Roadmap](00-roadmap.md) · [Next: Hash map to node →](03-concept-hash-map-to-node.md)

**Practise:** [LeetCode 707](https://leetcode.com/problems/design-linked-list/)
**Skill it adds:** a **doubly linked list** with **dummy head and tail** nodes, where inserting or unlinking a node you hold is O(1) with no special cases.

## Problem in brief

Build your own linked list class with 0-based indices:

- `get(index)`: the value at `index`, or `-1` if the index is invalid
- `addAtHead(val)`, `addAtTail(val)`
- `addAtIndex(index, val)`: insert before position `index`. If `index` equals the length, append. If it is larger, do nothing.
- `deleteAtIndex(index)`: remove the node at `index` if the index is valid

## Key idea

Give every node a `prev` and a `next` pointer, and put two **dummy** nodes at the ends: `head` before the first real node and `tail` after the last one. Every real node now has real neighbours on both sides, so two helpers cover every case:

- `_insert_before(node, val)`: 4 pointer updates
- `_unlink(node)`: 2 pointer updates, `node.prev.next = node.next` and `node.next.prev = node.prev`

The only slow part is **finding** the node for an index. Keep a `size` and walk from whichever end is closer.

```
head ⇄ 1 ⇄ 2 ⇄ 3 ⇄ tail          unlink(2):   head ⇄ 1 ⇄ 3 ⇄ tail
```

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [doubly linked list](../../course/primers/06-linked-list.md#doubly-linked-list)
3. [LeetCode Editorial](https://leetcode.com/problems/design-linked-list/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+design+linked+list)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Node:
    def __init__(self, val=0):
        self.val = val
        self.prev = None
        self.next = None


class MyLinkedList:
    def __init__(self):
        self.head = Node()                 # dummy before the first real node
        self.tail = Node()                 # dummy after the last real node
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0

    def _node_at(self, index: int) -> Node:
        # assumes 0 <= index < size; walk from the closer end
        if index < self.size // 2:
            node = self.head.next
            for _ in range(index):
                node = node.next
        else:
            node = self.tail.prev
            for _ in range(self.size - 1 - index):
                node = node.prev
        return node

    def _insert_before(self, node: Node, val: int) -> None:
        new = Node(val)
        new.prev, new.next = node.prev, node
        node.prev.next = new
        node.prev = new
        self.size += 1

    def _unlink(self, node: Node) -> None:
        node.prev.next = node.next
        node.next.prev = node.prev
        self.size -= 1

    def get(self, index: int) -> int:
        if 0 <= index < self.size:
            return self._node_at(index).val
        return -1

    def addAtHead(self, val: int) -> None:
        self._insert_before(self.head.next, val)

    def addAtTail(self, val: int) -> None:
        self._insert_before(self.tail, val)

    def addAtIndex(self, index: int, val: int) -> None:
        if index == self.size:
            self.addAtTail(val)
        elif 0 <= index < self.size:
            self._insert_before(self._node_at(index), val)

    def deleteAtIndex(self, index: int) -> None:
        if 0 <= index < self.size:
            self._unlink(self._node_at(index))
```

</details>

**Time:** `addAtHead`, `addAtTail` and the two helpers O(1) · `get`, `addAtIndex`, `deleteAtIndex` O(min(i, n − i)) to find the node · **Space:** O(n)

## Test cases

| Operations | `get` outputs |
| --- | --- |
| `addAtHead(1), addAtTail(3), addAtIndex(1,2), get(1), deleteAtIndex(1), get(1)` | `2, 3` |
| `addAtIndex(0,10), addAtIndex(0,20), addAtIndex(1,30), get(0), get(1), get(2)` | `20, 30, 10` |
| `addAtHead(7), addAtIndex(3,0), deleteAtIndex(1), get(1), deleteAtIndex(0), get(0)` | `-1, -1` (the out-of-range add and delete do nothing) |

## Pitfall

- Without dummies, every insert and delete needs `if node is self.head` / `if node is self.tail` branches, and that is where most bugs come from.
- In `_insert_before`, set the new node's own pointers first. If you change `node.prev` first, you lose the old predecessor.
- `addAtIndex(size, val)` is valid (it appends), but `get(size)` is not.

## What changes in the next step

`_unlink(node)` is O(1), but only once you **hold** the node, and finding it by index costs O(n). An LRU cache looks items up by **key**, not by position. Step 03 stores `key → node` in a dict, so finding a node is O(1) as well.
