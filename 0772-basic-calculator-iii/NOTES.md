# 0772. Basic Calculator III

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q16 | [772](https://leetcode.com/problems/basic-calculator-iii/) 🔒 | Hard | Stack | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0772-basic-calculator-iii.py` (save it yourself, see the 🔒 note below)

> 🔒 **LeetCode Premium:** LeetHub can't sync this without Premium. Save your solution yourself as `0772-basic-calculator-iii/0772-basic-calculator-iii.py`, or practise the free alternative: [224. Basic Calculator](https://leetcode.com/problems/basic-calculator/) + [227. Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/).

## 1. Case scenario

**Scientific Calculator Engine:** evaluate a string with non-negative integers, `+ - * /`, parentheses and spaces. Integer division truncates toward zero. No `eval` and no recursion: use stacks. Explain how operator precedence is handled and how unary minus would be supported. `1 ≤ |s| ≤ 100,000`; the expression is always valid; no division by zero.

## 2. Pattern recognition

- **Pattern:** Stack, expression evaluation
- **Signals in the statement:** "precedence", "parentheses", "without eval", "**without recursion** (use stacks)".
- **Core idea in one line:** a number stack and an operator stack. Apply an operator when a new one of lower or equal precedence arrives, at `)` and at the end, and treat `(` as a wall the operator loop can't pass.

## 3. Approaches

### Approach 1: `eval`, or recursive descent (not allowed)

`eval` is banned, and it's unsafe on untrusted input. A recursive-descent parser (one function per precedence level, recursing at `(`) is natural and O(n), but its call depth grows with nesting. 50,000 nested `(` would overflow Python's default recursion limit (about 1,000). That's why the doc asks for stacks.

### Approach 2: Evaluate the innermost parentheses and splice

Find an innermost `( … )`, evaluate it with step 02's method, replace it with its value, and repeat. **Time:** O(n²) in the worst case (deep nesting rebuilds the string each time) · **Space:** O(n)

### Approach 3: One stack of saved frames (steps 02 + 03 combined)

Run step 02's "signed terms" method. At `(`, save the current `(stack, op)` on a second stack and start a fresh frame. At `)`, the inner value is `sum(stack)`; restore the outer frame and treat that value as the current number. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show the saved-frames solution</b></summary>

```python
class Solution:
    def calculate(self, s: str) -> int:
        def push_term(stack, op, num):
            if op == '+':
                stack.append(num)
            elif op == '-':
                stack.append(-num)
            elif op == '*':
                stack.append(stack.pop() * num)
            else:
                stack.append(int(stack.pop() / num))

        stack, num, op = [], 0, '+'
        saved = []                        # (stack, op) of each open '('
        for ch in s + '+':                # the trailing '+' flushes the last number
            if ch.isdigit():
                num = num * 10 + int(ch)
            elif ch == '(':
                saved.append((stack, op))
                stack, num, op = [], 0, '+'
            elif ch == ')':
                push_term(stack, op, num)
                num = sum(stack)          # value of the parenthesis
                stack, op = saved.pop()
            elif ch in '+-*/':
                push_term(stack, op, num)
                num, op = 0, ch
        return sum(stack)
```

</details>

### Approach 4: Operand and operator stacks (optimal)

Step 04's two stacks, plus `(` as a wall and `)` that applies down to it. Unary minus is an extra operator, `'neg'`, with the highest precedence and one operand. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def calculate(self, s: str) -> int:
        PREC = {'+': 1, '-': 1, '*': 2, '/': 2, 'neg': 3}
        nums, ops = [], []

        def apply_top():
            op = ops.pop()
            if op == 'neg':                       # unary minus: one operand
                nums.append(-nums.pop())
                return
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

        i, n = 0, len(s)
        expect_operand = True                     # at the start, after '(' and after an operator
        while i < n:
            ch = s[i]
            if ch.isdigit():
                num = 0
                while i < n and s[i].isdigit():
                    num = num * 10 + int(s[i])
                    i += 1
                nums.append(num)
                expect_operand = False
                continue
            if ch == '(':
                ops.append('(')
                expect_operand = True
            elif ch == ')':
                while ops[-1] != '(':
                    apply_top()
                ops.pop()                         # remove the wall
                expect_operand = False
            elif ch in '+-' and expect_operand:   # unary sign
                if ch == '-':
                    ops.append('neg')
            elif ch in '+-*/':
                while ops and ops[-1] != '(' and PREC[ops[-1]] >= PREC[ch]:
                    apply_top()
                ops.append(ch)
                expect_operand = True
            i += 1                                # spaces fall through to here
        while ops:
            apply_top()
        return nums[-1]
```

</details>

## 4. Why it works

- **Precedence:** an operator waits on `ops` until we know its right operand is complete. That happens when an operator of **lower or equal** precedence arrives, at `)`, or at the end. A higher-precedence operator arriving later (`*` after `+`) is pushed on top and therefore applied **first**.
- **Left to right for equal precedence:** using `≥` applies `8−3` before pushing the second `−` in `8−3−2`.
- **Parentheses without recursion:** `(` sits on `ops` as a wall. Operators pushed after it are applied before anything below it, and `)` applies everything down to the wall, leaving the parenthesis's value as one number on `nums`. The two stacks hold exactly what a recursive parser would keep in its call frames.
- **Unary minus:** a `−` is unary when an operand is expected (start, after `(`, after another operator). It becomes `'neg'`, which takes one operand and has the **highest** precedence, so `-2*3` is `(−2)×3` and `2*-3` is `2×(−3)`. It is pushed without applying anything first, because a prefix operator has no left operand to finish.
- **Truncation:** `int(a / b)` rounds toward zero (`−7 / 2 → −3`). Python's `//` would floor to `−4`.
- **O(n):** each number is pushed once, and each operator is pushed and applied once.

## 5. Dry run: TC 2, `s = "2*(5+5*2)/3+(6/2+8)"` → `21`

| Token | Action | nums | ops |
| --- | --- | --- | --- |
| `2` | push | [2] | [] |
| `*` | push | [2] | [*] |
| `(` | wall | [2] | [*, (] |
| `5` | push | [2, 5] | [*, (] |
| `+` | stop at wall, push | [2, 5] | [*, (, +] |
| `5` | push | [2, 5, 5] | [*, (, +] |
| `*` | `+` is lower, push | [2, 5, 5] | [*, (, +, *] |
| `2` | push | [2, 5, 5, 2] | [*, (, +, *] |
| `)` | apply `*` → 10, apply `+` → 15, remove `(` | [2, 15] | [*] |
| `/` | apply `*` → 30, push | [30] | [/] |
| `3` | push | [30, 3] | [/] |
| `+` | apply `/` → 10, push | [10] | [+] |
| `(` | wall | [10] | [+, (] |
| `6` `/` `2` | push, push, push | [10, 6, 2] | [+, (, /] |
| `+` | apply `/` → 3, push | [10, 3] | [+, (, +] |
| `8` | push | [10, 3, 8] | [+, (, +] |
| `)` | apply `+` → 11, remove `(` | [10, 11] | [+] |
| end | apply `+` → 21 | [**21**] | [] |

## 6. Edge cases & pitfalls

- **Multi-digit numbers:** read all digits before pushing (`"100"` is one number).
- **Spaces** can appear anywhere; skip them.
- **Truncation toward zero** matters once a value is negative: `(1-8)/2` is `−3`, not `−4`.
- **Operand order:** pop `b` first, then `a`, and compute `a op b`.
- **The wall:** the precedence loop must stop at `(`; otherwise `2*(3+4)` would apply `*` to `2` and `3`.
- **Unary minus after an operator** (`2*-3`): the `0 − x` trick (push a 0 first) is only safe at the start or right after `(`. Here it would compute `(2*0)−3`. A separate `'neg'` operator is safe everywhere.
- `int(a / b)` uses a float, exact while values stay below 2⁵³ (fine for 32-bit results). For unbounded integers, use the primer's `trunc_div`.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `s = "3+2*2"` | `7` |
| TC 2 | `s = "2*(5+5*2)/3+(6/2+8)"` | `21` |
| TC 3 | `s = "(2+6*3+5-(3*14/7+2)*5)+3"` | `-12` |

## 8. Follow-up questions (from course doc)

- **Q: Evaluate it without `eval` and without recursion.**
  **A:** Approach 4 (or Approach 3) uses only explicit stacks. Nesting depth costs stack entries on the heap, not call frames, so 50,000 nested parentheses are fine.
- **Q: Explain operator precedence handling.**
  **A:** Each operator has a level (`+ −` = 1, `* /` = 2). Before pushing a new operator, apply every operator on top of `ops` with level **≥** the new one, stopping at `(`. So a waiting `*` is applied before a new `+` is pushed, while a waiting `+` stays put when `*` arrives (the `*` goes on top and runs first). `≥` rather than `>` gives left-to-right order within a level. Parentheses override everything because the loop can't pass the `(` wall. See §4 and the §5 dry run.
- **Q: How would unary minus be supported?**
  **A:** Decide from context: a `−` is **unary** when an operand is expected, meaning at the start, right after `(`, or right after another operator. Otherwise it is binary.
  - **Simple version** (enough for LC 224, where unary `−` only appears at the start or before `(`): push a `0` onto `nums` first, so `-x` becomes `0 − x`. Approach 3 gets this for free.
  - **General version** (used in Approach 4): push a one-operand operator `'neg'` with the **highest** precedence. It applies to the next complete operand: `-2*3 = −6`, `2*-3 = −6`, `-(2+3) = −5`, `--4 = 4`, `1--(2) = 3`. The `0 − x` trick gets `2*-3` wrong, because it computes `(2×0) − 3`.
  - A unary `+` can simply be skipped.

## 9. Threads

- **Q14** Longest Valid Parentheses and **Q20** Min Stack: stacks for brackets and saved state.
- **Q21** Serialize and Deserialize Binary Tree: another token-by-token parser.
- Step 01's postfix form is what Approach 4 effectively produces: each `apply_top` call is one postfix operator.

## 10. One-line revision

> Number stack + operator stack: before pushing an operator, apply those on top with precedence ≥ it (stop at `(`); `)` applies down to `(`; unary minus is a top-precedence `neg`; divide with `int(a / b)`.

<!-- course-nav:start -->

---

### Course navigation

**Problem 14 of 30** · Stack (5 of 5)

[← Previous: Q14 · Longest Valid Parentheses](../0032-longest-valid-parentheses/NOTES.md) · [Course index](../INDEX.md) · [Next: Q3 · Median of Two Sorted Arrays →](../0004-median-of-two-sorted-arrays/prerequisites/00-roadmap.md)

> 🧭 **Next pattern: Binary Search.** Read [05 · Binary Search primer](../course/primers/05-binary-search.md) before starting Q3.

<!-- course-nav:end -->
