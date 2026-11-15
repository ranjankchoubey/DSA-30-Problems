# Roadmap → Q14 · 32. Longest Valid Parentheses (Hard)

**Goal:** in a string made only of `(` and `)`, return the length of the longest well-formed substring. Solve it with a stack, with DP, and with an O(1)-space two-pass counter, and compare them.

> 📚 **Before you start:** read [Primer 04 · Stack](../../course/primers/04-stack.md).

## Skills you need

- [ ] Match brackets with a stack
- [ ] Track a balance counter and know what it means when it goes negative
- [ ] Use a stack of **indices** to find the brackets that can never be matched
- [ ] Measure a run's length from two indices

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Valid Parentheses](01-lc0020-valid-parentheses.md) ↩ recap | Bracket matching | [LC 20](https://leetcode.com/problems/valid-parentheses/) |
| 02 | [Minimum Add to Make Parentheses Valid](02-lc0921-minimum-add-to-make-parentheses-valid.md) | Balance counter (leads to the O(1) two-pass method) | [LC 921](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/) |
| 03 | [Minimum Remove to Make Valid Parentheses](03-lc1249-minimum-remove-to-make-valid-parentheses.md) | Stack of **indices** to find where valid runs break | [LC 1249](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/) |
| ★ | [**Longest Valid Parentheses** → NOTES](../NOTES.md) | The unmatched brackets split the string into valid pieces; measure the longest | [LC 32](https://leetcode.com/problems/longest-valid-parentheses/) |

## How the steps add up

- Step 01: a `)` matches the most recent unmatched `(`.
- Step 02: with one bracket type, a counter can replace the stack, and a `)` that drives the balance below zero can never be matched.
- Step 03: storing **indices** shows exactly which brackets are unmatched. Everything between two of them is valid.
- **Target:** keep the last unmatched index on the stack as a base and measure `i − base` on the fly (stack). The same idea gives the DP and the two-pass counter.

## Threads

- **Q20** Min Stack: step 01 is taught in full there.
- **Q16** Basic Calculator III: a stack while scanning parentheses.
- **Q11 / Q12** 1-D DP: "the best answer **ending at** index `i`" is the same DP state as here.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
