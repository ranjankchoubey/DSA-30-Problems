# Roadmap → Q16 · 772. Basic Calculator III (Hard) 🔒

**Goal:** evaluate a string with non-negative integers, `+ - * /`, parentheses and spaces. Integer division truncates toward zero. No `eval` and no recursion: use stacks. Explain how precedence is handled and how unary minus would be supported.

> 📚 **Before you start:** read [Primer 04 · Stack](../../course/primers/04-stack.md).

> 🔒 LC 772 is Premium. Steps 02 and 03 are its free halves ([LC 227](https://leetcode.com/problems/basic-calculator-ii/) and [LC 224](https://leetcode.com/problems/basic-calculator/)). The target code in NOTES passes both.

## Skills you need

- [ ] Evaluate with a stack of numbers
- [ ] Do `* /` before `+ -` (precedence), and truncate division toward zero
- [ ] Save and restore state at parentheses with a stack instead of recursion
- [ ] Keep an operand stack and an operator stack, and know when to apply an operator

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Evaluate Reverse Polish Notation](01-lc0150-evaluate-reverse-polish-notation.md) | Evaluating with a stack | [LC 150](https://leetcode.com/problems/evaluate-reverse-polish-notation/) |
| 02 | [Basic Calculator II](02-lc0227-basic-calculator-ii.md) | `* /` precedence (no parentheses); truncation toward zero | [LC 227](https://leetcode.com/problems/basic-calculator-ii/) |
| 03 | [Basic Calculator](03-lc0224-basic-calculator.md) | Parentheses with `+ -` (push the context onto the stack) | [LC 224](https://leetcode.com/problems/basic-calculator/) |
| 04 | [Two stacks and operator precedence](04-concept-two-stacks-operator-precedence.md) | Operand and operator stacks, no recursion | — |
| ★ | [**Basic Calculator III** → NOTES](../NOTES.md) | `(` as a wall on the operator stack; unary minus | [LC 772](https://leetcode.com/problems/basic-calculator-iii/) 🔒 |

## How the steps add up

- Step 01: in postfix, an operator takes the top two numbers. No precedence, no parentheses.
- Step 02: infix with precedence. `*` and `/` act at once on the previous term; `+` and `−` wait on the stack.
- Step 03: parentheses with only `+ −`. At `(` push the current state, at `)` pop it and combine.
- Step 04: the general method. A number stack and an operator stack, where an operator is applied when one of lower or equal precedence arrives.
- **Target:** add `(` and `)` to step 04: `(` is a wall on the operator stack, and `)` applies everything down to the wall. Unary minus becomes a high-precedence one-operand operator.

## Threads

- **Q14** Longest Valid Parentheses and **Q20** Min Stack: stacks for brackets and for saved state.
- **Q21** Serialize and Deserialize Binary Tree: another parser that walks a string token by token.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
