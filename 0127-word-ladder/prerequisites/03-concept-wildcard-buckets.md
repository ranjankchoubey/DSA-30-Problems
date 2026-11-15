# Step 03 · Concept: wildcard buckets for fast neighbour lookup

[← Previous: Minimum Genetic Mutation](02-lc0433-minimum-genetic-mutation.md) · [Roadmap](00-roadmap.md) · [Target: Word Ladder →](../NOTES.md)

**Skill it adds:** grouping words by **pattern** (`h*t`) so that "all words one letter away" is a few dictionary lookups.

> 📚 **New to this?** Primer: [BFS](../../course/primers/08-graphs.md#bfs)

## Idea

Two words are neighbours if they differ in exactly one position. Replace that position with `*` and both words give the **same pattern**: `hot` and `dot` both give `*ot`.

So build a dictionary `pattern → words with that pattern` once. To find a word's neighbours, make its L patterns and read their buckets. Every word in those buckets (other than the word itself) is one letter away.

| Way to find neighbours | Work per word |
| --- | --- |
| Compare with every word in the list | O(N · L) |
| Try all 26 letters at each position (step 02 style) | O(26 · L²): 26L candidates, each built and hashed in O(L) |
| Wildcard buckets | O(L²) to build the L patterns, plus the bucket sizes |

## Code

```python
from collections import defaultdict

def build_buckets(words):
    buckets = defaultdict(list)
    for word in words:
        for i in range(len(word)):
            buckets[word[:i] + "*" + word[i + 1:]].append(word)
    return buckets

def neighbours(word, buckets):
    result = []
    for i in range(len(word)):
        for other in buckets.get(word[:i] + "*" + word[i + 1:], []):
            if other != word:
                result.append(other)
    return result
```

**Time:** O(N · L²) to build all buckets · **Space:** O(N · L²): N · L patterns, each a string of length L.

## Walk-through: `words = ["hot","dot","dog","lot","log","cog"]`

The buckets with more than one word:

| pattern | words |
| --- | --- |
| `*ot` | hot, dot, lot |
| `do*` | dot, dog |
| `*og` | dog, log, cog |
| `lo*` | lot, log |

Every other pattern (`h*t`, `ho*`, `d*t`, …) holds a single word.

## Test cases (using the buckets above)

| Call | Expected |
| --- | --- |
| `neighbours("hot", buckets)` | `["dot", "lot"]` (from `*ot`) |
| `neighbours("dog", buckets)` | `["log", "cog", "dot"]` (from `*og`, then `do*`) |
| `neighbours("hit", buckets)` | `["hot"]` (from `h*t`; `hit` itself needn't be in the list) |

Two different words share **at most one** pattern (the position where they differ), so no neighbour is listed twice.

## What changes in the target

Plug `neighbours` into step 01's BFS, starting from `beginWord` with length 1, and stop when `endWord` is popped. Once a bucket has been read, every word in it is visited, so it can be emptied to save work. Then grow the search from **both** ends (bidirectional BFS) → [NOTES](../NOTES.md).
