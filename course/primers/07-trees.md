# Primer 07 · Trees

Read this **before** the Trees ladders (Q24, Q23, Q22, Q21), or when a step's key idea doesn't make sense yet.

## Tree basics

A **binary tree** is a set of nodes. Each node holds a value and links to at most two children, `left` and `right`. LeetCode uses this class:

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

| Term | Meaning |
| --- | --- |
| Root | The top node. The whole tree is reached from it. |
| Leaf | A node with no children. |
| Subtree | A node plus everything below it. Every child is the root of its own subtree. |
| Ancestor | Any node on the way up from a node to the root (a node counts as its own ancestor in LCA problems). |
| Depth / height | Depth = edges (or nodes) from the root down to a node. Height = longest path down from a node to a leaf. |
| BST | Binary search tree: everything in the left subtree is smaller than the node, everything in the right subtree is larger. |

| Operation | How | Time |
| --- | --- | --- |
| Move to a child | `node.left`, `node.right` | O(1) |
| Move to the parent | not stored; needs a `parent` field or a dict | — |
| Visit every node (any traversal) | DFS or BFS | O(n) |
| Find a value in a plain binary tree | full traversal | O(n) |
| Find a value in a BST | go left or right at each node | O(h) |
| Extra space for DFS / BFS | recursion stack / queue | O(h) / O(width) |

`h` is the height: about `log n` for a balanced tree, but `n` for a "chain" where every node has one child.

### LeetCode's list format

LeetCode writes trees as a **level-order list** with `null` for a missing child: `[3,9,20,null,null,15,7]` is 3 at the root, 9 and 20 below it, 9 has no children, and 20 has 15 and 7. The children of a `null` are not listed, and trailing `null`s are dropped. Use these helpers to test locally (`None` instead of `null`):

```python
from collections import deque

def build_tree(values):
    """[3, 9, 20, None, None, 15, 7] -> TreeNode root (or None)."""
    if not values or values[0] is None:
        return None
    root = TreeNode(values[0])
    queue = deque([root])
    i = 1
    while queue and i < len(values):
        node = queue.popleft()
        if i < len(values) and values[i] is not None:
            node.left = TreeNode(values[i])
            queue.append(node.left)
        i += 1
        if i < len(values) and values[i] is not None:
            node.right = TreeNode(values[i])
            queue.append(node.right)
        i += 1
    return root

def to_list(root):
    """TreeNode root -> [3, 9, 20, None, None, 15, 7]."""
    out = []
    queue = deque([root])
    while queue:
        node = queue.popleft()
        if node:
            out.append(node.val)
            queue.append(node.left)
            queue.append(node.right)
        else:
            out.append(None)
    while out and out[-1] is None:
        out.pop()
    return out
```

## Tree traversals

Depth-first traversals all walk the tree the same way. They only differ in **when** they record the node:

| Order | Record the node… | Tree `[1,2,3,4,5]` | Use it when… |
| --- | --- | --- | --- |
| Preorder | **before** its children (node, left, right) | 1 2 4 5 3 | you copy, print or serialize a tree; the root comes first |
| Inorder | **between** its children (left, node, right) | 4 2 5 1 3 | you need a BST in sorted order |
| Postorder | **after** its children (left, right, node) | 4 5 2 3 1 | a node needs its children's answers first (height, path sums, LCA) |
| Level order | level by level (BFS, see below) | 1 2 3 4 5 | you need levels, or the nearest node to the root |

Recursive (move the `out.append` line to switch order):

```python
def preorder(root):
    out = []
    def dfs(node):
        if not node:
            return
        out.append(node.val)     # preorder: here
        dfs(node.left)           # inorder: append between the two calls
        dfs(node.right)          # postorder: append after both calls
    dfs(root)
    return out
```

Iterative, with an explicit stack instead of the call stack:

```python
def preorder_iterative(root):
    out = []
    stack = [root] if root else []
    while stack:
        node = stack.pop()
        out.append(node.val)
        if node.right:
            stack.append(node.right)   # pushed first so left is popped first
        if node.left:
            stack.append(node.left)
    return out

def inorder_iterative(root):
    out = []
    stack = []
    node = root
    while node or stack:
        while node:                    # go left as far as possible
            stack.append(node)
            node = node.left
        node = stack.pop()
        out.append(node.val)
        node = node.right
    return out
```

## Recursion on trees

Almost every tree problem is solved by answering three questions:

1. **Base case:** what is the answer for an empty tree (`None`)?
2. **Ask the children:** call the function on `node.left` and `node.right`, and **trust** that it returns the right answer for those subtrees.
3. **Combine:** build this node's answer from the two child answers and `node.val`.

```python
def max_depth(node):
    if not node:
        return 0                                   # 1. base case
    left = max_depth(node.left)                    # 2. ask the children
    right = max_depth(node.right)
    return 1 + max(left, right)                    # 3. combine
```

### Passing information down

Sometimes the children need to know something about the path above them. Pass it as a parameter, for example the remaining sum in Path Sum, or the allowed `(low, high)` range in a BST.

### Return one value, record another

Sometimes the value a parent needs is **not** the final answer. Diameter of a tree is the classic case: a parent can only extend **one** arm of a child's path, but the best path may bend at the child and use **both** arms. So the helper **returns** the one-arm value and **records** the two-arm value in an outer variable:

```python
def diameter(root):
    best = 0
    def height(node):
        nonlocal best
        if not node:
            return 0
        left, right = height(node.left), height(node.right)
        best = max(best, left + right)        # record: path that bends here
        return 1 + max(left, right)           # return: one arm for the parent
    height(root)
    return best
```

### Recursion depth

Python stops at about 1000 nested calls by default. A chain of 3000+ nodes then raises `RecursionError`. LeetCode raises the limit for you. Locally, call `sys.setrecursionlimit(10**5)` or use an iterative version.

## Level order BFS

Use a `deque` as a queue. At the start of each level, `len(queue)` is exactly the number of nodes on that level:

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    out = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):      # only the nodes of this level
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        out.append(level)
    return out
```

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "depth", "height", "balanced", "sum of subtree" | recursion: combine the children's answers (postorder) |
| "path from root to leaf", "target sum along a path" | pass the running value down as a parameter |
| "path between any two nodes", "diameter", "max path sum" | return one arm, record the bent path globally |
| "level", "row", "nearest to the root", "right side view" | BFS with a queue, level by level |
| "BST", "sorted", "k-th smallest" | inorder gives sorted order; use the ordering to go left or right |
| "rebuild", "save and load", "serialize" | preorder (or level order) plus null markers |
| "common ancestor", "manager of both" | postorder: ask both subtrees what they found |

## Common pitfalls

- Forgetting the `None` base case, which gives `AttributeError: 'NoneType' object has no attribute 'val'`.
- Using `list.pop(0)` as a queue. It's O(n); use `deque.popleft()`.
- In iterative preorder, pushing left before right (you then visit right first).
- Returning a path that uses **both** children to the parent. A parent can only continue one arm.
- Comparing nodes by value when values can repeat. Compare node objects with `is` when the problem passes nodes.
- Deep trees and Python's recursion limit (see above).

## Learn more

- NeetCode roadmap (the Trees node): <https://neetcode.io/roadmap>
- Python docs: [collections.deque](https://docs.python.org/3/library/collections.html#collections.deque) · [sys.setrecursionlimit](https://docs.python.org/3/library/sys.html#sys.setrecursionlimit)
- Visualise a BST and its traversals: <https://visualgo.net/en/bst>
