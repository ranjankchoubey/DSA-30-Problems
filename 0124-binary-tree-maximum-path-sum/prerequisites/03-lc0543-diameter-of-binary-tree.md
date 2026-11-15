# Step 03 · 543. Diameter of Binary Tree (Easy)

[← Previous: Path Sum](02-lc0112-path-sum.md) · [Roadmap](00-roadmap.md) · [Next: Drop negative branches →](04-concept-drop-negative-branches.md)

**Practise:** [LeetCode 543](https://leetcode.com/problems/diameter-of-binary-tree/)
**Skill it adds:** the helper **returns** one value to its parent but **records** a different value as the global answer.

## Problem in brief

Return the number of **edges** on the longest path between any two nodes. The path doesn't have to pass through the root.

## Key idea

Every path has one highest node where it **bends**. The longest path bending at `node` is `height(left) + height(right)` edges, where `height` counts nodes on the longest downward path (`None` → 0).

But the parent can't use that bent path, because it already uses both of `node`'s arms. The parent can only extend **one** arm. So the helper does two different things:

| | Value | Why |
| --- | --- | --- |
| **Records** in `best` | `left + right` | the best path that bends at this node |
| **Returns** to the parent | `1 + max(left, right)` | the longest single arm the parent can extend |

For `[1,2,3,4,5]`:

| Node | left | right | records `left + right` | returns |
| --- | --- | --- | --- | --- |
| 4 | 0 | 0 | 0 | 1 |
| 5 | 0 | 0 | 0 | 1 |
| 2 | 1 | 1 | 2 | 2 |
| 3 | 0 | 0 | 0 | 1 |
| 1 | 2 | 1 | **3** | 3 |

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [return one value, record another](../../course/primers/07-trees.md#return-one-value-record-another)
3. [LeetCode Editorial](https://leetcode.com/problems/diameter-of-binary-tree/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+diameter+of+binary+tree)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        best = 0

        def height(node):
            nonlocal best
            if not node:
                return 0
            left = height(node.left)
            right = height(node.right)
            best = max(best, left + right)    # RECORD: path bending here
            return 1 + max(left, right)       # RETURN: one arm for the parent

        height(root)
        return best
```

</details>

**Time:** O(n) · **Space:** O(h)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,3,4,5]` | `3` (4 → 2 → 1 → 3) |
| `[1,2]` | `1` |
| `[1,2,null,3,4,5,null,null,6]` | `4` (5 → 3 → 2 → 4 → 6, which skips the root) |

## Pitfall

Returning `left + right` to the parent would let it build a "path" that goes down both arms of a child and then up, which revisits a node. Return one arm, record both.

## What changes in the next step

Here every node adds `+1`, so a longer arm is always better. In Q22 each node adds its **value**, which can be negative, so an arm can make a path **worse**. Step 04 shows when to drop an arm.
