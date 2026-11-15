# 0105. Construct Binary Tree from Preorder and Inorder Traversal

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q24 | [105](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | Medium | Trees | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0105-construct-binary-tree-from-preorder-and-inorder-traversal.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Rebuilding an Org Chart:** a backup system saved the org chart (a binary tree) as two lists, its preorder and its inorder traversal. All values are unique and there are up to 3000 nodes. Rebuild the original tree and return its root.

## 2. Pattern recognition

- **Pattern:** Trees, recursive construction plus a hash map
- **Signals in the statement:** "preorder and inorder", "rebuild the tree", "all values unique". Unique values mean every value has exactly one position in inorder, so a hash map can find it.
- **Core idea in one line:** the next preorder value is the root; its index in inorder splits the remaining values into the left subtree and the right subtree.

## 3. Approaches

### Approach 1: Recursion with slicing and `list.index`

Take `preorder[0]` as the root, find it with `inorder.index`, slice both lists into left and right parts, and recurse. Easy to write, but each call does a linear search and copies slices. **Time:** O(n²) on a chain · **Space:** O(n²) on a chain, for the slices

<details>
<summary><b>Show slicing solution</b></summary>

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder:
            return None
        root = TreeNode(preorder[0])
        mid = inorder.index(root.val)               # O(n) search
        root.left = self.buildTree(preorder[1:mid + 1], inorder[:mid])
        root.right = self.buildTree(preorder[mid + 1:], inorder[mid + 1:])
        return root
```

</details>

### Approach 2: Hash map + preorder pointer + index range (optimal)

Store `value → inorder index` in a dict once. Walk preorder with one pointer `nxt`. Each call gets an inorder range `[lo, hi]` and builds the subtree made of exactly those values. **Time:** O(n) · **Space:** O(n) for the map, plus O(h) recursion

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        pos = {v: i for i, v in enumerate(inorder)}   # value -> inorder index
        nxt = 0                                       # index of the next root in preorder

        def build(lo, hi):                            # subtree made of inorder[lo..hi]
            nonlocal nxt
            if lo > hi:
                return None
            root = TreeNode(preorder[nxt])
            nxt += 1
            mid = pos[root.val]
            root.left = build(lo, mid - 1)            # left MUST be built before right
            root.right = build(mid + 1, hi)
            return root

        return build(0, len(inorder) - 1)
```

</details>

## 4. Why it works

- Preorder is laid out as **root, then the whole left subtree, then the whole right subtree**. So the first unused preorder value is always the root of the subtree being built.
- In inorder, everything left of the root's index belongs to its left subtree and everything right of it belongs to its right subtree. So `[lo, mid − 1]` and `[mid + 1, hi]` are exactly the two subtrees' values.
- Building the left subtree **completely** before the right one consumes preorder values in the same order preorder lists them. When `build(lo, mid − 1)` returns, `nxt` points at the right subtree's root.
- An empty range (`lo > hi`) means "no child here", which is how the recursion knows where the tree's `None`s go.

## 5. Dry run: TC 1, `preorder = [3,9,20,15,7]`, `inorder = [9,3,15,20,7]`

`pos = {9: 0, 3: 1, 15: 2, 20: 3, 7: 4}`

| Call | `nxt` before | root | `mid` (inorder index) | left range | right range |
| --- | --- | --- | --- | --- | --- |
| build(0, 4) | 0 | 3 | 1 | [0, 0] | [2, 4] |
| build(0, 0) | 1 | 9 | 0 | [0, −1] → None | [1, 0] → None |
| build(2, 4) | 2 | 20 | 3 | [2, 2] | [4, 4] |
| build(2, 2) | 3 | 15 | 2 | [2, 1] → None | [3, 2] → None |
| build(4, 4) | 4 | 7 | 4 | [4, 3] → None | [5, 4] → None |

Result: 3 with left child 9 and right child 20, and 20 has children 15 and 7 → `[3,9,20,null,null,15,7]`.

## 6. Edge cases & pitfalls

- **Build left before right.** Swapping the two lines makes the left subtree take the right subtree's preorder values.
- **Single node** (TC 2): `build(0, 0)` makes one node, and both child ranges are empty.
- **Chain** (TC 3, `inorder` is preorder reversed): every split puts everything on the left, so the recursion is `n` deep. LeetCode allows this. Locally, Python's default limit of about 1000 calls fails for n = 3000, so call `sys.setrecursionlimit(10**4)`.
- Don't use `inorder.index()` inside the recursion. That brings back O(n²).
- Duplicates would break `pos` (later indices overwrite earlier ones). See §8.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `preorder = [3,9,20,15,7]`, `inorder = [9,3,15,20,7]` | `[3,9,20,null,null,15,7]` |
| TC 2 | `preorder = [-1]`, `inorder = [-1]` | `[-1]` |
| TC 3 | `preorder = [1,2,3]`, `inorder = [3,2,1]` | `[1,2,null,3]` |

## 8. Follow-up questions (from course doc)

- **Q: Why are unique values needed?**
  **A:** The split depends on "where is the root in inorder?" having **one** answer. With duplicates it can have several, and different choices give different trees with the same traversals. Example: `preorder = [1,1]`, `inorder = [1,1]` fits both "root 1 with a **left** child 1" and "root 1 with a **right** child 1". The input itself is ambiguous, and the hash map would also overwrite the first index.
- **Q: How does a hash map from value to inorder index give O(n)?**
  **A:** Without it, each node does a linear `inorder.index()` search. On a chain that's n + (n−1) + … = O(n²). With the map built once in O(n), each node does O(1) work: one lookup and two recursive calls. Passing index ranges instead of slices also avoids copying. So the total is O(n) time and O(n) space.
- **Q: Why can't preorder + postorder alone always identify a tree?**
  **A:** Both put the root at one end (first in preorder, last in postorder), but neither tells you **where the left subtree ends and the right one begins** when a node has only one child. Example: `preorder = [1,2]`, `postorder = [2,1]` fits both "2 is the left child of 1" and "2 is the right child of 1". Inorder fixes this because it puts the root **between** the two subtrees. Preorder + postorder only works when every node has 0 or 2 children (LC 889 accepts any one valid tree).

## 9. Threads

- **Q21** Serialize and Deserialize Binary Tree: rebuilds a tree from preorder too. It writes a marker for every missing child, so one traversal is enough.
- **Q1, Q6, Q13**: a hash map paired with another structure for O(1) lookups.
- **Q22, Q21**: recursion that shares one outer variable (`nxt` here, `best` in Q22, the token iterator in Q21).

## 10. One-line revision

> The next preorder value is the root; its inorder index (from a hash map) splits the range into left and right; build left before right.

<!-- course-nav:start -->

---

### Course navigation

**Problem 19 of 30** · Trees (1 of 4)

[← Previous: Q19 · Merge k Sorted Lists](../0023-merge-k-sorted-lists/NOTES.md) · [Course index](../INDEX.md) · [Next: Q23 · Lowest Common Ancestor of a Binary Tree II →](../1644-lowest-common-ancestor-of-a-binary-tree-ii/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
