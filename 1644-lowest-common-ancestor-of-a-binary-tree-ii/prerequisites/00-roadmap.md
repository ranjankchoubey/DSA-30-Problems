# Roadmap → Q23 · 1644. Lowest Common Ancestor of a Binary Tree II (Medium)

**Goal:** find the lowest common ancestor (LCA) of nodes `p` and `q` in a binary tree, where `p` or `q` **may not be in the tree**. If either is missing, return `None`. O(n) time.

> 📚 **Before you start:** read [Primer 07 · Trees](../../course/primers/07-trees.md).

## Skills you need

- [ ] Say what "lowest common ancestor" means (a node can be its own ancestor)
- [ ] Use BST ordering to decide which side to search
- [ ] Find the LCA with one postorder pass when both nodes exist
- [ ] Traverse the **whole** tree and count how many targets each subtree holds

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Lowest Common Ancestor of a BST](01-lc0235-lowest-common-ancestor-of-a-bst.md) | LCA using the BST ordering | [LC 235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) |
| 02 | [Lowest Common Ancestor of a Binary Tree](02-lc0236-lowest-common-ancestor-of-a-binary-tree.md) | Classic LCA (assumes both nodes exist) | [LC 236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) |
| 03 | [Postorder: count found nodes](03-concept-postorder-count-found-nodes.md) | Full traversal that counts found nodes, to check both exist | — |
| ★ | [**Lowest Common Ancestor of a Binary Tree II** → NOTES](../NOTES.md) | Count while recording the lowest node that holds both | [LC 1644](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/) 🔒 · free alt [LC 236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) |

## How the steps add up

- Step 01: the LCA is the first node where `p` and `q` **split** into different sides (or one of them is the node itself).
- Step 02: without BST ordering, ask **both** subtrees what they found. The node where both sides report something is the LCA.
- Step 03: step 02 stops early at `p` and so never checks whether `q` exists. Counting found targets over the **whole** tree fixes that.
- **Target:** one postorder pass that **returns** the count and **records** the first node whose count reaches 2.

## Threads

- **Q22** Binary Tree Maximum Path Sum: the same "return one value to the parent, record the answer globally" shape.
- **Q24** and **Q21**: recursion on trees, built up in the Q24 ladder.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
