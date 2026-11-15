# Roadmap → Q24 · 105. Construct Binary Tree from Preorder and Inorder Traversal (Medium)

**Goal:** given the preorder and inorder traversals of a tree with **unique** values, rebuild the original tree in O(n) time.

> 📚 **Before you start:** read [Primer 07 · Trees](../../course/primers/07-trees.md).

## Skills you need

- [ ] Write preorder and inorder traversals, both recursively and with a stack
- [ ] Solve a tree problem as "base case, ask the children, combine"
- [ ] Build a subtree recursively from an index range `[lo, hi]`
- [ ] Find a value's position in O(1) with a hash map

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Preorder and Inorder Traversal](01-lc0144-lc0094-preorder-and-inorder-traversal.md) | Traversal orders: where the root sits in each | [LC 144](https://leetcode.com/problems/binary-tree-preorder-traversal/) · [LC 94](https://leetcode.com/problems/binary-tree-inorder-traversal/) |
| 02 | [Maximum Depth of Binary Tree](02-lc0104-maximum-depth-of-binary-tree.md) | Recursion on trees | [LC 104](https://leetcode.com/problems/maximum-depth-of-binary-tree/) |
| 03 | [Convert Sorted Array to BST](03-lc0108-convert-sorted-array-to-bst.md) | Build a tree recursively from index ranges | [LC 108](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/) |
| ★ | [**Construct Binary Tree from Preorder and Inorder** → NOTES](../NOTES.md) | Preorder gives the root, inorder splits it into left and right; a hash map makes it O(n) | [LC 105](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) |

## How the steps add up

- Step 01: preorder lists the root **first**. Inorder puts the root **between** its left subtree and its right subtree.
- Step 02: a tree function handles `None`, trusts the recursive calls on the children, and combines their answers.
- Step 03: make the middle of a range the root, then build the left half and the right half the same way.
- **Target:** the same range recursion, but the root is the **next value in preorder**, and its position in inorder (found with a hash map) decides where the range splits.

## Threads

- **Q21** Serialize and Deserialize Binary Tree rebuilds a tree from a traversal again. With null markers, preorder alone is enough.
- **Q1, Q6, Q13** also pair a hash map with another structure for O(1) lookups.
- **Q22, Q21** use the same "recursion plus one shared variable" shape (here the shared variable is the preorder pointer).

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
