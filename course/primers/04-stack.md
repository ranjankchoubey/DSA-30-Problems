# Primer 04 · Stack

Read this **before** the Stack ladders (Q20, Q17, Q15, Q14, Q16), or when a step's key idea doesn't make sense yet. The **Monotonic stack** section is also used by the Q5 Sliding Window Maximum ladder.

## Stack basics

A **stack** is last in, first out (LIFO). You can only add or remove at the **top**, like a pile of plates. In Python a plain `list` is a stack, and the end of the list is the top.

| Operation | Code | Time |
| --- | --- | --- |
| Push | `stack.append(x)` | O(1) amortised |
| Pop (remove and return the top) | `stack.pop()` | O(1) |
| Peek at the top | `stack[-1]` | O(1) |
| Is it empty? | `not stack` | O(1) |
| Size | `len(stack)` | O(1) |
| Sum / scan everything | `sum(stack)` | O(n) |

`collections.deque` has the same `append` / `pop`, but a list is the usual choice. Use a deque only when you also remove from the **front** (a queue, or a monotonic deque).

> `stack.pop()` and `stack[-1]` on an empty list raise `IndexError`. Check `if stack` first.

Think "stack" when **the most recent unfinished thing is the first one you need to finish**: brackets, undo, nested parentheses, "the previous element that…".

### Template: matching pairs (Valid Parentheses)

```python
def is_valid(s):
    partner = {')': '(', ']': '[', '}': '{'}
    stack = []                            # openers still waiting for a closer
    for ch in s:
        if ch in partner:                 # a closer
            if not stack or stack.pop() != partner[ch]:
                return False
        else:                             # an opener
            stack.append(ch)
    return not stack                      # leftover openers = invalid
```

### Template: a stack that carries extra state

Push a **tuple** when each level must remember something, for example `(value, min_so_far)` in Min Stack, or `(saved_result, sign)` when Basic Calculator opens a parenthesis. Popping then restores the old state for free.

```python
def push_with_min(stack, x):
    current_min = min(x, stack[-1][1]) if stack else x
    stack.append((x, current_min))        # stack[-1][1] is always the minimum
```

### Template: stack of indices

Store **indices** instead of characters when you need positions or lengths later.

```python
def unmatched_indices(s):
    stack = []                            # indices of '(' not matched yet
    bad = []                              # indices of ')' with no partner
    for i, ch in enumerate(s):
        if ch == '(':
            stack.append(i)
        elif ch == ')':
            if stack:
                stack.pop()
            else:
                bad.append(i)
    return sorted(bad + stack)            # every bracket that can't be matched
```

## Monotonic stack

A **monotonic stack** keeps its items in sorted order from bottom to top. Before pushing a new item, pop every item that would break the order. **The new item is the answer for each item it pops.**

### Template: next greater element

```python
def next_greater(nums):
    answer = [-1] * len(nums)
    stack = []                            # indices; their values decrease bottom → top
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            answer[stack.pop()] = x       # x is the first bigger value after that index
        stack.append(i)
    return answer                         # next_greater([2, 1, 3]) == [3, 3, -1]
```

It looks like O(n²) because of the inner `while`, but each index is pushed once and popped at most once, so the total work is **O(n)**.

### Template: previous smaller element

Here the answer is read **after** popping: whatever is left on top is the nearest smaller item on the left.

```python
def previous_smaller(nums):
    answer = [-1] * len(nums)
    stack = []                            # indices; their values increase bottom → top
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] >= x:
            stack.pop()
        answer[i] = stack[-1] if stack else -1
        stack.append(i)
    return answer                         # previous_smaller([3, 1, 2]) == [-1, -1, 1]
```

### The four variants

| You want, for each i | Stack order (bottom → top) | Pop while `nums[top]` is… | Answer is |
| --- | --- | --- | --- |
| next greater | decreasing | `< x` | `x`, for each popped index |
| next smaller | increasing | `> x` | `x`, for each popped index |
| previous greater | decreasing | `<= x` | the top after popping |
| previous smaller | increasing | `>= x` | the top after popping |

