# Step 03 · 108. Convert Sorted Array to Binary Search Tree (Easy)

[← Previous: Maximum Depth of Binary Tree](02-lc0104-maximum-depth-of-binary-tree.md) · [Roadmap](00-roadmap.md) · [Target: Construct Binary Tree from Preorder and Inorder →](../NOTES.md)

**Practise:** [LeetCode 108](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)
**Skill it adds:** building a tree recursively from an **index range** `[lo, hi]`.

## Problem in brief

You get a strictly increasing array. Build a **height-balanced** BST from it: every value appears once, and at every node the two subtrees differ in height by at most 1. Any valid answer is accepted.

## Key idea

Pick the **middle** value of the range as the root. Everything left of it is smaller, so it builds the left subtree. Everything right of it builds the right subtree. Both halves have about the same size, so the tree is balanced.

Pass the range as two indices `lo, hi` instead of slicing. Slicing copies the array at every level, which costs extra time and memory. An empty range (`lo > hi`) is an empty subtree.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [recursion on trees](../../course/primers/07-trees.md#recursion-on-trees)
3. [LeetCode Editorial](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+convert+sorted+array+to+binary+search+tree)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        def build(lo, hi):                    # builds nums[lo..hi], inclusive
            if lo > hi:
                return None                   # empty range, empty subtree
            mid = (lo + hi) // 2
            root = TreeNode(nums[mid])
            root.left = build(lo, mid - 1)
            root.right = build(mid + 1, hi)
            return root

        return build(0, len(nums) - 1)
```

</details>

**Time:** O(n), one node per call · **Space:** O(log n) for the recursion, since the tree is balanced

## Test cases

| Input | Expected (one valid answer) |
| --- | --- |
| `[-10,-3,0,5,9]` | `[0,-10,5,null,-3,null,9]` (LeetCode's sample `[0,-3,9,-10,null,5]` is also accepted) |
| `[1,3]` | `[1,null,3]` |
| `[0]` | `[0]` |

## Pitfall

Several trees are correct. With `mid = (lo + hi) // 2` you get the lower middle. With `(lo + hi + 1) // 2` you get the upper middle and LeetCode's sample output. Both are accepted.

## What changes in the target

Here the root of a range is always its **middle**. In LC 105 the root of a range is the **next value in preorder**, and its position in inorder splits the range into left and right parts that can have any sizes. A hash map from value to inorder index finds that position in O(1) → [NOTES](../NOTES.md).
