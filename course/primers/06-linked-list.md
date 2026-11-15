# Primer 06 · Linked List

Read this **before** the Linked List ladders (Q13, Q18, Q19), or when a step's key idea doesn't make sense yet.

## Linked list basics

A **linked list** keeps each value in its own **node**, and each node points to the next one. There are no indices: to reach the i-th node you walk from the `head`. In exchange, adding or removing a node next to one you already hold is O(1), because only a few pointers change and nothing shifts.

LeetCode's singly linked node:

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

| Operation | Linked list | Python `list` |
| --- | --- | --- |
| Read the i-th item | O(i), walk from the head | O(1) |
| Insert / delete at the front | O(1) | O(n) |
| Insert after a node you already hold | O(1) | O(n) |
| Delete a node you already hold | singly: O(n) (you need its predecessor) · doubly: **O(1)** | O(n) |
| Append at the end | O(1) if you keep a `tail` pointer | O(1) amortised |
| Search for a value | O(n) | O(n) |

Walk a list:

```python
node = head
while node:              # stops after the last node, whose next is None
    print(node.val)
    node = node.next
```

Helpers for testing on your own machine (LeetCode builds the lists for you):

```python
def build(values):
    dummy = tail = ListNode()
    for v in values:
        tail.next = ListNode(v)
        tail = tail.next
    return dummy.next

def to_list(head):
    out = []
    while head:
        out.append(head.val)
        head = head.next
    return out
```

## Dummy node

A **dummy** (sentinel) node sits in front of the real head. Every real node now has a predecessor, so "insert or delete at the head" is no longer a special case. At the end, return `dummy.next`, which is the real head even if it changed.

```python
dummy = ListNode(0, head)
prev = dummy
# ... relink nodes after prev, possibly including the first real node ...
return dummy.next
```

Building a new list with a `tail` pointer (used when merging lists):

```python
dummy = tail = ListNode()
for v in [1, 2, 3]:
    tail.next = ListNode(v)
    tail = tail.next
head = dummy.next        # 1 -> 2 -> 3
```

## Reversing a list

Three pointers: `prev` (the part already reversed), `curr` (the node being moved) and `nxt` (saved so the rest of the list isn't lost).

```
start:          None    1 → 2 → 3 → None        prev=None, curr=1
after node 1:   None ← 1    2 → 3 → None        prev=1,    curr=2
after node 2:   None ← 1 ← 2    3 → None        prev=2,    curr=3
after node 3:   None ← 1 ← 2 ← 3    None        prev=3,    curr=None  → return prev
```

```python
def reverse(head):
    prev, curr = None, head
    while curr:
        nxt = curr.next      # 1. save the rest
        curr.next = prev     # 2. flip one pointer
        prev = curr          # 3. move both pointers forward
        curr = nxt
    return prev              # the old tail is the new head
```

**Reverse only k nodes** and keep going: stop after k steps instead of at `None`. Then `prev` is the new front of that piece and `curr` is the first node **after** it. You must reconnect both ends yourself.

## Fast and slow pointers

Move `slow` one step and `fast` two steps. When `fast` reaches the end, `slow` is in the middle. Use it to split a list in half (merge sort on a list) or to detect a cycle.

```python
def split_in_half(head):          # head has at least 2 nodes
    slow, fast = head, head.next
    while fast and fast.next:
        slow, fast = slow.next, fast.next.next
    right = slow.next
    slow.next = None                 # cut: head..slow is the left half
    return head, right
```

## Doubly linked list

Each node also points **back** (`prev`). A node can then unlink itself in O(1) without searching for its predecessor. Use **two** dummies, `head` and `tail`, so the list is never truly empty and insert/remove never need `if` checks.

```python
class Node:
    def __init__(self, key=0, val=0):
        self.key, self.val = key, val
        self.prev = self.next = None

head, tail = Node(), Node()          # dummies: real nodes live between them
head.next, tail.prev = tail, head

def unlink(node):                    # O(1)
    node.prev.next = node.next
    node.next.prev = node.prev

def insert_before_tail(node):        # O(1)
    node.prev, node.next = tail.prev, tail
    tail.prev.next = node
    tail.prev = node
```

Pair it with a dict `key → node` and you can find **and** move any item in O(1). That is the LRU cache design (Q13).

Python already has this combination built in: `collections.OrderedDict` (`move_to_end(key)`, `popitem(last=False)`). It's fine in real code, but interviewers usually want you to build the list yourself.

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "linked list", `ListNode`, "relink nodes", "don't change values" | pointer manipulation; draw the pointers first |
| the head might change (delete, insert, reverse at the front) | dummy node, return `dummy.next` |
| "reverse", "reverse every k", "reverse between left and right" | `prev / curr / nxt` reversal |
| "middle", "split in half", "cycle" | fast and slow pointers |
| "merge sorted lists" | dummy + `tail` pointer, take the smaller head |
| O(1) "move to front / remove any item" plus O(1) lookup by key | doubly linked list + hash map |

## Common pitfalls

- Overwriting `curr.next` before saving it, which loses the rest of the list.
- Returning `head` after the head has changed. Return `dummy.next` (or `prev` after a full reversal).
- Forgetting to cut a list (`slow.next = None`), which leaves a cycle or doubles the work.
- Following `node.next.next` without checking that `node.next` exists.
- In a doubly linked list, updating only two of the four pointers on insert.
- In a key → node design, forgetting to store the **key** in the node, so you can't delete it from the dict on eviction.

## Learn more

- NeetCode roadmap (Linked List node): <https://neetcode.io/roadmap>
- Python docs: [collections.OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict) · [collections.deque](https://docs.python.org/3/library/collections.html#collections.deque)
- Visualise linked lists: <https://visualgo.net/en/list>
