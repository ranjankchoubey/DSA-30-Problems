# 0269. Alien Dictionary

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q30 | [269](https://leetcode.com/problems/alien-dictionary/) 🔒 | Hard | Advanced Graphs | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0269-alien-dictionary.py` (save it yourself, see the 🔒 note below)

> 🔒 **LeetCode Premium:** LeetHub can't sync this without Premium. Save your solution yourself as `0269-alien-dictionary/0269-alien-dictionary.py`, or practise the free alternative: [NeetCode Foreign Dictionary](https://neetcode.io/problems/foreign-dictionary).

## 1. Case scenario

**Decoding an Alien Alphabet:** you find a dictionary of an unknown language, with words sorted in that language's order. Derive any valid order of its letters, or return `""` if the dictionary is inconsistent (it implies a cycle, or a longer word appears before its own prefix). Build a graph from adjacent words and run a topological sort.

## 2. Pattern recognition

- **Pattern:** Advanced Graphs, topological sort on a **derived** graph
- **Signals in the statement:** "sorted in an unknown order", "derive the order", "any valid order", "inconsistent". These are "X before Y" constraints, which means topological sort.
- **Core idea in one line:** each adjacent pair of words gives at most one edge (first different letter), then a topological sort of the letters is the alphabet.

## 3. Approaches

Both approaches build the same graph ([step 03](./prerequisites/03-concept-first-difference-gives-edge.md)) and differ only in how they sort it. U = distinct letters (≤ 26), C = total letters in all words.

### Approach 1: Build edges + DFS topological sort (3 colours)

DFS from every letter; append a letter after all its successors are done, then reverse. Meeting a letter that is still "in progress" means a cycle. **Time:** O(C + U + E) · **Space:** O(U + E)

<details>
<summary><b>Show DFS version</b></summary>

```python
class Solution:
    def alienOrder(self, words: List[str]) -> str:
        graph = {c: set() for w in words for c in w}
        for a, b in zip(words, words[1:]):
            for x, y in zip(a, b):
                if x != y:
                    graph[x].add(y)
                    break
            else:
                if len(a) > len(b):
                    return ""                # longer word before its prefix
        VISITING, DONE = 1, 2
        state = {}
        out = []

        def dfs(c):                          # False if a cycle is found
            state[c] = VISITING
            for nxt in graph[c]:
                if state.get(nxt) == VISITING:
                    return False
                if nxt not in state and not dfs(nxt):
                    return False
            state[c] = DONE
            out.append(c)                    # after everything that follows c
            return True

        for c in graph:
            if c not in state and not dfs(c):
                return ""
        return "".join(reversed(out))
```

</details>

### Approach 2: Build edges + Kahn's algorithm (optimal)

Count in-degrees, start from letters with nothing before them, and peel letters off layer by layer. If some letters are never output, they're in a cycle. **Time:** O(C + U + E) · **Space:** O(U + E)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
from collections import deque

class Solution:
    def alienOrder(self, words: List[str]) -> str:
        graph = {c: set() for w in words for c in w}
        indegree = {c: 0 for c in graph}
        for a, b in zip(words, words[1:]):
            for x, y in zip(a, b):
                if x != y:
                    if y not in graph[x]:        # don't count an edge twice
                        graph[x].add(y)
                        indegree[y] += 1
                    break
            else:
                if len(a) > len(b):
                    return ""                    # longer word before its prefix
        queue = deque(c for c in graph if indegree[c] == 0)
        order = []
        while queue:
            c = queue.popleft()
            order.append(c)
            for nxt in graph[c]:
                indegree[nxt] -= 1
                if indegree[nxt] == 0:
                    queue.append(nxt)
        if len(order) < len(graph):
            return ""                            # cycle
        return "".join(order)
```

</details>

On NeetCode the method is called `foreignDictionary`; the body is the same.

## 4. Why it works

- **Edges are exactly the information the list gives.** Adjacent words are ordered by their first different letter, so that pair is the only fact one comparison reveals. Comparing only adjacent pairs is enough, because sortedness is transitive.
- **Any topological order is valid.** It puts `x` before `y` for every edge, so every adjacent pair compares correctly, so the whole list is sorted under it.
- **Cycle → `""`:** a cycle means some letter must come before itself. Kahn's queue then empties early, and `len(order) < len(graph)`.
- **Prefix case → `""`:** if `b` is a proper prefix of `a` but `a` comes first, no alphabet can fix it (a prefix always sorts first), so we return before sorting.

## 5. Dry run: TC 1 `["wrt","wrf","er","ett","rftt"]` → `"wertf"`

Edges from adjacent pairs: `t → f`, `w → e`, `r → t`, `e → r`. Letters in first-seen order: `w, r, t, f, e`.

| Step | Queue | Pop | Order | In-degree changes |
| --- | --- | --- | --- | --- |
| start | [w] | — | — | w:0 r:1 t:1 f:1 e:1 |
| 1 | [w] | w | w | e: 1 → 0, queue e |
| 2 | [e] | e | we | r: 1 → 0, queue r |
| 3 | [r] | r | wer | t: 1 → 0, queue t |
| 4 | [t] | t | wert | f: 1 → 0, queue f |
| 5 | [f] | f | wertf | — |

All 5 letters placed → **"wertf"**. Here the order is unique because the edges form one chain.

## 6. Edge cases & pitfalls

- **Prefix case** (TC 3, `["abc","ab"]`): return `""` before sorting. The opposite (`["ab","abc"]`) is fine and gives no edge.
- **Duplicate edges:** the same `x → y` from two pairs must not raise `indegree[y]` twice, or `y` never reaches 0 and you report a false cycle.
- **Letters with no edges** still belong in the output. Create a node for every letter in every word, not just those in edges.
- **Only the first difference** per pair counts. Adding edges from later letters invents rules that aren't true.
- **Several valid answers:** when the queue holds two letters at once, either can go first. Any valid order is accepted, so tests should check the constraints, not one exact string.
- A single word gives no edges; its letters in any order is valid.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `words = ["wrt","wrf","er","ett","rftt"]` | `"wertf"` |
| TC 2 | `words = ["z","x"]` | `"zx"` |
| TC 3 | `words = ["abc","ab"]` | `""` (a longer word appears before its prefix) |

## 8. Follow-up questions (from course doc)

- **Q: How do you build the graph from the words?**
  **A:** Compare each adjacent pair. The first position where they differ gives one edge `x → y`. Nothing else in the pair is informative (step 03).
- **Q: How do you detect that the dictionary is inconsistent because of a cycle?**
  **A:** Kahn's algorithm outputs fewer letters than exist, because letters on a cycle never reach in-degree 0. In the DFS version, reaching a letter that is still "visiting" means a back edge, so there's a cycle. Example: `["a","b","a"]` gives `a → b` and `b → a`, so the answer is `""`.
- **Q: How do you detect the prefix case?**
  **A:** If two adjacent words have no differing letter and the first is **longer**, return `""` immediately. No alphabet can put `"abc"` before `"ab"`, since a prefix always sorts first.
- **Q: Is the answer unique?**
  **A:** Only if the queue never holds more than one letter. For example `["ab","ac"]` gives only `b → c`, so `a` can go anywhere (`"abc"`, `"bac"`, `"bca"` are all valid). To check uniqueness, verify that the queue has exactly one letter at every step.

## 9. Threads

- **Q25** Course Schedule II: the same Kahn's algorithm and cycle check on a given graph.
- **Q29 / Q28**: other Advanced Graphs problems, where the edges are given rather than derived.

## 10. One-line revision

> First different letter of each adjacent pair → edge; longer word before its prefix → ""; Kahn's topological sort, and if not all letters come out → "".
