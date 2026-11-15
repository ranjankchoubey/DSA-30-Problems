# 0124. Binary Tree Maximum Path Sum

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q22 | [124](https://leetcode.com/problems/binary-tree-maximum-path-sum/) | Hard | Trees | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0124-binary-tree-maximum-path-sum.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Most Profitable Route in an Org Tree:** every node in a company hierarchy holds a profit or a loss (it can be negative). A route is any chain of connected nodes that doesn't revisit a node, and it may start and end anywhere. Find the largest total. Up to 30,000 nodes, values in [−1000, 1000].

## 2. Pattern recognition

- **Pattern:** Trees, postorder DFS ("return one value, record another")
- **Signals in the statement:** "path may start and end at **any** node" → the path bends at some highest node, like the diameter. "Can be negative" → drop arms that lose money.
- **Core idea in one line:** at each node, record `val + max(0, L) + max(0, R)` as a candidate, and return `val + max(0, L, R)` to the parent.

## 3. Approaches

### Approach 1: Try every node as the bend (brute force)

For every node, compute the best downward arm on each side from scratch ([step 04](./prerequisites/04-concept-drop-negative-branches.md)'s `max_gain`) and add them. Each node rescans its whole subtree. **Time:** O(n²) on a chain (O(n·h) in general) · **Space:** O(h)

<details>
<summary><b>Show brute-force solution</b></summary>

```python
class Solution:
    def maxPathSum(self, root: Optional[TreeNode]) -> int:
        def max_gain(node):                       # best downward path from node
            if not node:
                return 0
            return node.val + max(0, max_gain(node.left), max_gain(node.right))

        best = float("-inf")
        stack = [root]
        while stack:                              # visit every node as the bend
            node = stack.pop()
            left = max(0, max_gain(node.left))
            right = max(0, max_gain(node.right))
            best = max(best, node.val + left + right)
            stack.extend(child for child in (node.left, node.right) if child)
        return best
```

</details>

### Approach 2: One postorder pass (optimal)

Compute each arm once, bottom-up, and use it twice: in the candidate recorded at this node, and in the value returned to the parent. **Time:** O(n) · **Space:** O(h)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def maxPathSum(self, root: Optional[TreeNode]) -> int:
        best = float("-inf")

        def gain(node):                           # best downward path starting at node
            nonlocal best
            if not node:
                return 0
            left = max(0, gain(node.left))        # drop a losing arm
            right = max(0, gain(node.right))
            best = max(best, node.val + left + right)   # RECORD: path bending here
            return node.val + max(left, right)          # RETURN: one arm for the parent

        gain(root)
        return best
```

</details>

## 4. Why it works

- Every path has exactly one **highest** node, where it bends. Below it, the path goes down the left side, the right side, both, or neither.
- At that node, the best such path is `val + max(0, best left arm) + max(0, best right arm)`. The algorithm records this at **every** node, so the true answer is among the candidates.
- `gain(child)` is the best downward arm from the child, so the parent gets the right arm values. An arm with a negative sum is replaced by 0, meaning "don't extend".
- The returned value uses **one** arm because a parent extending into a child can only go down one side. Using both would revisit the child.
- `best` starts at −∞ (not 0), because every value may be negative and the path must contain at least one node.

## 5. Dry run: TC 2, `[-10,9,20,null,null,15,7]` → `42`

| Postorder node | left = max(0, ·) | right = max(0, ·) | records `val + left + right` | `best` after | returns `val + max(left, right)` |
| --- | --- | --- | --- | --- | --- |
| 9 | 0 | 0 | 9 | 9 | 9 |
| 15 | 0 | 0 | 15 | 15 | 15 |
| 7 | 0 | 0 | 7 | 15 | 7 |
| 20 | 15 | 7 | 42 | **42** | 35 |
| −10 | 9 | 35 | 34 | 42 | 25 |

The best path is 15 → 20 → 7, which bends at 20 and never reaches the root.

## 6. Edge cases & pitfalls

- **All negative** (TC 3 `[-3]` → −3): starting `best` at 0 would wrongly return 0, an empty path.
- **Returning both arms** to the parent: it would count a path that goes down two sides of a child and then up, which revisits a node.
- Clamp the **arms** with `max(0, ·)`, never the node's own value. The bend node itself must stay in the path.
- **Deep trees:** 30,000 nodes in a chain recurse 30,000 deep. LeetCode allows it. Locally, call `sys.setrecursionlimit(10**5)` or write an iterative postorder.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `root = [1,2,3]` | `6` |
| TC 2 | `root = [-10,9,20,null,null,15,7]` | `42` |
| TC 3 | `root = [-3]` | `-3` |

## 8. Follow-up questions (from course doc)

- **Q: What is the difference between what the recursive helper returns to its parent and what it records globally?**
  **A:**

  | | Returns to the parent | Records in `best` |
  | --- | --- | --- |
  | Value | `node.val + max(left, right)` | `node.val + left + right` |
  | Shape | a **one-arm** path going down from this node | a path that **bends** at this node (up to two arms) |
  | Who uses it | the parent, to extend its own arm | nobody. It's a finished candidate answer |
  | Why different | a parent can continue into only one side without revisiting a node | the best path may bend anywhere, not only at the root |

  With TC 2, node 20 **records** 42 (15 + 20 + 7, the answer) but **returns** only 35 (20 + 15), because −10 can extend just one of 20's arms.

## 9. Threads

- **Q23** LCA II: the helper returns a count and records the answer node, the same split.
- **Q11** Maximum Product Subarray: Kadane's "drop a negative running sum" (LC 53) is `max(0, gain)` on an array.
- **Q24, Q21**: tree recursion, taught in the Q24 ladder.

## 10. One-line revision

> Postorder: arms = max(0, gain(child)); record val + left + right; return val + max(left, right); start best at −∞.
