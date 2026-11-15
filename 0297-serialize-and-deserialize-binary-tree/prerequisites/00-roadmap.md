# Roadmap → Q21 · 297. Serialize and Deserialize Binary Tree (Hard)

**Goal:** turn a binary tree into a string and back, so that the rebuilt tree has exactly the same shape and values, including where children are missing. 0 to 10,000 nodes. Choose a format and justify it.

> 📚 **Before you start:** read [Primer 07 · Trees](../../course/primers/07-trees.md).

## Skills you need

- [ ] Write preorder and inorder traversals
- [ ] Walk a tree level by level with a queue, and read LeetCode's list format
- [ ] Write a tree as text with markers for missing children
- [ ] Read tokens back one at a time with the same recursion that wrote them

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Preorder and Inorder Traversal](01-lc0144-lc0094-preorder-and-inorder-traversal.md) ↩ | Traversals | [LC 144](https://leetcode.com/problems/binary-tree-preorder-traversal/) · [LC 94](https://leetcode.com/problems/binary-tree-inorder-traversal/) |
| 02 | [Binary Tree Level Order Traversal](02-lc0102-binary-tree-level-order-traversal.md) | BFS on a tree (also the test-case input format) | [LC 102](https://leetcode.com/problems/binary-tree-level-order-traversal/) |
| 03 | [Construct String from Binary Tree](03-lc0606-construct-string-from-binary-tree.md) | Tree to string, with markers for missing children | [LC 606](https://leetcode.com/problems/construct-string-from-binary-tree/) |
| ★ | [**Serialize and Deserialize Binary Tree** → NOTES](../NOTES.md) | Preorder with a `#` for every missing child; read it back with the same recursion | [LC 297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) |

## How the steps add up

- Step 01: a traversal lists the values, but on its own it loses the shape (Q24 needed **two** traversals to rebuild a tree).
- Step 02: BFS visits the tree level by level. LeetCode's `[1,2,3,null,null,4,5]` format is exactly BFS with `null` for missing children.
- Step 03: write the tree as a preorder string and mark missing children, so the shape survives.
- **Target:** mark **every** missing child with `#` and separate values with `,`. Then preorder alone is enough, and reading back uses the same recursion as writing.

## Threads

- **Q24** Construct Binary Tree from Preorder and Inorder: rebuilds a tree from traversals **without** null markers, which is why it needs two of them.
- **Q22, Q24**: tree recursion that shares one outer variable (here, the token iterator).

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
