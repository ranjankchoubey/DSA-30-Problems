# Step 03 · 242. Valid Anagram (Easy)

[← Previous: Minimum Size Subarray Sum](02-lc0209-minimum-size-subarray-sum.md) · [Roadmap](00-roadmap.md) · [Next: Find All Anagrams in a String →](04-lc0438-find-all-anagrams-in-a-string.md)

**Practise:** [LeetCode 242](https://leetcode.com/problems/valid-anagram/)
**Skill it adds:** **character frequency maps**. Two strings use the same letters exactly when their count maps are equal.

## Problem in brief

Given two strings `s` and `t`, return `True` if `t` is a rearrangement of `s`: the same characters, each used the same number of times. Otherwise return `False`.

## Key idea

Count every character: `+1` for each character of `s`, `−1` for each character of `t`. They are anagrams exactly when every count ends at 0. If the lengths differ, return `False` straight away.

`Counter(s) == Counter(t)` does the same in one line.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [hash set and hash map](../../course/primers/01-arrays-and-hashing.md#hash-set-and-hash-map)
3. [LeetCode Editorial](https://leetcode.com/problems/valid-anagram/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+valid+anagram)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False
        count = {}
        for a, b in zip(s, t):
            count[a] = count.get(a, 0) + 1
            count[b] = count.get(b, 0) - 1
        return all(v == 0 for v in count.values())
```

</details>

**Time:** O(n) · **Space:** O(1) for a fixed alphabet (26 lowercase letters), O(distinct characters) in general

## Test cases

| Input | Expected |
| --- | --- |
| `s = "anagram", t = "nagaram"` | `True` |
| `s = "rat", t = "car"` | `False` |
| `s = "aacc", t = "ccac"` | `False` (same letters, different counts) |

## Pitfall

Comparing **sets** of characters (`set(s) == set(t)`) ignores counts, so test 3 would wrongly return `True`. Multiplicity matters, just as `t = "AABC"` needs two A's in the target.

## What changes in the next step

Here both strings are fixed. In Find All Anagrams, one side is a **window sliding over `s`**, so its count map must be updated in O(1) per step (add the entering character, remove the leaving one) instead of being rebuilt.
