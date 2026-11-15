# Step 04 · Concept: drop negative branches

[← Previous: Diameter of Binary Tree](03-lc0543-diameter-of-binary-tree.md) · [Roadmap](00-roadmap.md) · [Target: Binary Tree Maximum Path Sum →](../NOTES.md)

> 📚 **New to this?** Primer: [recursion on trees](../../course/primers/07-trees.md#recursion-on-trees)

**Skill it adds:** `max(0, gain)`. An arm whose best sum is negative is better left out.

## Idea

Let `gain(node)` be the best sum of a **downward** path that starts at `node`. It must include `node` but may stop anywhere below it. The node can continue into its left arm, its right arm, or **neither**. Continuing only helps if the arm adds something positive:

`gain(node) = node.val + max(0, gain(left), gain(right))`

Writing `left = max(0, gain(node.left))` makes "neither" automatic: a negative arm counts as 0, which means "stop here".

## Code

```python
def max_gain(node):
    """Best sum of a downward path that starts at node (it may stop anywhere)."""
    if not node:
        return 0
    left = max(0, max_gain(node.left))       # a losing arm counts as 0 = "don't go there"
    right = max(0, max_gain(node.right))
    return node.val + max(left, right)
```

**Time:** O(n) · **Space:** O(h)

## Walk-through: `[-10,9,20,null,null,15,7]`

| Node | left (after max 0) | right (after max 0) | returns |
| --- | --- | --- | --- |
| 9 | 0 | 0 | 9 |
| 15 | 0 | 0 | 15 |
| 7 | 0 | 0 | 7 |
| 20 | 15 | 7 | 20 + 15 = 35 |
| −10 | 9 | 35 | −10 + 35 = **25** |

## Test cases

| Input | Expected `max_gain(root)` |
| --- | --- |
| `[-10,9,20,null,null,15,7]` | `25` (−10 → 20 → 15) |
| `[2,-1,-3]` | `2` (both arms dropped) |
| `[-3]` | `-3` (the node itself can't be dropped) |

## What changes in the target

This path only goes **down** from a node. The target's path can bend, exactly like the diameter in step 03. So at every node:
- **record** `node.val + left + right` (both arms, each already `max(0, ·)`) as a candidate for the global best;
- **return** `node.val + max(left, right)` (one arm) to the parent.

Notice that −10 returns 25 here, but the best path in this tree is 15 → 20 → 7 = **42**, which bends at 20. Only the recorded value can find it → [NOTES](../NOTES.md).
