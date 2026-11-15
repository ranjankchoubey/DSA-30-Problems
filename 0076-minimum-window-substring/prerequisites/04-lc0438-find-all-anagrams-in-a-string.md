# Step 04 · 438. Find All Anagrams in a String (Medium)

[← Previous: Valid Anagram](03-lc0242-valid-anagram.md) · [Roadmap](00-roadmap.md) · [Next: Have vs need counter →](05-concept-have-vs-need-counter.md)

**Practise:** [LeetCode 438](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
**Skill it adds:** a **sliding window matched against a frequency map**, updated in O(1) per step.

## Problem in brief

Given strings `s` and `p` (lowercase letters), return every start index `i` where the piece of `s` of length `len(p)` starting at `i` is an anagram of `p`. The order of the output doesn't matter.

## Key idea

An anagram of `p` has length `len(p)`, so this is a **fixed-size window** of `k = len(p)`.
- Count `p` into `need` (26 slots, one per letter).
- Slide over `s`, keeping `window` counts: add `s[right]`, and once `right ≥ k` remove `s[right − k]`.
- When `window == need`, record the start `right − k + 1`.

Comparing two 26-slot lists is O(26), which is a constant.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [counting characters in a window](../../course/primers/03-sliding-window.md#counting-characters-in-a-window)
3. [LeetCode Editorial](https://leetcode.com/problems/find-all-anagrams-in-a-string/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+find+all+anagrams+in+a+string)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        k = len(p)
        need = [0] * 26
        window = [0] * 26
        for c in p:
            need[ord(c) - ord("a")] += 1
        result = []
        for right, c in enumerate(s):
            window[ord(c) - ord("a")] += 1
            if right >= k:                               # s[right-k] leaves
                window[ord(s[right - k]) - ord("a")] -= 1
            if window == need:
                result.append(right - k + 1)
        return result
```

</details>

**Time:** O(26·|s| + |p|) = O(|s| + |p|) · **Space:** O(1) (two 26-slot lists)

## Test cases

| Input | Expected |
| --- | --- |
| `s = "cbaebabacd", p = "abc"` | `[0, 6]` (`"cba"`, `"bac"`) |
| `s = "abab", p = "ab"` | `[0, 1, 2]` |
| `s = "a", p = "ab"` | `[]` (`p` is longer than `s`) |

## Pitfall

The character that leaves is `s[right − k]`, not `s[right − k + 1]`: after adding `s[right]` the window must be `s[right − k + 1 .. right]`, which is exactly `k` characters.

## What changes in the next step

The target has two problems with this approach:
1. Its window size is **variable**, and its alphabet is upper- and lowercase letters. Comparing whole count tables on every step costs O(alphabet) each time.
2. It needs "**at least** the counts of `t`", not "exactly", because extra characters are allowed in the window.

Step 05 replaces the table comparison with a single counter, `formed`, that answers "is every character covered?" in O(1).
