# Step 04 · Concept: two stacks and operator precedence

[← Previous: Basic Calculator](03-lc0224-basic-calculator.md) · [Roadmap](00-roadmap.md) · [Target: Basic Calculator III →](../NOTES.md)

> 📚 **New to this?** Primer: [expression evaluation](../../course/primers/04-stack.md#expression-evaluation)

**Skill it adds:** evaluating infix with an **operand stack** and an **operator stack** plus a precedence table. No recursion.

## Idea

Read left to right:
- **number** → push onto `nums`
- **operator** → first apply every operator on top of `ops` whose precedence is **≥** the new one, then push the new one
- **end** → apply whatever is left

"Apply" means: pop one operator and two numbers (right operand first), push the result.

Why `≥` and not `>`: operators of equal precedence must run left to right. In `8-3-2`, the first `−` must run before the second is pushed, giving `(8−3)−2 = 3`, not `8−(3−2) = 7`.

## Code

```python
PREC = {'+': 1, '-': 1, '*': 2, '/': 2}


def apply_top(nums, ops):
    op = ops.pop()
    b = nums.pop()                        # right operand
    a = nums.pop()
    if op == '+':
        nums.append(a + b)
    elif op == '-':
        nums.append(a - b)
    elif op == '*':
        nums.append(a * b)
    else:
        nums.append(int(a / b))           # truncate toward zero


def calculate_two_stacks(s):
    nums, ops = [], []
    i = 0
    while i < len(s):
        ch = s[i]
        if ch.isdigit():
            num = 0
            while i < len(s) and s[i].isdigit():
                num = num * 10 + int(s[i])
                i += 1
            nums.append(num)
            continue
        if ch in PREC:
            while ops and PREC[ops[-1]] >= PREC[ch]:
                apply_top(nums, ops)
            ops.append(ch)
        i += 1                            # spaces are skipped
    while ops:
        apply_top(nums, ops)
    return nums[-1]
```

**Time:** O(n), since each operator is pushed and applied once · **Space:** O(n)

## Walk-through: `"3+2*2-1"` → `6`

| Token | Action | nums | ops |
| --- | --- | --- | --- |
| `3` | push | [3] | [] |
| `+` | nothing to apply | [3] | [+] |
| `2` | push | [3, 2] | [+] |
| `*` | `+` is lower, keep it | [3, 2] | [+, *] |
| `2` | push | [3, 2, 2] | [+, *] |
| `-` | apply `*` → 4, apply `+` → 7 | [7] | [-] |
| `1` | push | [7, 1] | [-] |
| end | apply `-` → 6 | [6] | [] |

## Test cases

| Input | Expected |
| --- | --- |
| `"3+2*2"` | `7` |
| `" 3/2 "` | `1` |
| `"8-3-2"` | `3` (left to right) |

## What changes in the target

Add parentheses:
- `(` is pushed onto `ops` as a **wall**: the "apply while `≥`" loop stops when it reaches a `(`.
- `)` applies operators down to the wall, then removes the `(`.

Then support unary minus as a one-operand operator with the highest precedence. Still no recursion → [NOTES](../NOTES.md).
