# 0297. Serialize and Deserialize Binary Tree

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q21 | [297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | Hard | Trees | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0297-serialize-and-deserialize-binary-tree.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Saving a Decision Tree to Disk:** a game saves its AI decision tree to disk and must load back exactly the same tree, with the same shape, the same values and the same missing children. Write `serialize(root) -> str` and `deserialize(str) -> root`, choose a format and justify it. Then explain how it changes for an N-ary tree, and how a BST could be stored in fewer bytes. 0 to 10,000 nodes, values in [−1000, 1000].

## 2. Pattern recognition

- **Pattern:** Trees, traversal with null markers (DFS preorder or BFS)
- **Signals in the statement:** "rebuild the identical tree", "including null children" → a traversal alone loses the shape, so write a marker for every missing child.
- **Core idea in one line:** preorder with `#` for each `None`: the reader meets every node before its children, so the same recursion that wrote the string can read it back.

## 3. Approaches

### Approach 1: Level order (BFS) with `null` markers

This is LeetCode's own format, `1,2,3,null,null,4,5`. Write the nodes level by level, with `null` for each missing child. Read them back with a queue: each real node takes the next two tokens as its children. Iterative, so a very deep tree is no problem. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show BFS solution</b></summary>

```python
class Codec:
    def serialize(self, root: Optional[TreeNode]) -> str:
        if not root:
            return ""
        out = []
        queue = deque([root])
        while queue:
            node = queue.popleft()
            if node:
                out.append(str(node.val))
                queue.append(node.left)
                queue.append(node.right)
            else:
                out.append("null")
        while out[-1] == "null":             # trailing nulls carry no information
            out.pop()
        return ",".join(out)

    def deserialize(self, data: str) -> Optional[TreeNode]:
        if not data:
            return None
        vals = data.split(",")
        root = TreeNode(int(vals[0]))
        queue = deque([root])
        i = 1
        while queue and i < len(vals):
            node = queue.popleft()
            if vals[i] != "null":            # next token: left child
                node.left = TreeNode(int(vals[i]))
                queue.append(node.left)
            i += 1
            if i < len(vals) and vals[i] != "null":   # next token: right child
                node.right = TreeNode(int(vals[i]))
                queue.append(node.right)
            i += 1
        return root
```

</details>

### Approach 2: Preorder with `#` markers (chosen, optimal)

Write `value` for a node and `#` for a missing child, in preorder, separated by commas. Read with an iterator: take a token, and if it's a value, create the node and read its left and then its right subtree. **Time:** O(n) · **Space:** O(n) for the string, plus O(h) recursion

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Codec:
    def serialize(self, root: Optional[TreeNode]) -> str:
        out = []

        def write(node):
            if not node:
                out.append("#")              # mark EVERY missing child
                return
            out.append(str(node.val))
            write(node.left)
            write(node.right)

        write(root)
        return ",".join(out)

    def deserialize(self, data: str) -> Optional[TreeNode]:
        tokens = iter(data.split(","))

        def read():                          # same order as write()
            tok = next(tokens)
            if tok == "#":
                return None
            node = TreeNode(int(tok))
            node.left = read()
            node.right = read()
            return node

        return read()
```

</details>

**Why this format:**

| | Preorder + `#` (chosen) | Level order + `null` |
| --- | --- | --- |
| Code | two short, mirror-image recursions | two loops with index bookkeeping |
| Size | n values + n + 1 markers | about the same (trailing nulls trimmed) |
| Streaming | each subtree is one contiguous block, so you can read it lazily | a subtree is spread over many levels |
| Deep trees | recursion is h deep (10,000 for a chain) | iterative, no depth limit |
| Human-readable | less so | matches LeetCode's display |

Preorder wins on simplicity and correctness: the writer and the reader are the same recursion, so they can't disagree. The comma separator handles negative and multi-digit values. If the tree can be very deep and you can't raise the recursion limit, use the BFS version instead.

## 4. Why it works

- With a marker for every missing child, each subtree is written as **value, left part, right part**, and an empty subtree is exactly one `#`. So the string has only one possible reading.
- `read()` consumes tokens in the same order `write()` produced them. When it reads a value, the next tokens are that node's complete left subtree (ending with its own `#`s), then its complete right subtree.
- The tokens are used up exactly when the tree is complete, and nothing extra is needed to know where a subtree ends.
- This is why Q24 needed **two** traversals and this needs only one: the markers carry the shape that inorder supplied there.

## 5. Dry run: TC 1, `[1,2,3,null,null,4,5]`

`serialize` → `"1,2,#,#,3,4,#,#,5,#,#"` (5 values, 6 markers)

`deserialize`, one token per `read()` call:

| # | Token | What `read()` does |
| --- | --- | --- |
| 1 | `1` | create 1, then read its left |
| 2 | `2` | create 2 as 1's left, read its left |
| 3 | `#` | 2.left = None |
| 4 | `#` | 2.right = None, return 2 |
| 5 | `3` | create 3 as 1's right, read its left |
| 6 | `4` | create 4 as 3's left |
| 7 | `#` | 4.left = None |
| 8 | `#` | 4.right = None, return 4 |
| 9 | `5` | create 5 as 3's right |
| 10 | `#` | 5.left = None |
| 11 | `#` | 5.right = None, return 5, then 3, then 1 |

Level order of the result: `[1,2,3,null,null,4,5]`. Same tree.

## 6. Edge cases & pitfalls

- **Empty tree** (TC 2): preorder writes `"#"`, and `read()` returns `None`. In the BFS version the empty string means an empty tree.
- **Skewed tree** (TC 3 `[1,null,2]`): `"1,#,2,#,#"`. The `#` before 2 is what says 2 is a **right** child.
- **Negative and multi-digit values:** always use a separator. `"-1000"` must stay one token.
- **Deep trees:** a 10,000-node chain means 10,000 nested `read()` calls. LeetCode allows it. Locally, raise `sys.setrecursionlimit` or use the BFS version.
- Don't build the string with repeated `+=` inside the recursion. Collect parts in a list and `join` once.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `root = [1,2,3,null,null,4,5]` | `deserialize(serialize(root))` has level order `[1,2,3,null,null,4,5]` |
| TC 2 | `root = []` | `deserialize(serialize(root))` is an empty tree |
| TC 3 | `root = [1,null,2]` | round trip gives `[1,null,2]` (right-skewed) |

## 8. Follow-up questions (from course doc)

- **Q: Choose a format and justify it.**
  **A:** Preorder with `#` for every missing child, comma-separated. See the table in §3: the writer and reader are the same recursion, a subtree is one contiguous block, and markers make the string unambiguous. BFS with `null` is the alternative when the tree may be too deep for recursion.

- **Q: How does it change for an N-ary tree?**
  **A:** There's no fixed "left" and "right", so a `#` per missing child doesn't work: you don't know how many children to expect. Write the **number of children** after each value, still in preorder. The reader reads the value, reads the count, then reads that many subtrees. For example, 1 with children [3, 2, 4], where 3 has children [5, 6], becomes `1,3,3,2,5,0,6,0,2,0,4,0`. (Another option is a closing marker such as `)` after each node's last child.)

