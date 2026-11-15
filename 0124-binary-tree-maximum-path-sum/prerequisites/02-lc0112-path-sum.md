# Step 02 · 112. Path Sum (Easy)

[← Previous: Maximum Depth (recap)](01-lc0104-maximum-depth-of-binary-tree.md) · [Roadmap](00-roadmap.md) · [Next: Diameter of Binary Tree →](03-lc0543-diameter-of-binary-tree.md)

**Practise:** [LeetCode 112](https://leetcode.com/problems/path-sum/)
**Skill it adds:** carrying a sum along a path by **passing it down** as a parameter.

## Problem in brief

Return `True` if some path from the root down to a **leaf** has values that add up to `targetSum`. An empty tree has no paths.

## Key idea

Pass down "how much is still needed". Each node subtracts its own value. At a **leaf**, the path is complete, so check whether nothing is left. Otherwise ask the children, and either one succeeding is enough.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [recursion on trees](../../course/primers/07-trees.md#recursion-on-trees)
3. [LeetCode Editorial](https://leetcode.com/problems/path-sum/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+path+sum)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False
        remaining = targetSum - root.val
        if not root.left and not root.right:          # leaf: the path ends here
            return remaining == 0
        return (self.hasPathSum(root.left, remaining)
                or self.hasPathSum(root.right, remaining))
```

</details>

**Time:** O(n) · **Space:** O(h)

## Test cases

| Input | Expected |
| --- | --- |
| `root = [5,4,8,11,null,13,4,7,2,null,null,null,1]`, `targetSum = 22` | `true` (5 → 4 → 11 → 2) |
| `root = [1,2,3]`, `targetSum = 5` | `false` (paths sum to 3 and 4) |
| `root = []`, `targetSum = 0` | `false` (no path at all) |

## Pitfall

Check at a **leaf**, not at `None`. If you return `remaining == 0` at `None`, then `root = [1,2]` with `targetSum = 1` returns `True` through the missing right child, even though `1` alone is not a root-to-leaf path.

## What changes in the next step

Here every path goes straight **down** from the root. In step 03 a path can start anywhere and **bend** at a node: up one side and down the other. The value a node passes up is no longer the final answer.
