# 0076. Minimum Window Substring

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q4 | [76](https://leetcode.com/problems/minimum-window-substring/) | Hard | Sliding Window | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0076-minimum-window-substring.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Shortest Log Window:** a security analyst has a long log string `s` and a set of required event codes `t`, where duplicates matter (`t = "AABC"` needs two A's). Find the smallest contiguous window of `s` that contains every character of `t` with the required counts. Return the leftmost one if several tie, or `""` if none exists. Required: O(|s| + |t|), with lengths up to 100,000.

## 2. Pattern recognition

- **Pattern:** Sliding Window (variable size, minimum) with a have/need counter
- **Signals in the statement:** "smallest **contiguous** window", "contains every character of t", "duplicates matter", linear time. The rule is monotonic: adding characters to a window that covers `t` keeps it covering `t`.
- **Core idea in one line:** grow `right` until the window covers `t`, then shrink `left` while it still does, recording the shortest; `formed == required` checks coverage in O(1).

## 3. Approaches

### Approach 1: Brute force

From every start `i`, extend to the right until the window covers `t`; that is the shortest window starting at `i`. **Time:** O(|s|² · alphabet) · **Space:** O(alphabet)

<details>
<summary><b>Show brute force</b></summary>

```python
from collections import Counter

def min_window_brute(s, t):
    need = Counter(t)
    best = ""
    for i in range(len(s)):
        have = Counter()
        for j in range(i, len(s)):
            have[s[j]] += 1
            if all(have[c] >= need[c] for c in need):
                if best == "" or j - i + 1 < len(best):
                    best = s[i:j + 1]
                break
    return best
```

</details>

### Approach 2: Sliding window, comparing whole count maps

The same two pointers, but checking validity with `all(have[c] >= need[c] for c in need)` on every step, like step 04's table comparison. **Time:** O(|s| · alphabet + |t|) · **Space:** O(alphabet)

### Approach 3: Sliding window + have/need counter (optimal)

Step 02's shrink-while-valid loop with [step 05](./prerequisites/05-concept-have-vs-need-counter.md)'s O(1) check. **Time:** O(|s| + |t|) · **Space:** O(alphabet)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
from collections import Counter

class Solution:
    def minWindow(self, s: str, t: str) -> str:
        need = Counter(t)
        required = len(need)             # distinct characters to cover
        have = {}
        formed = 0                       # distinct characters with have[c] >= need[c]
        left = 0
        best_len = float("inf")
        best_left = 0
        for right, c in enumerate(s):
            have[c] = have.get(c, 0) + 1
            if c in need and have[c] == need[c]:
                formed += 1
            while formed == required:    # valid: record, then try shorter
                if right - left + 1 < best_len:
                    best_len = right - left + 1
                    best_left = left
                d = s[left]
                if d in need and have[d] == need[d]:
                    formed -= 1
                have[d] -= 1
                left += 1
        return "" if best_len == float("inf") else s[best_left:best_left + best_len]
```

</details>

## 4. Why it works

- **`formed` is exact:** it changes only when some `have[c]` crosses `need[c]` (step 05), so `formed == required` holds exactly when the window covers `t` with multiplicity.
- **Nothing is missed:** for each `right`, the inner loop moves `left` as far as it can while the window stays valid, recording every valid window on the way. So it ends at the **shortest** valid window ending at `right`.
- **`left` never needs to move back:** `left` passes position `p` only after recording a valid window that starts at `p`. Any later window starting at `p` ends further right, so it is longer and can't be the minimum.
- **Linear time:** `right` and `left` each move at most |s| times, each step is O(1), and building `need` is O(|t|).

## 5. Dry run: `s = "ADOBECODEBANC", t = "ABC"` → `"BANC"`

`need = {A:1, B:1, C:1}`, `required = 3`.

| right | char | what happens | windows recorded | best |
| --- | --- | --- | --- | --- |
| 0–4 | A D O B E | A and B covered, `formed = 2` | — | — |
| 5 | C | `formed = 3`: shrink from `left = 0` | `ADOBEC` (6) | `ADOBEC` |
| | | remove A → `formed = 2`, `left = 1` | | |
| 6–9 | O D E B | `formed` stays 2 (the second B is an extra copy) | — | `ADOBEC` |
| 10 | A | `formed = 3`: shrink from `left = 1` | `DOBECODEBA` (10) down to `CODEBA` (6), none shorter than 6 | `ADOBEC` |
| | | remove C → `formed = 2`, `left = 6` | | |
| 11 | N | not in `t` | — | `ADOBEC` |
| 12 | C | `formed = 3`: shrink from `left = 6` | `ODEBANC` (7), `DEBANC` (6), `EBANC` (5), `BANC` (4) | **`BANC`** |
| | | remove B → `formed = 2`, `left = 10` | | |

## 6. Edge cases & pitfalls

- **`t` longer than `s`**, or a character of `t` missing from `s` (TC 3): `formed` never reaches `required`, so return `""`.
- **Duplicates in `t`:** `need["A"] = 2` for `"AABC"`, and `formed` counts A only when the window holds two A's.
- **Ties:** update only on a **strictly** shorter window (`<`). Windows of equal length are found in left-to-right order, so the first one found is the leftmost.
- When removing, check `have[d] == need[d]` **before** decrementing. Checking after is off by one.
- Characters are case-sensitive: `"a"` doesn't cover `"A"`.
- Store `best_left` and `best_len` and slice once at the end. Slicing inside the loop costs O(|s|) each time.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `s = "ADOBECODEBANC", t = "ABC"` | `"BANC"` |
| TC 2 | `s = "a", t = "a"` | `"a"` |
| TC 3 | `s = "a", t = "aa"` | `""` |

## 8. Follow-up questions (from course doc)

- **Q: Duplicates matter (`t = "AABC"` needs two A's). How does the solution handle that?**
  **A:** `need` is a count map, not a set, so `need["A"] = 2`. `formed` counts A as covered only when `have["A"]` reaches 2, and un-counts it as soon as it drops back to 1. A set of characters would accept a window with one A.
- **Q: If several windows have the same minimum length, which one is returned?**
  **A:** The leftmost. Windows are recorded as `right` increases, and for a fixed length a larger `right` means a larger `left`. Updating only on a strictly shorter length (`<`) keeps the first, leftmost one.
- **Q: Why is it O(|s| + |t|)?**
  **A:** Building `need` is O(|t|). Each pointer moves forward at most |s| times, and every move does O(1) work: one dictionary update and at most one change to `formed`. There is no per-step scan of the alphabet, which is what `formed` replaces (see Approach 2).

## 9. Threads

- **Q9** Longest Substring with At Most K Distinct: the "longest" version of this loop (shrink while **invalid**, record after).
- **Q5** Sliding Window Maximum: a fixed-size window whose summary is a monotonic deque.
- **Q1** Subarray Sum Equals K: a window needs a monotonic rule, which Q1's negative numbers break.

## 10. One-line revision

> need = Counter(t); grow right and bump formed when have[c] hits need[c]; while formed == required, record the window, then remove s[left] (drop formed if it was exactly at need).

<!-- course-nav:start -->

---

### Course navigation

**Problem 8 of 30** · Sliding Window (2 of 3)

[← Previous: Q9 · Longest Substring with At Most K Distinct Characters](../0340-longest-substring-with-at-most-k-distinct-characters/NOTES.md) · [Course index](../INDEX.md) · [Next: Q5 · Sliding Window Maximum →](../0239-sliding-window-maximum/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