<details>
<summary><b>Show N-ary version</b></summary>

```python
def serialize_nary(root: Optional['Node']) -> str:
    out = []

    def write(node):
        out.append(str(node.val))
        out.append(str(len(node.children)))   # how many subtrees follow
        for child in node.children:
            write(child)

    if root:
        write(root)
    return ",".join(out)

def deserialize_nary(data: str) -> Optional['Node']:
    if not data:
        return None
    tokens = iter(data.split(","))

    def read():
        node = Node(int(next(tokens)), [])
        for _ in range(int(next(tokens))):
            node.children.append(read())
        return node

    return read()
```

</details>

- **Q: How could a BST be encoded in fewer bytes?**
  **A:** A BST's ordering already tells you where each value goes, so **drop the null markers** and store only the preorder values. That's n tokens instead of 2n + 1. To read it back, give each subtree a `(low, high)` range: the next value belongs to the current subtree only if `low < value < high`. Otherwise that subtree is empty. Each value is read once, so this is O(n), not the O(n log n) of inserting values one by one. To shrink it further, store each value as a 2-byte signed integer instead of text, because [−1000, 1000] fits in 16 bits. That's exactly 2n bytes, with no separators. ([LC 449](https://leetcode.com/problems/serialize-and-deserialize-bst/) is this problem. It wants a `str`, so there keep the comma-separated preorder values instead of bytes.)

<details>
<summary><b>Show compact BST version</b></summary>

```python
import struct

def serialize_bst(root: Optional[TreeNode]) -> bytes:
    vals = []

    def write(node):
        if node:
            vals.append(node.val)
            write(node.left)
            write(node.right)

    write(root)
    return struct.pack(f"<{len(vals)}h", *vals)    # 2 bytes per value, no markers

def deserialize_bst(data: bytes) -> Optional[TreeNode]:
    vals = struct.unpack(f"<{len(data) // 2}h", data)
    i = 0

    def read(low, high):                            # values allowed here: low < v < high
        nonlocal i
        if i == len(vals) or not (low < vals[i] < high):
            return None
        node = TreeNode(vals[i])
        i += 1
        node.left = read(low, node.val)
        node.right = read(node.val, high)
        return node

    return read(float("-inf"), float("inf"))
```

</details>

## 9. Threads

- **Q24** Construct Binary Tree from Preorder and Inorder: rebuilds a tree from preorder **without** markers, which is why it also needs inorder.
- **Q22, Q24**: tree recursion with one shared outer variable (the token iterator here).

## 10. One-line revision

> Preorder, write `#` for every None, join with commas; read back with an iterator and the same recursion (value → node, then left, then right).

<!-- course-nav:start -->

---

### Course navigation

**Problem 22 of 30** · Trees (4 of 4)

[← Previous: Q22 · Binary Tree Maximum Path Sum](../0124-binary-tree-maximum-path-sum/NOTES.md) · [Course index](../INDEX.md) · [Next: Q25 · Course Schedule II →](../0210-course-schedule-ii/prerequisites/00-roadmap.md)

> 🧭 **Next pattern: Graphs.** Read [08 · Graphs primer](../course/primers/08-graphs.md) before starting Q25.

<!-- course-nav:end -->
