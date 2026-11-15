# Step 02 · 3. Longest Substring Without Repeating Characters (Medium)

[← Previous: Maximum Average Subarray I](01-lc0643-maximum-average-subarray-i.md) · [Roadmap](00-roadmap.md) · [Next: Fruit Into Baskets →](03-lc0904-fruit-into-baskets.md)

**Practise:** [LeetCode 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
**Skill it adds:** the **variable-size window**. Expand on the right; while the window breaks the rule, shrink it from the left.

## Problem in brief

Given a string `s`, return the length of the longest **substring** (a contiguous piece) in which no character appears twice.

## Key idea

Keep a window `s[left .. right]` with no repeats, and a set of the characters inside it.
- Move `right` one step at a time. Before adding `c = s[right]`, if `c` is already in the set, remove `s[left]` and move `left` forward, **repeating** until `c` is gone.
- Now adding `c` keeps the window repeat-free. Record its length `right − left + 1`.

Each character enters the set once and leaves at most once, so the whole scan is O(n).

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [variable size window](../../course/primers/03-sliding-window.md#variable-size-window)
3. [LeetCode Editorial](https://leetcode.com/problems/longest-substring-without-repeating-characters/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+longest+substring+without+repeating+characters)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        window = set()
        left = 0
        best = 0
        for right, c in enumerate(s):
            while c in window:               # adding c would create a repeat
                window.remove(s[left])
                left += 1
            window.add(c)
            best = max(best, right - left + 1)
        return best
```

Walk-through on `"pwwkew"`:

| right | c | shrink | window after | best |
| --- | --- | --- | --- | --- |
| 0 | p | — | `p` | 1 |
| 1 | w | — | `pw` | 2 |
| 2 | w | remove `p`, `w` | `w` | 2 |
| 3 | k | — | `wk` | 2 |
| 4 | e | — | `wke` | **3** |
| 5 | w | remove `w` | `kew` | 3 |

</details>

**Time:** O(n) · **Space:** O(min(n, alphabet size))

## Test cases

| Input | Expected |
| --- | --- |
| `"abcabcbb"` | `3` (`"abc"`) |
| `"bbbbb"` | `1` (`"b"`) |
| `"pwwkew"` | `3` (`"wke"`) |

## Pitfall

- `"pwke"` is a **subsequence**, not a substring. The window must stay contiguous.
- Use `while`, not `if`, when shrinking: in `"pwwkew"` at `right = 2`, two characters have to leave.

## What changes in the next step

The rule changes from "no character twice" to "**at most 2 distinct** values", and repeats are now allowed. A set can't tell you when the last copy of a value leaves the window, so Fruit Into Baskets switches to a **count map** and shrinks while `len(count) > 2`.
