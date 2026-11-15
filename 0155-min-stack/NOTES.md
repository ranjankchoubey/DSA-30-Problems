# 0155. Min Stack

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q20 | [155](https://leetcode.com/problems/min-stack/) | Medium | Stack | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0155-min-stack.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Undo Stack With Instant Minimum:** a text-analysis tool keeps a stack of numeric scores and needs `push`, `pop`, `top` and `getMin`, all in O(1), for up to 300,000 operations. `pop` and `top` are never called on an empty stack. Then reduce memory when many pushes are larger than the current minimum, comparing two designs: a second stack, or storing differences.

## 2. Pattern recognition

- **Pattern:** Stack (design), with extra state kept in step with each push
- **Signals in the statement:** "`getMin` in O(1)" on a structure where items leave in **LIFO** order. The minimum can only change at a push or a pop, so it can be recorded then.
- **Core idea in one line:** while an item is on the stack nothing below it changes, so record the minimum when you push and get the old one back when you pop.

## 3. Approaches

### Approach 1: Plain stack, scan on `getMin`

`min(self.stack)` on every call. **Time:** O(1) push/pop/top, **O(n)** `getMin` · **Space:** O(n)

### Approach 2: `(value, min_so_far)` pairs ([step 03](./prerequisites/03-concept-store-min-with-each-push.md))

**Time:** O(1) every operation · **Space:** O(n), always 2 numbers per item

### Approach 3: Design A, a second stack of minimums (optimal)

`stack` holds every value. `min_stack` gets a push **only** when the new value is `≤` its top, and a pop **only** when the popped value equals its top. **Time:** O(1) every operation · **Space:** O(n) for `stack`, plus one entry per new low (worst case O(n) for decreasing pushes, best case O(1))

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []               # the minimum so far, recorded only when it drops or ties

    def push(self, val: int) -> None:
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)

    def pop(self) -> None:
        if self.stack.pop() == self.min_stack[-1]:
            self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

</details>

### Approach 4: Design B, store differences

One stack plus one `self.min` variable. Each slot stores `val − (min before this push)`. A **negative** slot means "this push set a new minimum", and it also tells you the old minimum: `old_min = val − diff = self.min − diff`. **Time:** O(1) every operation · **Space:** one number per item and **one** extra variable

<details>
<summary><b>Show the differences design</b></summary>

```python
class MinStack:
    def __init__(self):
        self.stack = []                   # val - (min before this push)
        self.min = None

    def push(self, val: int) -> None:
        if not self.stack:
            self.stack.append(0)
            self.min = val
        else:
            diff = val - self.min
            self.stack.append(diff)
            if diff < 0:                  # new minimum
                self.min = val

    def pop(self) -> None:
        diff = self.stack.pop()
        if diff < 0:                      # popping the minimum: restore the old one
            self.min = self.min - diff
        if not self.stack:
            self.min = None

    def top(self) -> int:
        diff = self.stack[-1]
        return self.min if diff < 0 else self.min + diff

    def getMin(self) -> int:
        return self.min
```

</details>

## 4. Why it works

- **LIFO keeps old minimums valid.** While `x` is on the stack, nothing below it can change, so "the minimum of `x` and everything below" is fixed until `x` is popped. Every design stores that number (or enough to rebuild it) at push time.
- **Design A invariant:** `min_stack[-1] == min(stack)`. A push bigger than the minimum can't change it, so nothing is recorded. A push `≤` the minimum becomes the new top of `min_stack`. A pop removes a `min_stack` entry exactly when it removes the value that created it.
- **Why `≤` and not `<`:** with `push(5), push(5)` (TC 2), both 5s must be recorded. Otherwise the first `pop()` empties `min_stack` while a 5 is still on the stack.
- **Design B invariant:** `self.min` is the current minimum, and each slot is `val − (min before the push)`.
  - `diff ≥ 0`: the minimum didn't change, and `val = self.min + diff`.
  - `diff < 0`: `val` **is** the new minimum (so `top = self.min`), and the old minimum was `val − diff`.

## 5. Dry run: Design A on TC 3 → `1, 2`

