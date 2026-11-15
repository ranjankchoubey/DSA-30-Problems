# Step 01 · 104. Maximum Depth of Binary Tree (recap)

[← Roadmap](00-roadmap.md) · [Next: Path Sum →](02-lc0112-path-sum.md)

> ↩ **Taught in full in the Q24 ladder:** [Construct Binary Tree from Preorder and Inorder → Step 02 · Maximum Depth of Binary Tree](../../0105-construct-binary-tree-from-preorder-and-inorder-traversal/prerequisites/02-lc0104-maximum-depth-of-binary-tree.md)

> 📚 **New to this?** Primer: [recursion on trees](../../course/primers/07-trees.md#recursion-on-trees)

## Recap

- Base case: an empty tree (`None`) has depth `0`.
- Ask both children, and trust that the recursive calls return the right answer for their subtrees.
- Combine: `1 + max(left, right)`. O(n) time, O(h) space.

## Why it matters here

Maximum Path Sum uses exactly this postorder shape. Each node gets the best "arm" from each child and combines them. The only changes are what is combined (sums of values instead of a count of levels) and that the overall answer is recorded separately.
