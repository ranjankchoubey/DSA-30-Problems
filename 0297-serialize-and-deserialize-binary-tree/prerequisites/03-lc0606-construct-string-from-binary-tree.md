# Step 03 · 606. Construct String from Binary Tree (Medium)

[← Previous: Level Order Traversal](02-lc0102-binary-tree-level-order-traversal.md) · [Roadmap](00-roadmap.md) · [Target: Serialize and Deserialize Binary Tree →](../NOTES.md)

**Practise:** [LeetCode 606](https://leetcode.com/problems/construct-string-from-binary-tree/)
**Skill it adds:** turning a tree into a string with **markers for missing children**, so the shape is kept.

## Problem in brief

Write the tree in preorder as a string: a node's value, then its left subtree in parentheses, then its right subtree in parentheses. Leave out empty parentheses, **except** keep `()` for a missing left child when there is a right child. Otherwise you couldn't tell a lone right child from a lone left child.

## Key idea

At each node, write the value, then decide on the two groups:

| Node has | Write |
| --- | --- |
| no children | just the value |
| left only | `val(left)` |
| right only | `val()(right)`, where `()` marks the missing left |
| both | `val(left)(right)` |

So the left group is written whenever the node has **any** child, and the right group only when the right child exists. Build the string from a list of parts and join once at the end. Repeated `+` on strings copies them at every level.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [tree traversals](../../course/primers/07-trees.md#tree-traversals)
3. [LeetCode Editorial](https://leetcode.com/problems/construct-string-from-binary-tree/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+construct+string+from+binary+tree)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def tree2str(self, root: Optional[TreeNode]) -> str:
        parts = []

        def dfs(node):
            parts.append(str(node.val))
            if node.left or node.right:           # left group needed (maybe empty "()")
                parts.append("(")
                if node.left:
                    dfs(node.left)
                parts.append(")")
            if node.right:
                parts.append("(")
                dfs(node.right)
                parts.append(")")

        if root:
            dfs(root)
        return "".join(parts)
```

</details>

**Time:** O(n) · **Space:** O(n) for the output, O(h) for the recursion

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,3,4]` | `"1(2(4))(3)"` |
| `[1,2,3,null,4]` | `"1(2()(4))(3)"` (the `()` says 4 is a **right** child) |
| `[1,null,2]` | `"1()(2)"` |

## What changes in the target

LC 606 leaves out some markers to keep the string short, so reading it back needs bracket matching. For Q21, mark **every** missing child with `#` and separate tokens with `,`. For example, `[1,null,2]` becomes `1,#,2,#,#`. Then a reader can rebuild the tree with the same preorder recursion, taking one token at a time → [NOTES](../NOTES.md).
