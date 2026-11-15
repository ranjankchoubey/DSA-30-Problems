# Primer 03 · Sliding Window

Read this **before** the Sliding Window ladders (Q9, Q4, Q5), or when a step's key idea doesn't make sense yet.

## What a window is

A **window** is a contiguous slice `nums[left .. right]` of an array or string. Recomputing every slice from scratch costs O(n·k) or O(n²). Instead you **slide** it: add the element that enters on the right, remove the one that leaves on the left, and keep a small summary up to date (a sum, a count map, a deque).

Both pointers only move **forward**, so each element enters the window once and leaves at most once. That is O(n) work in total.

## Python tools

| Operation | Code | Time |
| --- | --- | --- |
| Update a running sum | `total += nums[r]`, `total -= nums[l]` | O(1) |
| Count a value in the window | `count[c] = count.get(c, 0) + 1` | O(1) avg |
| Drop a value whose count hit 0 | `del count[c]` | O(1) avg |
| Number of distinct values in the window | `len(count)` | O(1) |
| Window length | `right - left + 1` | O(1) |
| Deque: add / remove at either end | `dq.append(x)`, `dq.pop()`, `dq.appendleft(x)`, `dq.popleft()` | O(1) |
| Deque: peek at either end | `dq[0]`, `dq[-1]` | O(1) |
| List: remove from the front (avoid) | `a.pop(0)` | O(n) |
| Compare two count maps | `Counter(a) == Counter(b)` | O(number of keys) |

```python
from collections import deque, Counter

dq = deque()
dq.append(3)
dq.append(5)
dq.popleft()            # 3, O(1) (a list's pop(0) would be O(n))
dq[0], dq[-1]           # (5, 5): front and back

Counter("abca")         # {'a': 2, 'b': 1, 'c': 1}
```

## Fixed size window

The window always holds exactly `k` elements. When `right` moves to `i`, the element `nums[i − k]` leaves.

```python
def max_window_sum(nums, k):
    window = sum(nums[:k])                 # first window: nums[0 .. k-1]
    best = window
    for i in range(k, len(nums)):
        window += nums[i] - nums[i - k]    # add the new right, drop the old left
        best = max(best, window)
    return best
```

- The window ending at `i` is `nums[i − k + 1 .. i]`. The first full one ends at `i = k − 1`, so there are `n − k + 1` windows.
- This works for **sums** because subtraction undoes addition. A **max** can't be undone that way, which is why the window maximum needs a [monotonic deque](#monotonic-deque).

## Variable size window

The window grows and shrinks to follow a rule. There are two shapes.

### Longest valid window: expand, then shrink while invalid

```python
left = 0
best = 0
for right, x in enumerate(s):
    ...                                    # add x to the window state
    while ...:                             # the window breaks the rule
        ...                                # remove s[left] from the window state
        left += 1
    best = max(best, right - left + 1)     # the window is valid here
```

Used by Longest Substring Without Repeating Characters and Longest Substring with At Most K Distinct Characters (Q9).

### Shortest valid window: expand, then shrink while valid

```python
left = 0
best = float("inf")
for right, x in enumerate(nums):
    ...                                    # add x to the window state
    while ...:                             # the window satisfies the rule
        best = min(best, right - left + 1) # record BEFORE shrinking
        ...                                # remove nums[left]
        left += 1
```

Used by Minimum Size Subarray Sum and Minimum Window Substring (Q4).