Tips:
- Store **indices**, not values. You can always look up the value, and distances and widths need indices.
- **Circular array:** loop `i` over `range(2 * n)` and read `nums[i % n]` (Q17).
- **Monotonic deque:** the same pop rule, plus removing from the **front** when an index leaves a window (Q5, see [Primer 03](03-sliding-window.md#monotonic-deque)).

## Expression evaluation

Three tools, from simplest to most general.

### 1. Postfix (Reverse Polish Notation): one stack of numbers

In postfix, each operator comes **after** its two operands (`3 4 +`), so there is no precedence to worry about.

```python
def eval_rpn(tokens):
    stack = []
    for t in tokens:
        if t in {'+', '-', '*', '/'}:
            b = stack.pop()               # the right operand was pushed last
            a = stack.pop()
            if t == '+':
                stack.append(a + b)
            elif t == '-':
                stack.append(a - b)
            elif t == '*':
                stack.append(a * b)
            else:
                stack.append(int(a / b))  # truncate toward zero
        else:
            stack.append(int(t))
    return stack[-1]                      # eval_rpn(["3", "4", "+", "2", "*"]) == 14
```

### 2. Precedence without parentheses: a stack of signed terms

Remember the operator **before** the current number. `+` pushes `num`, `-` pushes `-num`, and `*` / `/` combine with the top **at once**, which is what "higher precedence" means. The answer is `sum(stack)`. (Taught in Q16 step 02.)

### 3. Parentheses: save the state, or use two stacks

- **Save the state:** at `(` push the current state (running result and sign) and start fresh; at `)` pop it and combine. The stack replaces recursion. (Q16 step 03.)
- **Two stacks** (the general method): `nums` holds operands, `ops` holds operators and `(`. Before pushing an operator, apply every operator on `ops` with precedence **≥** the new one, stopping at a `(`. At `)`, apply down to the `(` and remove it. At the end, apply the rest. (Q16 step 04 and target.)

### Truncation toward zero

Python's `//` **floors** (rounds down). Most calculator problems want **truncation toward zero**.

| Expression | `a // b` (floor) | `int(a / b)` | Wanted (truncate) |
| --- | --- | --- | --- |
| `7 / 2` | 3 | 3 | **3** |
| `-7 / 2` | −4 | −3 | **−3** |
| `7 / -2` | −4 | −3 | **−3** |

`int(a / b)` goes through a float. That's exact while `|a|` and `|b|` are below 2⁵³, so it's fine for 32-bit values. For huge integers, use an exact version:

```python
def trunc_div(a, b):
    q = abs(a) // abs(b)
    return q if (a < 0) == (b < 0) else -q
```

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| brackets, "valid", "balanced", "nested" | matching-pairs stack |
| "undo", "most recent", "cancel the last" | plain stack |
| "getMin / getMax in O(1)" on a stack | store extra state with each push |
| "next greater / smaller", "first warmer day", "until a bigger one" | monotonic stack |
| "how far can each bar stretch", histogram, skyline | previous and next smaller (monotonic stack) |
| "circular", "wrap around" plus "next greater" | monotonic stack over `2n` with `i % n` |
| evaluate an expression, "precedence", "without eval", "without recursion" | number stack + operator stack |
| only one bracket type and you only need counts | a counter is enough (O(1) space) |

## Common pitfalls

- Popping or peeking an **empty** stack. Check `if stack` first.
- Forgetting the final check: leftover items (unmatched openers, bars still waiting) need handling **after** the loop.
- Using `<` where `<=` is needed (or the reverse). Duplicates are where this shows up: equal values in Min Stack, equal bars in a histogram.
- Storing values when you later need positions. Store indices.
- Swapping operands: in `a - b` and `a / b`, `b` is the one popped **first**.
- Using `//` when the problem says "truncate toward zero".

## Learn more

- NeetCode roadmap (Stack is the third node): <https://neetcode.io/roadmap>
- Python docs: [Using lists as stacks](https://docs.python.org/3/tutorial/datastructures.html#using-lists-as-stacks) · [collections.deque](https://docs.python.org/3/library/collections.html#collections.deque)
- Visualise stacks: <https://visualgo.net/en/list>
