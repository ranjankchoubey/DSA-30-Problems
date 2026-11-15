# Step 03 · Concept: postorder that counts found nodes

[← Previous: LCA of a Binary Tree](02-lc0236-lowest-common-ancestor-of-a-binary-tree.md) · [Roadmap](00-roadmap.md) · [Target: Lowest Common Ancestor of a Binary Tree II →](../NOTES.md)

> 📚 **New to this?** Primer: [recursion on trees](../../course/primers/07-trees.md#recursion-on-trees)

**Skill it adds:** a full traversal (no early return) that tells you how many of `p` and `q` are in each subtree, so you can **prove** both exist.

## Idea

`count(node)` = how many of `p` and `q` are in `node`'s subtree = the left count + the right count + 1 if `node` is `p` + 1 if `node` is `q`.

It's a postorder: a node needs both children's counts first. Two facts follow:
- If the **root's** count is less than 2, at least one of `p`, `q` is missing.
- Going bottom-up, the **first** node whose count reaches 2 is the LCA. Every node above it also has count 2, but it's higher up.

## Code

```python
def count_found(root, p, q):
    """How many of p and q are in root's subtree (0, 1 or 2)."""
    if root is None:
        return 0
    left = count_found(root.left, p, q)
    right = count_found(root.right, p, q)
    return left + right + (root is p) + (root is q)
```

**Time:** O(n), every node is visited, even after `p` is found · **Space:** O(h)

## Walk-through: course tree `[3,5,1,6,2,0,8,null,null,7,4]`, `p = 7`, `q = 4`

Postorder visits the nodes in this order:

| Node | left count | right count | is p or q? | count |
| --- | --- | --- | --- | --- |
| 6 | 0 | 0 | no | 0 |
| 7 | 0 | 0 | p | 1 |
| 4 | 0 | 0 | q | 1 |
| 2 | 1 | 1 | no | **2** ← first node with 2, the LCA |
| 5 | 0 | 2 | no | 2 |
| 0 | 0 | 0 | no | 0 |
| 8 | 0 | 0 | no | 0 |
| 1 | 0 | 0 | no | 0 |
| 3 | 2 | 0 | no | 2 |

With `p = 5` and `q = 10` (10 is not in the tree), only node 5 matches, so the root's count is **1** and the answer must be `None`.

## Test cases

Course tree `[3,5,1,6,2,0,8,null,null,7,4]`:

| Input | Expected `count_found(root, p, q)` |
| --- | --- |
| `p = 5`, `q = 1` | `2` |
| `p = 7`, `q = 4` | `2` |
| `p = 5`, `q = 10` (not in the tree) | `1` |

## Why no early return

Step 02 returned as soon as it reached `p`. Here, with `p = 5` and `q = 4`, node 4 sits **below** node 5. Stopping at 5 would never see it, and you couldn't tell "4 is below 5" apart from "4 doesn't exist".

## What changes in the target

Do both jobs in the same pass: **return** the count to the parent, and **record** the first node whose count reaches 2. At the end, that recorded node is the answer, or `None` if no count ever reached 2 → [NOTES](../NOTES.md).
