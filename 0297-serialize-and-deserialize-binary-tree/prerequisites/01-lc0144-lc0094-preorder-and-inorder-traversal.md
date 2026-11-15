# Step 01 · 144. Preorder and 94. Inorder Traversal (recap)

[← Roadmap](00-roadmap.md) · [Next: Binary Tree Level Order Traversal →](02-lc0102-binary-tree-level-order-traversal.md)

> ↩ **Taught in full in the Q24 ladder:** [Construct Binary Tree from Preorder and Inorder → Step 01 · Preorder and Inorder Traversal](../../0105-construct-binary-tree-from-preorder-and-inorder-traversal/prerequisites/01-lc0144-lc0094-preorder-and-inorder-traversal.md)

> 📚 **New to this?** Primer: [tree traversals](../../course/primers/07-trees.md#tree-traversals)

## Recap

- Preorder records a node **before** its children (node, left, right). Inorder records it **between** them (left, node, right).
- Recursively it's one DFS, and only the position of `out.append` changes. Iteratively a stack replaces the call stack.
- O(n) time, O(h) space.

## Why it matters here

Preorder writes the root first, so a reader meets each node **before** its children and can create it straight away. That makes preorder a natural save format. But a plain list of values loses the shape: `[1,2]` could be 2 as the left child or the right child of 1. The rest of this ladder fixes that by marking missing children.
