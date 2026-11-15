# Step 02 · 433. Minimum Genetic Mutation (Medium)

[← Previous: Shortest Path in Binary Matrix](01-lc1091-shortest-path-in-binary-matrix.md) · [Roadmap](00-roadmap.md) · [Next: Wildcard buckets →](03-concept-wildcard-buckets.md)

**Practise:** [LeetCode 433](https://leetcode.com/problems/minimum-genetic-mutation/)
**Skill it adds:** **strings as graph nodes**. A move changes one letter, and only strings in an allowed set are valid. It's Word Ladder in miniature.

## Problem in brief

A gene is an 8-character string over `A`, `C`, `G`, `T`. One mutation changes exactly one character. Every gene you pass through (except the start) must appear in `bank`. Return the minimum number of mutations to turn `startGene` into `endGene`, or `-1` if it can't be done.

## Key idea

Nobody gives you the graph; you **generate** it. The neighbours of a gene are the strings you get by changing one position to one of the other 3 letters, kept only if they're in `bank`. That's 8 × 4 = 32 candidates per gene. Run step 01's BFS on that implicit graph and count **edges** (mutations).

Put `bank` in a `set` so each candidate check is O(1).

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [BFS](../../course/primers/08-graphs.md#bfs)
3. [LeetCode Editorial](https://leetcode.com/problems/minimum-genetic-mutation/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+minimum+genetic+mutation)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def minMutation(self, startGene: str, endGene: str, bank: List[str]) -> int:
        valid = set(bank)
        if endGene not in valid:
            return -1
        seen = {startGene}
        queue = deque([(startGene, 0)])           # (gene, mutations so far)
        while queue:
            gene, steps = queue.popleft()
            if gene == endGene:
                return steps
            for i in range(len(gene)):
                for ch in "ACGT":
                    nxt = gene[:i] + ch + gene[i + 1:]
                    if nxt in valid and nxt not in seen:
                        seen.add(nxt)
                        queue.append((nxt, steps + 1))
        return -1
```

</details>

**Time:** O(B · L · 4 · L), where B = bank size and L = 8: each gene builds 4L candidate strings of length L · **Space:** O(B · L)

## Test cases

| Input | Expected |
| --- | --- |
| `"AACCGGTT" → "AACCGGTA"`, bank `["AACCGGTA"]` | `1` |
| `"AACCGGTT" → "AAACGGTA"`, bank `["AACCGGTA","AACCGCTA","AAACGGTA"]` | `2` (via `AACCGGTA`) |
| `"AAAAACCC" → "AACCCCCC"`, bank `["AAAACCCC","AAACCCCC","AACCCCCC"]` | `3` |

## Pitfall

`startGene` does **not** have to be in `bank`, but `endGene` does. Don't reject the start for being missing, and do return `-1` early when the end is missing.

Storing the distance in the queue as `(gene, steps)` is an alternative to step 01's level loop; both work. What doesn't work is one shared counter incremented on every pop: that counts genes popped, not levels.

## What changes in the next step

Here the alphabet has 4 letters and the bank at most 10 genes. Word Ladder has 26 letters, words up to 10 letters, and 5000 words, so it pays to find neighbours faster. Step 03 groups words by **pattern** (`h*t`) so that all words one letter apart sit in the same bucket.
