# Roadmap → Q20 · 155. Min Stack (Medium)

**Goal:** design a stack with `push`, `pop`, `top` and `getMin`, **all in O(1)**. Then cut the memory used when most pushes are bigger than the current minimum.

> 📚 **Before you start:** read [Primer 04 · Stack](../../course/primers/04-stack.md).

## Skills you need

- [ ] Push, pop and peek with a Python list
- [ ] Keep extra state in step with every push and pop
- [ ] Remember "the minimum at this depth", so a pop brings back the old minimum instantly

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Valid Parentheses](01-lc0020-valid-parentheses.md) | Stack basics: push, pop, peek, empty check | [LC 20](https://leetcode.com/problems/valid-parentheses/) |
| 02 | [Baseball Game](02-lc0682-baseball-game.md) | Stack operations that carry extra state | [LC 682](https://leetcode.com/problems/baseball-game/) |
| 03 | [Store the min with each push](03-concept-store-min-with-each-push.md) | `(value, min_so_far)` pairs | — |
| ★ | [**Min Stack** → NOTES](../NOTES.md) | A second stack that only grows on a new low, or one stack of differences | [LC 155](https://leetcode.com/problems/min-stack/) |

## How the steps add up

- Step 01: the top of the stack is the most recent unfinished item, and LIFO order undoes things in reverse.
- Step 02: each pushed value is **computed** from the top, and a pop undoes it with no recalculation.
- Step 03: compute the minimum at push time and store it next to the value. A pop brings back the old minimum.
- **Target:** store a minimum only when it changes (second stack), or store each value as a difference from the minimum (no second stack at all).

## Threads

- **Q14** Longest Valid Parentheses starts from step 01's bracket matching.
- **Q16** Basic Calculator III pushes saved state onto a stack, like step 03.
- **Q13** LRU Cache is another design class where every operation must be O(1).

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