| Operation | stack | min_stack | Output / note |
| --- | --- | --- | --- |
| `push(2)` | `[2]` | `[2]` | first value |
| `push(1)` | `[2, 1]` | `[2, 1]` | 1 ≤ 2 |
| `push(1)` | `[2, 1, 1]` | `[2, 1, 1]` | 1 ≤ 1, recorded again |
| `pop()` | `[2, 1]` | `[2, 1]` | popped 1 == top of min_stack |
| `getMin()` | | | **1** |
| `pop()` | `[2]` | `[2]` | popped 1 == top of min_stack |
| `getMin()` | | | **2** |

## 6. Edge cases & pitfalls

- **Duplicates of the minimum:** use `≤` in Design A (TC 2, TC 3).
- In Design A's `pop`, compare the **popped value** with `min_stack[-1]`. Don't compare indices or positions.
- **Overflow in Design B:** `val − min` can be twice the value range (e.g. `2³¹−1 − (−2³¹)`). Python ints don't overflow, but in Java/C++ store the differences as 64-bit.
- Design B only suits integers. With floats, the repeated `+`/`−` adds rounding error.
- Negative values need no special handling in any design.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `push(-2), push(0), push(-3), getMin(), pop(), top(), getMin()` | `-3, 0, -2` |
| TC 2 | `push(5), push(5), getMin(), pop(), getMin()` | `5, 5` |
| TC 3 | `push(2), push(1), push(1), pop(), getMin(), pop(), getMin()` | `1, 2` |

## 8. Follow-up questions (from course doc)

- **Q: Design a stack with `push`, `pop`, `top` and `getMin`, all O(1).**
  **A:** Record the minimum at push time, see §3 and §4. Every operation touches only the top of one or two lists.
- **Q: How would you reduce memory if many pushes are larger than the current minimum?**
  **A:** The pairs design stores a minimum for **every** push. Design A stores one only when a value `≤` the minimum arrives, so pushes larger than the minimum cost nothing extra. To go further:
  - store `[value, count]` in `min_stack`, so repeated equal minimums (`1, 1, 1, 1`) share one entry, or
  - use Design B, which drops the second stack completely and keeps just one variable.

  Extra numbers stored besides the values themselves:

  | Pushes | Pairs | Design A | Design A + counts | Design B |
  | --- | --- | --- | --- | --- |
  | `1, 5, 6, 7, 8` | 5 | 1 | 2 (one `[1, 1]` entry) | 1 variable |
  | `1, 1, 1, 1` | 4 | 4 | 2 (one `[1, 4]` entry) | 1 variable |
  | `5, 4, 3, 2` (falling) | 4 | 4 | 8 (four entries) | 1 variable |

- **Q: Compare the two designs (second stack vs differences).**
  **A:**

  | | Design A: second stack | Design B: differences |
  | --- | --- | --- |
  | Extra memory | 0 to n entries (one per new low) | 1 variable |
  | `top()` | read directly | rebuild from `min + diff` |
  | Readability | easy to explain and debug | trickier; the sign of `diff` carries meaning |
  | Overflow risk | none | `val − min` needs a wider integer type |
  | Values allowed | anything comparable | integers only (floats drift) |

  Lead with Design A in an interview. Mention Design B when memory is the main concern.

<details>
<summary><b>Show Design A with counts</b></summary>

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []               # [minimum, how many times it is currently on the stack]

    def push(self, val: int) -> None:
        self.stack.append(val)
        if not self.min_stack or val < self.min_stack[-1][0]:
            self.min_stack.append([val, 1])
        elif val == self.min_stack[-1][0]:
            self.min_stack[-1][1] += 1

    def pop(self) -> None:
        if self.stack.pop() == self.min_stack[-1][0]:
            self.min_stack[-1][1] -= 1
            if self.min_stack[-1][1] == 0:
                self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1][0]
```

</details>

## 9. Threads

- **Q14** Longest Valid Parentheses: starts from the bracket-matching stack of step 01.
- **Q16** Basic Calculator III: pushes saved state at each `(`, the same "store it at push time" idea.
- **Q13** LRU Cache: another design class where every operation must be O(1).
- **Q17 / Q15** monotonic stacks: the stack's order itself answers "next greater / smaller".

## 10. One-line revision

> Record the minimum at push time (pairs, or a second stack that grows only on a value ≤ min); a pop then brings back the old minimum for free.
