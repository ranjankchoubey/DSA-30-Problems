# Step 03 · Concept: the first difference gives an edge

[← Previous: Verifying an Alien Dictionary](02-lc0953-verifying-an-alien-dictionary.md) · [Roadmap](00-roadmap.md) · [Target: Alien Dictionary →](../NOTES.md)

> 📚 **New to this?** Primer: [graph representation](../../course/primers/08-graphs.md#graph-representation)

**Skill it adds:** deriving a graph that isn't given, one edge per adjacent pair of words, and rejecting the invalid prefix case.

## Idea

Run step 02 in reverse. For each adjacent pair `a`, `b`:

- Find the **first** position where they differ, with letters `x` (in `a`) and `y` (in `b`). Since `a` comes first, `x` comes before `y`: add the edge **`x → y`**. Stop there, because later letters tell you nothing.
- If there's **no** difference and `a` is **longer** than `b` (like `"abc"` before `"ab"`), no alphabet can make that sorted. The input is **invalid**.
- If there's no difference and `a` is not longer (equal words, or `a` is a prefix of `b`), the pair gives no edge.

Every letter that appears in any word must be in the answer, even if it has no edges, so collect them all as nodes.

## Code

```python
def build_letter_graph(words):
    graph = {c: set() for w in words for c in w}   # every letter is a node
    for a, b in zip(words, words[1:]):
        for x, y in zip(a, b):
            if x != y:
                graph[x].add(y)                     # x comes before y
                break
        else:
            if len(a) > len(b):
                return None                         # "abc" before "ab"
    return graph
```

**Time:** O(total letters) · **Space:** O(U + E) for U distinct letters and E edges (E ≤ number of words − 1)

## Walk-through: `["wrt","wrf","er","ett","rftt"]`

| Pair | First difference | Edge |
| --- | --- | --- |
| wrt, wrf | position 2: `t` vs `f` | t → f |
| wrf, er | position 0: `w` vs `e` | w → e |
| er, ett | position 1: `r` vs `t` | r → t |
| ett, rftt | position 0: `e` vs `r` | e → r |

The edges chain into **w → e → r → t → f**, so the only valid order is `"wertf"`.

## Test cases

| Input | Expected `build_letter_graph` result |
| --- | --- |
| `["wrt","wrf","er","ett","rftt"]` | `{w: {e}, r: {t}, t: {f}, f: {}, e: {r}}` |
| `["z","x"]` | `{z: {x}, x: {}}` |
| `["abc","ab"]` | `None` (invalid prefix case) |

## Pitfall

Only the **first** difference counts. In `"wrt"` vs `"wrf"` it would be wrong to also compare the `w`s or `r`s (they're equal), and in `"ett"` vs `"rftt"` it would be wrong to add `t → f` from position 1, because once `e < r` decides the order, the rest of the letters can be anything.

## What changes in the target

Feed this graph into step 01's **topological sort**. If it can't place every letter, there's a **cycle** (e.g. `["a","b","a"]` gives `a → b` and `b → a`), so return `""`. Otherwise join the order into a string → [NOTES](../NOTES.md).
