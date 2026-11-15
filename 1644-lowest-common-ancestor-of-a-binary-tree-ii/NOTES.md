# 1644. Lowest Common Ancestor of a Binary Tree II

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q23 | [1644](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/) 🔒 | Medium | Trees | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `1644-lowest-common-ancestor-of-a-binary-tree-ii.py` (save it yourself, see the 🔒 note below)

> 🔒 **LeetCode Premium:** LeetHub can't sync this without Premium. Save your solution yourself as `1644-lowest-common-ancestor-of-a-binary-tree-ii/1644-lowest-common-ancestor-of-a-binary-tree-ii.py`, or practise the free alternative: [236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/).

## 1. Case scenario

**Common Manager Finder:** in an employee reporting tree, find the lowest manager above both employees `p` and `q` (an employee counts as their own manager here). Unlike the classic problem, `p` or `q` might not be in the tree at all, and then the answer is `None`. Values are unique, with up to 100,000 nodes. Also explain how to do it in O(1) space if every node has a parent pointer.

## 2. Pattern recognition

- **Pattern:** Trees, postorder DFS ("return one value, record another")
- **Signals in the statement:** "lowest common ancestor" → postorder (a node needs its children's findings). "May not exist" → you can't stop early, so visit every node and count.
- **Core idea in one line:** each subtree returns how many of `p`, `q` it contains; the first node whose count reaches 2 is the answer.

## 3. Approaches

### Approach 1: Check existence, then classic LCA (two passes)

First search the tree for `p` and for `q`. If either is missing, return `None`. Otherwise run the classic LC 236 code ([step 02](./prerequisites/02-lc0236-lowest-common-ancestor-of-a-binary-tree.md)). **Time:** O(n) · **Space:** O(h)

<details>
<summary><b>Show two-pass solution</b></summary>

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        def contains(node, target):
            if node is None:
                return False
            return node is target or contains(node.left, target) or contains(node.right, target)

        def lca(node):                           # classic LC 236, assumes both exist
            if node is None or node is p or node is q:
                return node
            left, right = lca(node.left), lca(node.right)
            return node if left and right else left or right

        if not contains(root, p) or not contains(root, q):
            return None
        return lca(root)
```

</details>

### Approach 2: One postorder pass that counts (optimal)

The helper **returns** how many of `p`, `q` are in the subtree ([step 03](./prerequisites/03-concept-postorder-count-found-nodes.md)) and **records** the first node where that count hits 2. **Time:** O(n), one pass · **Space:** O(h)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        ans = None

        def count(node):                         # how many of p, q are in this subtree
            nonlocal ans
            if node is None:
                return 0
            found = count(node.left) + count(node.right)
            found += (node is p) + (node is q)
            if found == 2 and ans is None:       # first, so lowest, node holding both
                ans = node
            return found

        count(root)
        return ans
```

</details>

### Approach 3: Parent pointers, O(1) space (follow-up)

This is LC 1650 (also Premium). If every node has a `parent` field, you don't need the root or any recursion. Walk up from `p` and from `q` at the same time. When a pointer falls off the top, restart it at the **other** node. Both pointers then walk `len(p's path) + len(q's path)` steps, so they meet at the LCA. If the two nodes aren't in the same tree, both reach `None` together and the answer is `None`. **Time:** O(h) · **Space:** O(1)

<details>
<summary><b>Show parent-pointer solution</b></summary>

```python
class Solution:
    def lowestCommonAncestor(self, p: 'Node', q: 'Node') -> 'Node':
        a, b = p, q
        while a is not b:
            a = a.parent if a else q             # fell off the top: switch to q's path
            b = b.parent if b else p
        return a                                 # the LCA, or None if the paths never join
```

</details>

## 4. Why it works

- `count(node)` is correct by induction: the counts of the two subtrees, plus 1 if `node` itself is `p` and 1 if it is `q`.
- Counts only grow as you go up. The nodes with count 2 are exactly the common ancestors of `p` and `q`.
- Postorder finishes children before parents, so the **first** node to reach 2 is the lowest common ancestor. The `ans is None` check stops its ancestors from overwriting it.
- There is no early return, so a missing node can't be mistaken for "hidden below `p`". If either is missing, no count ever reaches 2 and `ans` stays `None`.

## 5. Dry run: TC 2, tree `[3,5,1,6,2,0,8,null,null,7,4]`, `p = 7`, `q = 4` → `2`

| Postorder node | left | right | is p / q | returns | `ans` after |
| --- | --- | --- | --- | --- | --- |
| 6 | 0 | 0 | — | 0 | None |
| 7 | 0 | 0 | p | 1 | None |
| 4 | 0 | 0 | q | 1 | None |
| 2 | 1 | 1 | — | 2 | **2** |
| 5 | 0 | 2 | — | 2 | 2 (already set) |
| 0 | 0 | 0 | — | 0 | 2 |
| 8 | 0 | 0 | — | 0 | 2 |
| 1 | 0 | 0 | — | 0 | 2 |
| 3 | 2 | 0 | — | 2 | 2 (already set) |

Answer: node **2**.

## 6. Edge cases & pitfalls

- **One node is the ancestor of the other** (`p = 5`, `q = 4`): node 4 is counted inside 5's subtree, and 5 adds its own 1, so the answer is 5.
- **A node is missing** (TC 3): the root's count is 1, `ans` stays `None`. The classic LC 236 code would wrongly return 5 here.
- Compare nodes with `is`. On LeetCode a missing `p` or `q` is a separate `TreeNode` object that isn't linked into the tree.
- Keep the `ans is None` check. Without it, every ancestor above the LCA overwrites the answer with a higher node.
- With 100,000 nodes, a chain-shaped tree recurses 100,000 deep. Raise the limit locally, or use an iterative postorder.

## 7. Test cases (from course doc)

Tree `[3,5,1,6,2,0,8,null,null,7,4]`:

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `p = 5`, `q = 1` | `3` |
| TC 2 | `p = 7`, `q = 4` | `2` |
| TC 3 | `p = 5`, `q = 10` (10 is not in the tree) | `null` |

## 8. Follow-up questions (from course doc)

- **Q: Explain your approach.**
  **A:** A postorder DFS. Each call returns how many of `p` and `q` its subtree contains, and records the first node whose count reaches 2. That node is the lowest one with both below it. Because it never stops early, a missing node simply means no count reaches 2, and the result is `None`. One pass, O(n) time, O(h) space. See §3 and §4.
- **Q: How would you solve it in O(1) space when each node has a parent pointer?**
  **A:** Use Approach 3, the same trick as "intersection of two linked lists". The path from `p` up to the root and the path from `q` up to the root are two linked lists that merge at the LCA. Walk both pointers up, and when one runs off the top, restart it at the other start node. After one switch each, both pointers have walked the same total distance (p's path + q's path), so they reach the merge point at the same step. If `p` and `q` aren't in the same tree, the paths never merge and both pointers reach `None` at the same step, so it returns `None`. O(h) time, O(1) space.
  An equivalent version: compute both depths by walking up, move the deeper node up by the difference, then move both up together until they meet.

## 9. Threads

- **Q22** Binary Tree Maximum Path Sum: the helper returns one value (a one-arm gain) and records another (the best bent path), just like `count` and `ans` here.
- **Q24 / Q21**: recursion on trees, taught in the Q24 ladder.
- The parent-pointer trick is the same as [LC 160 Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/): two paths that merge into one.

## 10. One-line revision

> Postorder: return how many of p, q are below; record the first node where that count hits 2; no early return, so a missing node gives None.
