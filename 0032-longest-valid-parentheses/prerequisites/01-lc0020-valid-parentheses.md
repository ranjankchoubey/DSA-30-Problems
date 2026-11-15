# Step 01 · 20. Valid Parentheses (recap)

[← Roadmap](00-roadmap.md) · [Next: Minimum Add to Make Parentheses Valid →](02-lc0921-minimum-add-to-make-parentheses-valid.md)

> ↩ **Taught in full in the Q20 ladder:** [Min Stack → Step 01 · Valid Parentheses](../../0155-min-stack/prerequisites/01-lc0020-valid-parentheses.md)

> 📚 **New to this?** Primer: [stack basics](../../course/primers/04-stack.md#stack-basics)

## Recap

- Push every opener. A closer must match the **top** of the stack, which is the most recent unmatched opener, so pop it.
- A closer that arrives when the stack is **empty** can never be matched.
- Openers **left on the stack** at the end were never closed.

## Why it matters here

Longest Valid Parentheses uses the same matching, but instead of a yes/no answer it needs to know **where** matching fails. The empty-stack closers and the leftover openers are exactly the places that split the string into valid pieces.
