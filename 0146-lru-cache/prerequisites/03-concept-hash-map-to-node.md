# Step 03 · Concept: hash map to node

[← Previous: Design Linked List](02-lc0707-design-linked-list.md) · [Roadmap](00-roadmap.md) · [Target: LRU Cache →](../NOTES.md)

> 📚 **New to this?** Primer: [doubly linked list](../../course/primers/06-linked-list.md#doubly-linked-list)

**Skill it adds:** **O(1) lookup plus O(1) unlink**. A dict finds the node, the doubly linked list moves it.

## Idea

Each structure alone is missing half of what we need:

| Structure | Find a key | Move / remove it | Know the oldest |
| --- | --- | --- | --- |
| dict | O(1) | O(1) | no order to ask |
| doubly linked list | O(n) walk | O(1) once you hold the node | O(1) (first node) |
| **dict of key → node + list** | **O(1)** | **O(1)** | **O(1)** |

So store the **node itself** as the dict value. The list keeps keys in order of use: the **front** (just after `head`) is the least recently used and the **back** (just before `tail`) is the most recently used.

- `touch(key)`: look the node up in the dict, unlink it, and append it at the back. A new key gets a new node.
- `pop_oldest()`: unlink the front node and delete **its key** from the dict. That's why the node stores its key.

## Code

```python
class Node:
    def __init__(self, key=0):
        self.key = key
        self.prev = None
        self.next = None


class RecencyList:
    """Keys ordered from least recently used (front) to most recently used (back)."""

    def __init__(self):
        self.node_of = {}                      # key -> its Node
        self.head, self.tail = Node(), Node()  # dummies
        self.head.next, self.tail.prev = self.tail, self.head

    def _unlink(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _append(self, node):                   # insert just before tail
        node.prev, node.next = self.tail.prev, self.tail
        self.tail.prev.next = node
        self.tail.prev = node

    def touch(self, key):                      # mark key as most recently used
        if key in self.node_of:
            node = self.node_of[key]
            self._unlink(node)
        else:
            node = Node(key)
            self.node_of[key] = node
        self._append(node)

    def pop_oldest(self):                      # remove and return the LRU key
        node = self.head.next
        self._unlink(node)
        del self.node_of[node.key]
        return node.key

    def keys(self):                            # front to back, for checking
        out, node = [], self.head.next
        while node is not self.tail:
            out.append(node.key)
            node = node.next
        return out
```

**Time:** `touch` and `pop_oldest` O(1) average (one dict operation and a few pointer changes) · **Space:** O(n)

## Walk-through

| Call | `keys()` after (front = oldest) | Returns |
| --- | --- | --- |
| `touch("a")` | `[a]` | |
| `touch("b")` | `[a, b]` | |
| `touch("c")` | `[a, b, c]` | |
| `touch("a")` | `[b, c, a]` (a moved to the back) | |
| `pop_oldest()` | `[c, a]` | `"b"` |
| `touch("d")` | `[c, a, d]` | |

## Test cases

| Calls | Expected |
| --- | --- |
| `touch(1), touch(2), touch(1), pop_oldest()` | returns `2`; `keys()` is `[1]` |
| `touch(5), pop_oldest()` | returns `5`; `keys()` is `[]` |
| `touch(1), touch(2), touch(3), touch(2), touch(1), pop_oldest()` | returns `3`; `keys()` is `[2, 1]` |

## What changes in the target

Store the **value** in the node as well. Then `get(key)` is "look up, touch, return the value", and `put(key, value)` is "update or create, touch, and if over capacity, `pop_oldest()`". Every operation stays O(1) → [NOTES](../NOTES.md).
