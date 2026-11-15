# 0146. LRU Cache

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q13 | [146](https://leetcode.com/problems/lru-cache/) | Medium | Linked List | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0146-lru-cache.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Mobile App Cache:** a phone app can keep only `capacity` items in memory. `get(key)` and `put(key, value)` must be O(1) on average. When the cache is over capacity, evict the **least recently used** item. A `get`, or a `put` on an existing key, counts as a use. Constraints: capacity ≤ 3000, up to 200,000 operations.

## 2. Pattern recognition

- **Pattern:** Linked List, hash map + doubly linked list
- **Signals in the statement:** "O(1)" lookup by key → hash map. "Least recently used", "counts as a use" → items must be **reordered** on every access and the oldest found instantly → a list ordered by recency where any node can move in O(1) → doubly linked list.
- **Core idea in one line:** a dict `key → node` finds a node in O(1); a doubly linked list with dummies moves it to the back or drops the front in O(1).

## 3. Approaches

### Approach 1: Brute force (a Python list of keys in recency order)

Every use searches for the key and moves it to the end (`list.remove` + `append`), and eviction is `pop(0)`. **Time:** O(capacity) per operation · **Space:** O(capacity). With 200,000 operations and capacity 3000 that is up to ~6×10⁸ steps.

### Approach 2: `OrderedDict` (aside)

Python's `OrderedDict` is a hash map plus a doubly linked list already, with `move_to_end(key)` and `popitem(last=False)`. Good in production code, but in an interview you are usually asked to build the list yourself. **Time:** O(1) average per operation · **Space:** O(capacity)

<details>
<summary><b>Show the OrderedDict version</b></summary>

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.data = OrderedDict()              # oldest first, newest last

    def get(self, key: int) -> int:
        if key not in self.data:
            return -1
        self.data.move_to_end(key)
        return self.data[key]

    def put(self, key: int, value: int) -> None:
        if key in self.data:
            self.data.move_to_end(key)
        self.data[key] = value
        if len(self.data) > self.cap:
            self.data.popitem(last=False)      # drop the oldest
```

</details>

### Approach 3: Hash map + doubly linked list (optimal)

Dummy `head` and `tail`. `head.next` is the least recently used node, `tail.prev` the most recently used. Every use unlinks the node and appends it before `tail`. **Time:** O(1) average per operation · **Space:** O(capacity)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Node:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None


class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.node_of = {}                       # key -> Node
        self.head, self.tail = Node(), Node()   # head.next = LRU, tail.prev = MRU
        self.head.next, self.tail.prev = self.tail, self.head

    def _unlink(self, node: Node) -> None:
        node.prev.next = node.next
        node.next.prev = node.prev

    def _append(self, node: Node) -> None:      # make node the most recently used
        node.prev, node.next = self.tail.prev, self.tail
        self.tail.prev.next = node
        self.tail.prev = node

    def get(self, key: int) -> int:
        if key not in self.node_of:
            return -1
        node = self.node_of[key]
        self._unlink(node)
        self._append(node)
        return node.val

    def put(self, key: int, value: int) -> None:
        if key in self.node_of:
            node = self.node_of[key]
            node.val = value
            self._unlink(node)
        else:
            if len(self.node_of) == self.cap:   # full: evict the LRU node
                lru = self.head.next
                self._unlink(lru)
                del self.node_of[lru.key]
            node = Node(key, value)
            self.node_of[key] = node
        self._append(node)
```

</details>

## 4. Why it works

- **Invariant:** the list holds exactly the keys in `node_of`, ordered from least recently used (front) to most recently used (back).
- Every use (`get` hit, `put` on any key) unlinks that node and appends it at the back, so the invariant holds after each call. Untouched nodes keep their relative order.
- So the front node is always the least recently used, and evicting it is O(1). The node stores its **key**, so the dict entry can be deleted too.
- Each operation is one or two dict operations plus a constant number of pointer changes: O(1) average. The dummies mean `_unlink` and `_append` never deal with `None`.

## 5. Dry run: TC 1, capacity = 2

| Call | Output | List after (front = LRU → back = MRU) | Evicted |
| --- | --- | --- | --- |
| `put(1,1)` | | `[1]` | |
| `put(2,2)` | | `[1, 2]` | |
| `get(1)` | **1** | `[2, 1]` | |
| `put(3,3)` | | `[1, 3]` | 2 |
| `get(2)` | **-1** | `[1, 3]` | |
| `put(4,4)` | | `[3, 4]` | 1 |
| `get(1)` | **-1** | `[3, 4]` | |
| `get(3)` | **3** | `[4, 3]` | |
| `get(4)` | **4** | `[3, 4]` | |

## 6. Edge cases & pitfalls

- **`put` on an existing key** updates the value and counts as a use, but must **not** evict anything (TC 3: `put(2,1), put(2,2)` keeps one entry with value 2).
- **Capacity 1** (TC 2): every new key evicts the only one.
- The node must store its **key**. Otherwise you can't delete the evicted entry from the dict, and the dict and list drift apart.
- A `get` miss must not change the order.
- Check "full" against the dict size **before** inserting a new key (or insert, then evict if over). Don't evict when only updating.

## 7. Test cases (from course doc)

| Test | Input | Expected `get` outputs |
| --- | --- | --- |
| TC 1 | capacity=2: put(1,1), put(2,2), get(1), put(3,3), get(2), put(4,4), get(1), get(3), get(4) | `1, -1, -1, 3, 4` |
| TC 2 | capacity=1: put(1,1), put(2,2), get(1), get(2) | `-1, 2` |
| TC 3 | capacity=2: put(2,1), put(2,2), get(2), put(1,1), put(4,1), get(2) | `2, -1` |

## 8. Follow-up questions (from course doc)

- **Q: Explain the hash map plus doubly linked list design.**
  **A:** The dict answers "where is key k?" in O(1), and stores the node, not just the value. The doubly linked list keeps keys in order of use. Because each node knows both neighbours, it can be unlinked in O(1) without a search, and with dummy `head`/`tail` there are no edge cases. A use moves the node to the back. Eviction removes `head.next` and deletes its key from the dict. A singly linked list wouldn't work: unlinking a node needs its predecessor, which takes an O(n) walk. See §4.

- **Q: What must change to make it thread-safe with low lock contention?**
  **A:**
  1. **Why it's unsafe now:** even `get` **writes** (it moves a node). Two threads relinking at the same time can lose nodes, create cycles, or leave the dict and list out of sync. Python's GIL doesn't help, because it only makes single bytecode steps atomic, not a 4-pointer relink.
  2. **Simplest correct fix:** one `threading.Lock` around the whole body of `get` and `put`. It's correct, but every call waits for every other call, so contention is high. A read-write lock doesn't help, since `get` is also a writer.
  3. **Low contention: shard (stripe) the cache.** Split it into N independent LRU caches, each with its own lock and about `capacity / N` slots, and send each key to shard `hash(key) % N`. Threads using different shards never block each other. The trade-off: eviction is LRU **within a shard**, not globally, so it is an approximation, and one hot shard can evict while others still have room.
  4. **Even less locking:** make the lookup lock-free with a concurrent hash map (e.g. Java's `ConcurrentHashMap`), and **batch** the recency updates. A `get` just records "key was used" in a buffer, and whichever thread manages to `tryLock` replays the buffer into the list (the Caffeine library works this way). Or use an approximate policy such as **CLOCK**, where `get` only sets a "recently used" bit and never touches the list.
  5. Keep critical sections tiny: compute or load the value **outside** the lock, and never do I/O while holding it.

<details>
<summary><b>Show a sharded, thread-safe wrapper</b> (uses the LRUCache above)</summary>

```python
import threading

class ShardedLRUCache:
    def __init__(self, capacity: int, shards: int = 16):
        shards = max(1, min(shards, capacity))       # every shard gets at least 1 slot
        base, extra = divmod(capacity, shards)
        self.shards = [LRUCache(base + (i < extra)) for i in range(shards)]
        self.locks = [threading.Lock() for _ in range(shards)]

    def _index(self, key) -> int:
        return hash(key) % len(self.shards)

    def get(self, key: int) -> int:
        i = self._index(key)
        with self.locks[i]:                          # only this shard is blocked
            return self.shards[i].get(key)

    def put(self, key: int, value: int) -> None:
        i = self._index(key)
        with self.locks[i]:
            self.shards[i].put(key, value)
```

</details>

The shard capacities add up to exactly `capacity`. With `shards=1` this is the single-lock version from point 2.

## 9. Threads

- **Q1**, **Q6**, **Q24**: a hash map paired with another structure so every operation becomes O(1).
- **Q18** Reverse Nodes in k-Group and **Q19** Merge k Sorted Lists: pointer relinking and dummy nodes.
- **Q20** Min Stack: another design problem where every method must be O(1).

## 10. One-line revision

> Dict key → node plus a doubly linked list with dummy head and tail; every use unlinks the node and appends it before tail; evict head.next and delete its key.
