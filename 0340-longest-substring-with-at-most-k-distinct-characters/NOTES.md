# 0340. Longest Substring with At Most K Distinct Characters

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q9 | [340](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) 🔒 | Medium | Sliding Window | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0340-longest-substring-with-at-most-k-distinct-characters.py` (save it yourself, see the 🔒 note below)

> 🔒 **LeetCode Premium:** LeetHub can't sync this without Premium. Save your solution yourself as `0340-longest-substring-with-at-most-k-distinct-characters/0340-longest-substring-with-at-most-k-distinct-characters.py`, or practise the free alternative: [904. Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) (k = 2).

## 1. Case scenario

**Limited-Variety Playlist:** a playlist is a string of track-type letters. Find the length of the longest continuous stretch that uses **at most `k` distinct** track types. Then explain what you can still guarantee if the playlist arrives as a **stream** and you may keep only **O(k)** memory. `|s|` is up to 500,000, so it must be O(n).

## 2. Pattern recognition

- **Pattern:** Sliding Window (variable size) with a count map
- **Signals in the statement:** "longest **continuous** stretch", "**at most** k distinct". The rule is monotonic: removing characters from a valid window can't add a new type, so it stays valid.
- **Core idea in one line:** grow the window on the right; while it has more than `k` distinct characters, shrink it from the left; record the length.

## 3. Approaches

### Approach 1: Brute force

From every start `i`, extend to the right with a set of seen types and stop when a `(k+1)`-th type appears. **Time:** O(n²) · **Space:** O(k)

<details>
<summary><b>Show brute force</b></summary>

```python
def longest_k_distinct_brute(s, k):
    best = 0
    for i in range(len(s)):
        seen = set()
        for j in range(i, len(s)):
            seen.add(s[j])
            if len(seen) > k:
                break
            best = max(best, j - i + 1)
    return best
```

</details>

### Approach 2: Sliding window + count map (optimal)

Step 03 (Fruit Into Baskets) with `2` replaced by `k`. **Time:** O(n) · **Space:** O(k)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def lengthOfLongestSubstringKDistinct(self, s: str, k: int) -> int:
        count = {}
        left = 0
        best = 0
        for right, c in enumerate(s):
            count[c] = count.get(c, 0) + 1
            while len(count) > k:            # too many types: shrink
                d = s[left]
                count[d] -= 1
                if count[d] == 0:
                    del count[d]
                left += 1
            best = max(best, right - left + 1)
        return best
```

</details>

### Approach 3: Last-seen index, O(k) memory (for a stream)

Instead of counts, store each type's **last-seen index** in an `OrderedDict` ordered from least to most recently seen. When a `(k+1)`-th type arrives, evict the least recently seen type; the window now starts just after its last occurrence. It never needs to read `s[left]` again, so it works on a stream (see §8). **Time:** O(n) · **Space:** O(k)

<details>
<summary><b>Show the stream version</b></summary>

```python
from collections import OrderedDict

def longest_k_distinct_stream(stream, k):
    last = OrderedDict()                     # type -> last index, least recent first
    left = 0
    best = 0
    for i, c in enumerate(stream):
        if c in last:
            last.move_to_end(c)
        last[c] = i
        if len(last) > k:
            _, idx = last.popitem(last=False)   # least recently seen type
            left = idx + 1
        best = max(best, i - left + 1)
    return best
```

</details>

## 4. Why it works

- **Invariant:** after the `while` loop, `s[left .. right]` has at most `k` distinct characters, and `count` holds exactly the characters in it (zero counts are deleted, so `len(count)` is the number of types).
- **Nothing is missed:** `left` only moves when the window has `k + 1` types. Any window that starts further left and ends at `right` contains this one, so it also has too many types. So `s[left .. right]` is the **longest** valid window ending at `right`, and taking the max over every `right` gives the answer.
- **Linear time:** `right` moves n times and `left` moves at most n times in total, and each move is O(1).

## 5. Dry run: `s = "eceba", k = 2` → 3

| right | c | count after adding | shrink | window | best |
| --- | --- | --- | --- | --- | --- |
| 0 | e | {e:1} | — | `e` | 1 |
| 1 | c | {e:1, c:1} | — | `ec` | 2 |
| 2 | e | {e:2, c:1} | — | `ece` | **3** |
| 3 | b | {e:2, c:1, b:1} | remove `e`, `c` → {e:1, b:1} | `eb` | 3 |
| 4 | a | {e:1, b:1, a:1} | remove `e` → {b:1, a:1} | `ba` | 3 |

## 6. Edge cases & pitfalls

- **`k = 0`:** every character is removed as soon as it is added, so the answer is `0`. No special case is needed.
- **`k` ≥ number of distinct characters:** the window never shrinks, so the answer is `len(s)` (TC 2).
- Delete keys whose count reaches 0. Otherwise `len(count)` overcounts and the window shrinks too much.
- Shrink with `while`, not `if`: one removal might not take a type out (step 03's pitfall).
- Record `best` **after** shrinking, when the window is valid.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `s = "eceba", k = 2` | `3` (`"ece"`) |
| TC 2 | `s = "aa", k = 1` | `2` |
| TC 3 | `s = "abaccc", k = 2` | `4` (`"accc"`) |

## 8. Follow-up questions (from course doc)

- **Q: What can you still guarantee if the playlist arrives as a stream and you can keep only O(k) memory?**
  **A:** The count-map solution has to read `s[left]` again when it shrinks, so on a stream it would need to buffer the whole window, which can be O(n). Approach 3 avoids that: it keeps only each type's **last-seen index** (at most `k + 1` entries). When a `(k+1)`-th type arrives, it evicts the type seen least recently and sets `left` just after that type's last index.
  You can still guarantee:
  - the **exact** longest length so far, updated after every character (an online answer);
  - the **start and end positions** of a best stretch (store `left` and `i` when `best` improves);
  - O(1) amortised time per character with an `OrderedDict` (a plain dict plus a scan for the smallest index costs O(k) per eviction).

  You **can't** return the stretch's actual contents unless you buffer them or can re-read the source by position. Evicting the least recently seen key is exactly the policy of an LRU cache (Q13).

## 9. Threads

- **Q4** Minimum Window Substring: the same two pointers, but shrink **while valid** to find a minimum.
- **Q5** Sliding Window Maximum: a fixed-size window with a monotonic deque.
- **Q1** Subarray Sum Equals K: a window needs a monotonic rule. With negative sums it doesn't have one, so Q1 uses prefix sums.
- **Q13** LRU Cache: the `OrderedDict` "evict the least recently used" trick from §8.

## 10. One-line revision

> Grow right, count types in a dict; while it has more than k keys, decrement s[left] (delete at 0) and move left; best = max(best, right − left + 1).

<!-- course-nav:start -->

---

### Course navigation

**Problem 7 of 30** · Sliding Window (1 of 3)

[← Previous: Q2 · Trapping Rain Water](../0042-trapping-rain-water/NOTES.md) · [Course index](../INDEX.md) · [Next: Q4 · Minimum Window Substring →](../0076-minimum-window-substring/prerequisites/00-roadmap.md)

<!-- course-nav:end -->
