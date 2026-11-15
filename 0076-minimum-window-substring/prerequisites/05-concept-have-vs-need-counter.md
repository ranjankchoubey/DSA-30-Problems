# Step 05 · Concept: have vs need counter

[← Previous: Find All Anagrams in a String](04-lc0438-find-all-anagrams-in-a-string.md) · [Roadmap](00-roadmap.md) · [Target: Minimum Window Substring →](../NOTES.md)

> 📚 **New to this?** Primer: [counting characters in a window](../../course/primers/03-sliding-window.md#counting-characters-in-a-window)

**Skill it adds:** checking "does the window cover every character of `t`, with multiplicity?" in **O(1)** with `formed == required`.

## Idea

- `need = Counter(t)`: how many of each character `t` requires.
- `required = len(need)`: how many **distinct** characters must be covered.
- `have`: the counts inside the window.
- `formed`: how many distinct characters currently have `have[c] ≥ need[c]`.

`formed` only changes when a count **crosses** its requirement:
- **Add `c`:** after `have[c] += 1`, if `have[c] == need[c]`, then `c` just became covered, so `formed += 1`.
- **Remove `c`:** if `have[c] == need[c]` **before** `have[c] -= 1`, then `c` is about to drop below, so `formed −= 1`.

Extra copies (`have[c] > need[c]`) and characters not in `t` never change `formed`. The window covers `t` exactly when `formed == required`.

## Code

Step 04 again, with the 26-slot comparison replaced by the counter:

```python
from collections import Counter

def find_anagrams_formed(s, p):
    need = Counter(p)
    required = len(need)             # distinct characters to cover
    have = {}
    formed = 0                       # distinct characters with have[c] >= need[c]
    k = len(p)
    result = []
    for right, c in enumerate(s):
        have[c] = have.get(c, 0) + 1
        if c in need and have[c] == need[c]:
            formed += 1
        if right >= k:
            d = s[right - k]         # d leaves the window
            if d in need and have[d] == need[d]:
                formed -= 1
            have[d] -= 1
        if formed == required:
            result.append(right - k + 1)
    return result
```

With a window of exactly `len(p)` characters, "covers `p`" and "is an anagram of `p`" mean the same thing: the counts can't exceed `need` without some other count falling short.

**Time:** O(|s| + |p|) · **Space:** O(distinct characters in `s` and `p`)

## Walk-through: `s = "cbaebabacd", p = "abc"` (need {a:1, b:1, c:1}, required 3)

| right | adds | removes | window | formed | result |
| --- | --- | --- | --- | --- | --- |
| 0 | c | — | `c` | 1 | [] |
| 1 | b | — | `cb` | 2 | [] |
| 2 | a | — | `cba` | **3** | [0] |
| 3 | e | c | `bae` | 2 | [0] |
| 4 | b | b | `aeb` | 2 | [0] |
| 5 | a | a | `eba` | 2 | [0] |
| 6 | b | e | `bab` | 2 | [0] |
| 7 | a | b | `aba` | 2 | [0] |
| 8 | c | a | `bac` | **3** | [0, 6] |
| 9 | d | b | `acd` | 2 | [0, 6] |

At `right = 6` the window `bab` has two b's, but `formed` counted `b` only once, when its count first reached 1.

## Test cases

| Input | Expected |
| --- | --- |
| `s = "cbaebabacd", p = "abc"` | `[0, 6]` |
| `s = "abab", p = "ab"` | `[0, 1, 2]` |
| `s = "a", p = "ab"` | `[]` |

## What changes in the target

The window is no longer a fixed `len(t)`. Use step 02's loop instead: grow `right` until `formed == required`, then **shrink while `formed == required`**, recording the shortest window (and its start) before each removal → [NOTES](../NOTES.md).
