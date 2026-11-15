# Step 02 · 102. Binary Tree Level Order Traversal (Medium)

[← Previous: Preorder and Inorder (recap)](01-lc0144-lc0094-preorder-and-inorder-traversal.md) · [Roadmap](00-roadmap.md) · [Next: Construct String from Binary Tree →](03-lc0606-construct-string-from-binary-tree.md)

**Practise:** [LeetCode 102](https://leetcode.com/problems/binary-tree-level-order-traversal/)
**Skill it adds:** BFS on a tree with a queue, one level at a time. It is also how LeetCode writes trees as lists.

## Problem in brief

Return the tree's values level by level: a list of lists, where each inner list holds one level's values from left to right.

## Key idea

Use a `deque` as a queue, starting with the root. At the start of each level, `len(queue)` is **exactly** the number of nodes on that level. Pop that many, record their values, and push their children, which form the next level.

**LeetCode's list format is this BFS** with one difference: missing children are written too, as `null`. For `[1,2,3,null,null,4,5]`: 1 is the root, its children are 2 and 3, 2 has `null, null`, and 3 has 4 and 5. Trailing `null`s are dropped. The course's test cases for Q21 use this format.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [level order BFS](../../course/primers/07-trees.md#level-order-bfs)
3. [LeetCode Editorial](https://leetcode.com/problems/binary-tree-level-order-traversal/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+binary+tree+level+order+traversal)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []
        out = []
        queue = deque([root])
        while queue:
            level = []
            for _ in range(len(queue)):        # exactly the nodes on this level
                node = queue.popleft()
                level.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            out.append(level)
        return out
```

</details>

**Time:** O(n) · **Space:** O(w), where w is the widest level (up to about n/2)

## Test cases

| Input | Expected |
| --- | --- |
| `[3,9,20,null,null,15,7]` | `[[3],[9,20],[15,7]]` |
| `[1]` | `[[1]]` |
| `[]` | `[]` |

## Pitfall

Don't use a list with `pop(0)` as the queue. Every `pop(0)` shifts the whole list, which is O(n), so the traversal becomes O(n²). Use `deque.popleft()`, which is O(1).

## What changes in the next step

Here missing children are simply skipped, so you can't rebuild the tree from the output. Step 03 writes the tree as a string that **marks** missing children, so the shape is kept.
