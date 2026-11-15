# Step 01 · 235. Lowest Common Ancestor of a Binary Search Tree (Medium)

[← Roadmap](00-roadmap.md) · [Next: Lowest Common Ancestor of a Binary Tree →](02-lc0236-lowest-common-ancestor-of-a-binary-tree.md)

**Practise:** [LeetCode 235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
**Skill it adds:** what the LCA is, and finding it with the BST ordering.

## Problem in brief

You get a BST and two nodes `p` and `q` that are both in it. Return their **lowest common ancestor**: the deepest node that has both `p` and `q` in its subtree. A node counts as its own ancestor, so if `p` is above `q`, the answer is `p`.

## Key idea

Start at the root and compare both values with the current node:
- both smaller → they're both in the left subtree, so go left;
- both larger → go right;
- otherwise they **split** here (or one of them *is* this node), so this node is the LCA.

No recursion and no extra memory are needed.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [tree basics](../../course/primers/07-trees.md#tree-basics)
3. [LeetCode Editorial](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+lowest+common+ancestor+of+a+binary+search+tree)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        node = root
        while node:
            if p.val < node.val and q.val < node.val:
                node = node.left
            elif p.val > node.val and q.val > node.val:
                node = node.right
            else:
                return node              # p and q split here, or one of them is node
        return None
```

</details>

**Time:** O(h) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `root = [6,2,8,0,4,7,9,null,null,3,5]`, `p = 2`, `q = 8` | `6` |
| `root = [6,2,8,0,4,7,9,null,null,3,5]`, `p = 2`, `q = 4` | `2` (a node is its own ancestor) |
| `root = [2,1]`, `p = 2`, `q = 1` | `2` |

## What changes in the next step

The ordering told us which side `p` and `q` are on. In a plain binary tree there's no ordering, so step 02 has to **search both subtrees** and combine what they report.