**When does a variable window work?** The rule must be **monotonic**. Shrinking a valid window must keep it valid (for "longest"), or growing a valid window must keep it valid (for "shortest"). "At most k distinct" and "sum ≥ target with positive numbers" are monotonic. "Sum equals k with negative numbers" is not, so use prefix sums and a hash map instead ([Primer 01](01-arrays-and-hashing.md#4-prefix-sum--hash-map-of-counts-subarray-sum-equals-k)).

## Counting characters in a window

To ask "does the window contain every character of `t`, with multiplicity?" in O(1), keep two maps and one counter:

```python
from collections import Counter

def make_window_checker(t):
    need = Counter(t)          # how many of each character t requires
    required = len(need)       # distinct characters to satisfy
    have = {}                  # counts inside the window
    state = {"formed": 0}      # distinct characters with have[c] >= need[c]

    def add(c):
        have[c] = have.get(c, 0) + 1
        if c in need and have[c] == need[c]:   # just reached the requirement
            state["formed"] += 1

    def remove(c):
        if c in need and have[c] == need[c]:   # about to drop below it
            state["formed"] -= 1
        have[c] -= 1

    def valid():
        return state["formed"] == required

    return add, remove, valid
```

`formed` only changes when a count **crosses** its requirement, so each `add`/`remove` is O(1) and validity is a single comparison.

## Monotonic deque

To get the **maximum** of every window, keep a deque of **indices** whose values are **decreasing** from front to back.

```python
from collections import deque

def window_maxima(nums, k):
    dq = deque()                           # indices; nums[dq[0]] is the largest
    out = []
    for i, x in enumerate(nums):
        while dq and nums[dq[-1]] <= x:    # smaller values can never be the max again
            dq.pop()
        dq.append(i)
        if dq[0] <= i - k:                 # the front has slid out of the window
            dq.popleft()
        if i >= k - 1:
            out.append(nums[dq[0]])
    return out
```

- **Back:** a new value `x` evicts every smaller value behind it. They are older **and** smaller, so they can never be a window maximum again.
- **Front:** the oldest index leaves when it falls out of the window.
- Each index is pushed once and popped at most once, so the total is O(n) even though one step may pop many.
- For the window **minimum**, flip the comparison to `>=`.
- Store **indices**, not values: an index tells you when the element has left the window.

The deque is a [monotonic stack](04-stack.md#monotonic-stack) (pop from the back) that can also pop from the front.

## Window maximum with a heap

A simpler, slower alternative: a max-heap of `(-value, index)` pairs. Old entries are not removed when they leave the window. They are popped only when they reach the top (**lazy deletion**).

```python
import heapq

def window_maxima_heap(nums, k):
    heap = []                              # (-value, index): a max-heap by negation
    out = []
    for i, x in enumerate(nums):
        heapq.heappush(heap, (-x, i))
        if i >= k - 1:
            while heap[0][1] <= i - k:     # the top is stale: it left the window
                heapq.heappop(heap)
            out.append(-heap[0][0])
    return out
```

O(n log n) time and O(n) space, because stale entries can pile up in the heap.

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "every window of size k", "k consecutive" | fixed size window |
| "longest substring / subarray such that…" | variable window, shrink while invalid |
| "shortest / minimum subarray such that…" | variable window, shrink while valid |
| "at most k distinct", "no repeating characters" | variable window + count map |
| "contains all characters of t", "anagram", "permutation of" | window + `need`/`have` counts |
| "maximum / minimum of every window" | monotonic deque |
| "next greater", "next warmer day", "previous smaller" | monotonic stack ([Primer 04](04-stack.md#monotonic-stack)) |
| "sum equals k" and negative numbers allowed | **not** a window: prefix sum + hash map ([Primer 01](01-arrays-and-hashing.md)) |

## Common pitfalls

- The window length is `right - left + 1`, not `right - left`.
- Shrinking with `if` instead of `while`. One removal may not be enough to fix the window.
- Forgetting `del count[c]` when a count drops to 0, so `len(count)` overcounts distinct values.
- Recording the answer at the wrong moment: **after** shrinking for "longest", **inside** the shrink loop for "shortest".
- Using `list.pop(0)` for the front of a queue. It is O(n); use `collections.deque`.
- Storing values in the deque instead of indices, so you can't tell when the front has left the window.
- Using a sliding window when the rule isn't monotonic (e.g. sums with negative numbers).

## Learn more

- NeetCode roadmap (the Sliding Window node): <https://neetcode.io/roadmap>
- Python docs: [collections.deque](https://docs.python.org/3/library/collections.html#collections.deque) · [collections.Counter](https://docs.python.org/3/library/collections.html#collections.Counter) · [heapq](https://docs.python.org/3/library/heapq.html)
- Visualise queues and deques: <https://visualgo.net/en/list>
