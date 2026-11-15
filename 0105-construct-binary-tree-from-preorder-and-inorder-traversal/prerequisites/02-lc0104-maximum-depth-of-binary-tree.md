# Step 02 · 104. Maximum Depth of Binary Tree (Easy)

[← Previous: Preorder and Inorder Traversal](01-lc0144-lc0094-preorder-and-inorder-traversal.md) · [Roadmap](00-roadmap.md) · [Next: Convert Sorted Array to BST →](03-lc0108-convert-sorted-array-to-bst.md)

**Practise:** [LeetCode 104](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
**Skill it adds:** recursion on trees. Handle `None`, trust the calls on the children, combine their answers.

## Problem in brief

Return the number of nodes on the longest path from the root down to a leaf. An empty tree has depth 0.

## Key idea

Answer three questions:

1. **Base case:** an empty tree has depth `0`.
2. **Ask the children:** `max_depth(node.left)` and `max_depth(node.right)`. Don't trace them in your head. **Trust** that they return the right depth for those subtrees.
3. **Combine:** this node adds one level on top of the deeper child: `1 + max(left, right)`.

For `[3,9,20,null,null,15,7]`, the answers flow **up** from the leaves (a postorder):

| Node | left answer | right answer | returns |
| --- | --- | --- | --- |
| 9 | 0 | 0 | 1 |
| 15 | 0 | 0 | 1 |
| 7 | 0 | 0 | 1 |
| 20 | 1 | 1 | 2 |
| 3 | 1 | 2 | **3** |

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [recursion on trees](../../course/primers/07-trees.md#recursion-on-trees)
3. [LeetCode Editorial](https://leetcode.com/problems/maximum-depth-of-binary-tree/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+maximum+depth+of+binary+tree)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0                              # 1. base case
        left = self.maxDepth(root.left)           # 2. ask the children
        right = self.maxDepth(root.right)
        return 1 + max(left, right)               # 3. combine
```

</details>

**Time:** O(n), each node is visited once · **Space:** O(h) for the recursion (h = height)

## Test cases

| Input | Expected |
| --- | --- |
| `[3,9,20,null,null,15,7]` | `3` |
| `[1,null,2]` | `2` |
| `[]` | `0` |

## Pitfall

Return `0` for `None`, not `1`. Then a leaf gets `1 + max(0, 0) = 1`, which is correct. Starting from 1 counts a level that doesn't exist.

## What changes in the next step

Here each call returns a **number** for its subtree. In step 03 each call returns a **node**: it builds a whole subtree from a slice of an array, described by two indices.
