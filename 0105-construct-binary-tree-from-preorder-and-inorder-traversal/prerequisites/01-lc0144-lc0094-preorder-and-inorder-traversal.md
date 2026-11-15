# Step 01 · 144. Preorder and 94. Inorder Traversal (Easy)

[← Roadmap](00-roadmap.md) · [Next: Maximum Depth of Binary Tree →](02-lc0104-maximum-depth-of-binary-tree.md)

**Practise:** [LeetCode 144](https://leetcode.com/problems/binary-tree-preorder-traversal/) · [LeetCode 94](https://leetcode.com/problems/binary-tree-inorder-traversal/)
**Skill it adds:** the depth-first traversal orders, and where the **root** sits in each. Written recursively and with a stack.

## Problem in brief

You get the root of a binary tree. LC 144 wants its values in **preorder**: the node, then its whole left subtree, then its whole right subtree. LC 94 wants them in **inorder**: the left subtree, then the node, then the right subtree.

## Key idea

Both are the **same** depth-first walk. The only difference is **when** you record the node:

| Order | Record the node | Tree `[3,9,20,null,null,15,7]` |
| --- | --- | --- |
| Preorder | before the children | `[3, 9, 20, 15, 7]` |
| Inorder | between the children | `[9, 3, 15, 20, 7]` |
| Postorder | after the children | `[9, 15, 7, 20, 3]` |

Look at the root `3`: preorder puts it **first**. Inorder puts it **between** its left subtree `[9]` and its right subtree `[15, 20, 7]`. The target (LC 105) relies on exactly this.

Without recursion, a **stack** does the call stack's job:
- **Preorder:** pop a node, record it, push its right child and then its left child (so the left one is popped first).
- **Inorder:** walk left as far as possible, pushing each node. Pop one, record it, then move to its right child and repeat.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [tree traversals](../../course/primers/07-trees.md#tree-traversals)
3. [LeetCode Editorial 144](https://leetcode.com/problems/binary-tree-preorder-traversal/editorial/) · [LeetCode Editorial 94](https://leetcode.com/problems/binary-tree-inorder-traversal/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+binary+tree+inorder+traversal)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b> (recursive)</summary>

```python
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        out = []

        def dfs(node):
            if not node:
                return
            out.append(node.val)          # record BEFORE the children
            dfs(node.left)
            dfs(node.right)

        dfs(root)
        return out

    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        out = []

        def dfs(node):
            if not node:
                return
            dfs(node.left)
            out.append(node.val)          # record BETWEEN the children
            dfs(node.right)

        dfs(root)
        return out
```

</details>

<details>
<summary><b>Show solution</b> (iterative, with a stack)</summary>

```python
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        out = []
        stack = [root] if root else []
        while stack:
            node = stack.pop()
            out.append(node.val)
            if node.right:
                stack.append(node.right)  # pushed first, so popped after left
            if node.left:
                stack.append(node.left)
        return out

    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        out = []
        stack = []
        node = root
        while node or stack:
            while node:                   # go left as far as possible
                stack.append(node)
                node = node.left
            node = stack.pop()
            out.append(node.val)          # everything to its left is done
            node = node.right
        return out
```

</details>

**Time:** O(n) · **Space:** O(h) for the recursion or the stack (h = height, up to n for a chain)

## Test cases

| Input | Preorder | Inorder |
| --- | --- | --- |
| `[1,null,2,3]` | `[1,2,3]` | `[1,3,2]` |
| `[3,9,20,null,null,15,7]` | `[3,9,20,15,7]` | `[9,3,15,20,7]` |
| `[]` | `[]` | `[]` |

## Pitfall

In iterative preorder, push **right before left**. A stack is last-in-first-out, so the child pushed last is visited first.

## What changes in the next step

Here the recursion only **collects** values into a list. In step 02 each call **returns** an answer for its subtree, and the parent combines its children's answers.
