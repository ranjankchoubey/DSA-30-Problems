# 0127. Word Ladder

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q26 | [127](https://leetcode.com/problems/word-ladder/) | Hard | Graphs (BFS) | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0127-word-ladder.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Spell-Correction Chain:** turn `beginWord` into `endWord` by changing one letter at a time, where every intermediate word must be in the dictionary. Return the number of words in the shortest chain (both ends included), or `0`. Solve with BFS, optimise with bidirectional BFS, and explain how to return **all** shortest chains. Words have up to 10 letters; the dictionary has up to 5000 words.

## 2. Pattern recognition

- **Pattern:** Graphs, BFS shortest path on an implicit graph
- **Signals in the statement:** "shortest", "one letter at a time" (every move costs 1), start and goal both known. Words are nodes, one-letter changes are edges, and the graph is never given explicitly.
- **Core idea in one line:** BFS from `beginWord`; the level at which `endWord` is first reached is the answer. Meeting in the middle from both ends explores far fewer words.

## 3. Approaches

Let N = dictionary size and L = word length.

### Approach 1: Brute force: build the whole graph, then BFS

Compare every pair of words to find the edges, then BFS. **Time:** O(N² · L) · **Space:** O(N²) edges in the worst case

### Approach 2: BFS with wildcard buckets ([step 03](./prerequisites/03-concept-wildcard-buckets.md))

Find neighbours through `h*t`-style buckets instead of comparing pairs. **Time:** O(N · L²) · **Space:** O(N · L²)

<details>
<summary><b>Show BFS solution</b></summary>

```python
class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0
        L = len(beginWord)
        buckets = defaultdict(list)
        for word in wordList:
            for i in range(L):
                buckets[word[:i] + "*" + word[i + 1:]].append(word)
        visited = {beginWord}
        queue = deque([(beginWord, 1)])              # (word, words in the chain so far)
        while queue:
            word, length = queue.popleft()
            if word == endWord:
                return length
            for i in range(L):
                key = word[:i] + "*" + word[i + 1:]
                for nxt in buckets.get(key, []):
                    if nxt not in visited:
                        visited.add(nxt)
                        queue.append((nxt, length + 1))
                buckets[key] = []                    # every word in it is now visited
        return 0
```

</details>

### Approach 3: Bidirectional BFS (optimal)

Keep two frontiers, one growing from `beginWord` and one from `endWord`. Each round, expand the **smaller** one by one level; stop as soon as a new word is already in the other frontier. Same worst case as BFS, but if each word has about `b` neighbours and the answer is `d` steps away, it touches about `2 · b^(d/2)` words instead of `b^d`. **Time:** O(N · 26 · L²) worst case, far less in practice · **Space:** O(N · L)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        unvisited = set(wordList)
        if endWord not in unvisited:
            return 0
        front, back = {beginWord}, {endWord}
        unvisited.discard(beginWord)
        unvisited.discard(endWord)
        length = 1
        while front and back:
            if len(front) > len(back):
                front, back = back, front            # always expand the smaller side
            length += 1
            nxt_front = set()
            for word in front:
                for i in range(len(word)):
                    for ch in "abcdefghijklmnopqrstuvwxyz":
                        cand = word[:i] + ch + word[i + 1:]
                        if cand in back:             # the two searches meet
                            return length
                        if cand in unvisited:
                            unvisited.remove(cand)
                            nxt_front.add(cand)
            front = nxt_front
        return 0
```

</details>

## 4. Why it works

- **BFS:** all edges cost 1, so BFS pops words in order of distance from `beginWord` (see [step 01](./prerequisites/01-lc1091-shortest-path-in-binary-matrix.md)). The first time `endWord` is popped, `length` is the shortest chain.
- **Bidirectional:** after the rounds so far, `front` holds the words exactly `i` steps from one end and `back` the words exactly `j` steps from the other, and `length` = `i + j + 1` words once we add the next level. The first time a new word lands in the other frontier, the chain through it has `length` words. Any shorter chain would have made the frontiers meet in an earlier round.
- A word is removed from `unvisited` the moment either side reaches it, so no word is expanded twice, and the two frontiers never overlap.
- Swapping to the smaller side doesn't change correctness (the graph is undirected: "one letter apart" goes both ways); it only keeps the work small.

## 5. Dry run: TC 1, `"hit" → "cog"`, list `[hot,dot,dog,lot,log,cog]` → 5

Start: `front = {hit}`, `back = {cog}`, `unvisited = {hot, dot, dog, lot, log}`, `length = 1`.

| round | swap? | `length` | expand | new frontier | met? |
| --- | --- | --- | --- | --- | --- |
| 1 | no (1 vs 1) | 2 | `hit` | `{hot}` | no |
| 2 | no (1 vs 1) | 3 | `hot` | `{dot, lot}` | no |
| 3 | yes (2 vs 1): expand `{cog}` | 4 | `cog` | `{dog, log}` | no |
| 4 | no (2 vs 2) | 5 | `dog` / `log` | — | `dog → dot` (or `log → lot`) is in `back` → return **5** |

The chain is `hit → hot → dot → dog → cog` (or via `lot → log`).

## 6. Edge cases & pitfalls

- **`endWord` not in the list** (TC 2's list has no `cog`): return `0` immediately. Bidirectional BFS **must** check this, or it would search backwards from a word that isn't allowed.
- **`beginWord` doesn't have to be in the list**, but if it is, mark it visited so it isn't revisited.
- The answer counts **words**, not changes: `"a" → "c"` is 2 words (TC 3), because `a → c` is one change.
- Mark words visited when they're **queued**, not when they're popped, or the same word enters the queue many times.
- In bidirectional BFS, check `cand in back` **before** `cand in unvisited`: the other side's words were already removed from `unvisited`.
- `endWord not in wordList` on a list is O(N · L); do it once, or convert to a set first.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `begin = "hit", end = "cog", list = [hot,dot,dog,lot,log,cog]` | `5` |
| TC 2 | `begin = "hit", end = "cog", list = [hot,dot,dog,lot,log]` | `0` |
| TC 3 | `begin = "a", end = "c", list = [a,b,c]` | `2` |

## 8. Follow-up questions (from course doc)

- **Q: Solve it with BFS, then optimise using bidirectional BFS. Why is bidirectional faster?**
  **A:** Plain BFS (Approach 2) explores every word closer than the answer. With branching factor `b` and answer distance `d`, that's about `b^d` words. Two searches that each go `d/2` levels deep touch about `2 · b^(d/2)`; for `b = 10`, `d = 6`, that's 2,000 instead of 1,000,000. Expanding the smaller frontier each round keeps both searches small. The worst case (a long thin chain) is the same O(N · 26 · L²).
- **Q: How would you return all shortest sequences?** (LeetCode 126, Word Ladder II)
  **A:** Two phases.
  1. **BFS level by level, recording parents.** For each word reached in the next level, store **every** word in the current level that leads to it (`parents[word]` is a list). Remove a whole level from the dictionary only **after** the level is finished, so a word can collect several parents from the same level, but never from a later one. Stop after the level in which `endWord` appears.
  2. **Backtrack** from `endWord` through `parents` to `beginWord` with DFS, building each path in reverse.

  Because parents only point one level back, every path built this way is a shortest one, and every shortest path is built. BFS costs O(N · 26 · L²); the backtracking costs O(L × number of shortest paths), which can be exponential in the worst case. That cost is unavoidable when the output itself is that large.

<details>
<summary><b>Show the all-shortest-sequences version</b></summary>

```python
def find_ladders(beginWord, endWord, wordList):
    unvisited = set(wordList)
    if endWord not in unvisited:
        return []
    parents = defaultdict(list)
    level = {beginWord}
    found = False
    while level and not found:
        unvisited -= level                       # remove this whole level first
        next_level = set()
        for word in level:
            for i in range(len(word)):
                for ch in "abcdefghijklmnopqrstuvwxyz":
                    cand = word[:i] + ch + word[i + 1:]
                    if cand in unvisited:
                        parents[cand].append(word)   # may get several parents
                        next_level.add(cand)
                        if cand == endWord:
                            found = True
        level = next_level
    if not found:
        return []
    paths = []

    def backtrack(word, path):
        if word == beginWord:
            paths.append(path[::-1])
            return
        for p in parents[word]:
            path.append(p)
            backtrack(p, path)
            path.pop()

    backtrack(endWord, [endWord])
    return paths
```

</details>

  For TC 1 it returns the two chains `hit → hot → dot → dog → cog` and `hit → hot → lot → log → cog`.

## 9. Threads

- **Q29** [Network Delay Time](../0743-network-delay-time/NOTES.md): recaps step 01's BFS, then replaces the queue with a heap when edges have different weights.
- **Q25** Course Schedule II: the same deque-based BFS, used to produce an order.
- **Q27** Number of Islands II: another "graph you never build" (grid cells instead of words).

## 10. One-line revision

> Words are nodes, one-letter changes are edges: BFS from begin (counting words), or bidirectional BFS expanding the smaller frontier until it hits the other; return 0 if end isn't in the list.
