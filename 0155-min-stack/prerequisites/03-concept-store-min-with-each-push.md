# Step 03 · Concept: store the min with each push

[← Previous: Baseball Game](02-lc0682-baseball-game.md) · [Roadmap](00-roadmap.md) · [Target: Min Stack →](../NOTES.md)

> 📚 **New to this?** Primer: [stack basics](../../course/primers/04-stack.md#stack-basics)

**Skill it adds:** storing `(value, min_so_far)` pairs, so the minimum is always on top.

## Idea

One `self.min` variable breaks on `pop`: if you pop the minimum, finding the new one means scanning the whole stack, which is O(n).

The fix: each entry stores its value **and the minimum of itself and everything below it**. While an item is on the stack, nothing below it can change (LIFO), so that minimum stays correct until the item is popped.
- `getMin` = the second field of the top entry
- `pop` removes the entry, and the entry underneath already holds the previous minimum

## Code

```python
class MinStack:
    def __init__(self):
        self.stack = []                   # (value, min of this value and everything below)

    def push(self, val: int) -> None:
        current_min = min(val, self.stack[-1][1]) if self.stack else val
        self.stack.append((val, current_min))

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1][0]

    def getMin(self) -> int:
        return self.stack[-1][1]
```

**Time:** O(1) for every operation · **Space:** O(n), two numbers per item

## Walk-through: `push(-2), push(0), push(-3), getMin(), pop(), top(), getMin()`

| Operation | stack (bottom → top) | Output |
| --- | --- | --- |
| `push(-2)` | `(-2,-2)` | |
| `push(0)` | `(-2,-2) (0,-2)` | |
| `push(-3)` | `(-2,-2) (0,-2) (-3,-3)` | |
| `getMin()` | | `-3` |
| `pop()` | `(-2,-2) (0,-2)` | |
| `top()` | | `0` |
| `getMin()` | | `-2` |

## Test cases

| Operations | Expected outputs |
| --- | --- |
| `push(-2), push(0), push(-3), getMin(), pop(), top(), getMin()` | `-3, 0, -2` |
| `push(5), push(5), getMin(), pop(), getMin()` | `5, 5` |
| `push(2), push(1), push(1), pop(), getMin(), pop(), getMin()` | `1, 2` |

## What changes in the target

Every entry stores a minimum, even when the minimum didn't change. Pushing `1, 5, 6, 7, 8` stores `1` five times. The target removes that waste in one of two ways:
1. a **second stack** that only grows when a value `≤` the current minimum arrives, or
2. one stack of **differences** `value − min`, plus a single `min` variable.

→ [NOTES](../NOTES.md)
