# Step 04 · Concept: naive hash-set walk (and its O(n²) worst case)

[← Previous: Sort, then count runs](03-concept-sort-then-count-runs.md) · [Roadmap](00-roadmap.md) · [Target: Longest Consecutive Sequence →](../NOTES.md)

**Skill it adds:** replacing sorting with set lookups, and spotting **repeated work**.

> 📚 **New to this?** Primer: [hash set](../../course/primers/01-arrays-and-hashing.md#hash-set-and-hash-map)

## Idea

Put everything in a set. From **every** value `x`, walk `x+1, x+2, …` while the next value is in the set, and track the longest walk.

## Code

```python
def longest_consecutive_naive(nums):
    s = set(nums)
    best = 0
    for x in s:
        length = 1
        while x + length in s:
            length += 1
        best = max(best, length)
    return best
```

**Correct, but slow:** O(n²) worst-case time · O(n) space

## Why it's O(n²)

Take `nums = [1, 2, 3, …, n]`:

| start x | values walked |
| --- | --- |
| 1 | 1 → n (n steps) |
| 2 | 2 → n (n − 1 steps) |
| … | … |
| n | n (1 step) |

That's `n + (n−1) + … + 1 = n(n+1)/2 = O(n²)`. The walks from 2, 3, … repeat what the walk from 1 already counted, because **they start in the middle of a run**.

## Test cases (still correct, just slow)

| Input | Expected |
| --- | --- |
| `[100,4,200,1,3,2]` | `4` |
| `[0,3,7,2,5,8,4,6,0,1]` | `9` |
| `[]` | `0` |

## What changes in the target

Only start walking at `x` if `x − 1` is **not** in the set, meaning `x` is the **first** value of its run. Then each value is walked over once, and the whole thing is **O(n)** → [NOTES](../NOTES.md).
