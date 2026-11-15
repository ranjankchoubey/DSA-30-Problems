# Step 02 · 236. Lowest Common Ancestor of a Binary Tree (Medium)

[← Previous: LCA of a BST](01-lc0235-lowest-common-ancestor-of-a-bst.md) · [Roadmap](00-roadmap.md) · [Next: Postorder: count found nodes →](03-concept-postorder-count-found-nodes.md)

**Practise:** [LeetCode 236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
**Skill it adds:** the classic LCA. Ask both subtrees, then combine their answers (postorder).

## Problem in brief

Same question as step 01, but the tree is a plain binary tree with no ordering. Both `p` and `q` are guaranteed to be in the tree.

## Key idea

Let `f(node)` return "the interesting node I found in this subtree": `p`, `q`, their LCA, or `None`.
- If `node` is `None`, `p` or `q`, return `node`.
- Otherwise ask both children. If **both** return something, `p` is on one side and `q` on the other, so `node` is the LCA.
- If only one side returns something, pass it up.

Compare nodes with `is`: the problem hands you the node objects themselves.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [recursion on trees](../../course/primers/07-trees.md#recursion-on-trees)
3. [LeetCode Editorial](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+lowest+common+ancestor+of+a+binary+tree)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if root is None or root is p or root is q:
            return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root                  # p on one side, q on the other
        return left or right             # pass up whatever was found
```

</details>

**Time:** O(n) · **Space:** O(h)

## Test cases

Tree `[3,5,1,6,2,0,8,null,null,7,4]` (the course tree):

| Input | Expected |
| --- | --- |
| `p = 5`, `q = 1` | `3` |
| `p = 5`, `q = 4` | `5` (4 is below 5) |
| `root = [1,2]`, `p = 1`, `q = 2` | `1` |

## Pitfall

The early `return root` when `root is p` **doesn't look below `p`**. That's fine here only because `q` is guaranteed to exist: if `q` isn't on the other side, it must be below `p`. In the course problem `q` might not exist. With the course's TC 3 (`p = 5`, `q = 10`, where 10 is missing) this code returns `5`, but the expected answer is `None`.

## What changes in the next step

We can no longer trust that both nodes exist. Step 03 drops the early return, visits **every** node, and counts how many of `p` and `q` each subtree contains.
