# Step 02 · 953. Verifying an Alien Dictionary (Easy)

[← Previous: Course Schedule II](01-q25-course-schedule-ii.md) · [Roadmap](00-roadmap.md) · [Next: First difference gives an edge →](03-concept-first-difference-gives-edge.md)

**Practise:** [LeetCode 953](https://leetcode.com/problems/verifying-an-alien-dictionary/)
**Skill it adds:** comparing words under a **custom** alphabet, and seeing that only **adjacent** pairs and their **first** different letter matter.

## Problem in brief

You get a list of words and a string `order` that is a reordering of the 26 lowercase letters. Return `True` if the words are sorted according to that alphabet, otherwise `False`.

## Key idea

- Map each letter to its position: `rank = {c: i for i, c in enumerate(order)}`.
- A list is sorted if **every adjacent pair** is in order, so check `words[i]` against `words[i + 1]` only.
- Walk both words together. At the **first** position where the letters differ, that pair of letters decides everything; later letters don't matter.
- If there's no difference before one word ends, the **shorter** word must come first. `"apple"` before `"app"` is wrong.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [hash map](../../course/primers/01-arrays-and-hashing.md#hash-set-and-hash-map)
3. [LeetCode Editorial](https://leetcode.com/problems/verifying-an-alien-dictionary/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+verifying+an+alien+dictionary)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def isAlienSorted(self, words: List[str], order: str) -> bool:
        rank = {c: i for i, c in enumerate(order)}
        for a, b in zip(words, words[1:]):
            for x, y in zip(a, b):
                if x != y:
                    if rank[x] > rank[y]:
                        return False
                    break                # first difference decides
            else:                        # no difference found
                if len(a) > len(b):
                    return False         # longer word before its prefix
        return True
```

</details>

**Time:** O(total letters) · **Space:** O(1) (the rank map has 26 entries)

## Test cases

| Input | Expected |
| --- | --- |
| `words = ["hello","leetcode"]`, `order = "hlabcdefgijkmnopqrstuvwxyz"` | `True` (`h` before `l`) |
| `words = ["word","world","row"]`, `order = "worldabcefghijkmnpqstuvxyz"` | `False` (first difference `d` vs `l`, and `d` comes after `l`) |
| `words = ["apple","app"]`, `order = "abcdefghijklmnopqrstuvwxyz"` | `False` (prefix case) |

## Pitfall

Python's `for … else` runs the `else` only when the loop **didn't** `break`. That's exactly "no differing letter was found". If that feels unclear, use a flag variable instead.

## What changes in the next step

Here the alphabet is **given** and we check the words. Step 03 flips it: the words are given and the alphabet is unknown. Each adjacent pair's first different letters `x, y` now **tell** us that `x` comes before `y`, which is one edge of a graph.
