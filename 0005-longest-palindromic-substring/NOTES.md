# 0005. Longest Palindromic Substring

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q12 | [5](https://leetcode.com/problems/longest-palindromic-substring/) | Medium | 1-D DP | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0005-longest-palindromic-substring.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**DNA Motif Finder:** a bioinformatics lab looks for the longest palindromic segment of a DNA string, because such segments often mark binding sites. Return the longest palindromic substring (any one if there's a tie). Give the O(n²) expand-around-centre solution and mention Manacher's O(n) algorithm.

## 2. Pattern recognition

- **Pattern:** 1-D DP / expand around centre
- **Signals in the statement:** "longest **palindromic** substring" and `n ≤ 1000`, so O(n²) is fine but O(n³) is not.
- **Core idea in one line:** every palindrome grows outwards from a centre (a character or a gap), so try all `2n − 1` centres and keep the longest expansion.

## 3. Approaches

### Approach 1: Brute force

Check every substring with the two-pointer test ([step 01](./prerequisites/01-lc0125-valid-palindrome.md)). **Time:** O(n³) · **Space:** O(1)

### Approach 2: DP table ([step 03](./prerequisites/03-concept-dp-is-palindrome-table.md))

`dp[i][j] = s[i] == s[j] and (j − i < 2 or dp[i+1][j−1])`, filled with `i` going down. **Time:** O(n²) · **Space:** O(n²)

### Approach 3: Expand around centre (optimal for interviews)

For each of the `2n − 1` centres, expand while the ends match, then compare the length with the best. **Time:** O(n²) · **Space:** O(1)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        best_start, best_len = 0, 0
        for centre in range(len(s)):
            for left, right in ((centre, centre), (centre, centre + 1)):
                while left >= 0 and right < len(s) and s[left] == s[right]:
                    left -= 1
                    right += 1
                # the loop overshot by one on each side: s[left+1 .. right-1]
                length = right - left - 1
                if length > best_len:
                    best_start, best_len = left + 1, length
        return s[best_start:best_start + best_len]
```

</details>

### Approach 4: Manacher's algorithm (O(n), beyond interview scope)

Insert `#` between characters so every palindrome has odd length, then reuse the **mirror** of each position inside the rightmost palindrome found so far, so the right edge only ever moves forward. **Time:** O(n) · **Space:** O(n)

<details>
<summary><b>Show Manacher's algorithm</b></summary>

```python
def longest_palindrome_manacher(s):
    t = "^#" + "#".join(s) + "#$"        # sentinels ^ and $ stop the expansion
    radius = [0] * len(t)                # radius[i]: palindrome half-width at i in t
    centre = right = 0                   # the palindrome reaching furthest right
    for i in range(1, len(t) - 1):
        if i < right:
            mirror = 2 * centre - i
            radius[i] = min(right - i, radius[mirror])
        while t[i + radius[i] + 1] == t[i - radius[i] - 1]:
            radius[i] += 1
        if i + radius[i] > right:
            centre, right = i, i + radius[i]
    best = max(range(len(t)), key=radius.__getitem__)
    start = (best - radius[best]) // 2   # map back to an index in s
    return s[start:start + radius[best]]
```

`radius[i]` in `t` equals the palindrome's length in `s`. The right edge `right` only grows, and each expansion step pushes it forward, so the total work is O(n).

</details>

## 4. Why it works

- Every palindrome `s[l..r]` has a centre: index `(l + r) / 2` for odd length, or the gap between `(l + r − 1) / 2` and `(l + r + 1) / 2` for even length. So trying all `2n − 1` centres covers every palindrome.
- Expanding from a centre visits the palindromes around it from shortest to longest, and stops at the first mismatch. A longer one there would need that mismatched pair to be equal.
- So each centre's loop ends at the **longest** palindrome around it, and the maximum over all centres is the answer.
- Keeping only a strictly longer result means ties go to the leftmost centre.

## 5. Dry run: TC 1 `s = "babad"` → `"bab"`

| Centre | Kind | Expansion | Palindrome found | Best after |
| --- | --- | --- | --- | --- |
| 0 | odd | `b`; next would be s[−1] | `b` (len 1) | `b` |
| 0 | even | `b` ≠ `a` | — (len 0) | `b` |
| 1 | odd | `a`, then `b` = `b` | `bab` (len 3) | **`bab`** |
| 1 | even | `a` ≠ `b` | — | `bab` |
| 2 | odd | `b`, then `a` = `a`, then `b` ≠ `d` | `aba` (len 3, not longer) | `bab` |
| 2 | even | `b` ≠ `a` | — | `bab` |
| 3 | odd | `a`, then `b` ≠ `d` | `a` | `bab` |
| 3 | even | `a` ≠ `d` | — | `bab` |
| 4 | odd | `d` | `d` | `bab` |
| 4 | even | next would be s[5] (past the end) | — | `bab` |

## 6. Edge cases & pitfalls

- **Even-length palindromes** (TC 2 `"cbbd"` → `"bb"`) need the gap centres. Without them you'd return `"c"`.
- After the `while` loop, `left` and `right` are **one step past** the palindrome. It is `s[left+1 .. right−1]`, length `right − left − 1`.
- A single character (TC 3) is a palindrome of length 1. Starting `best_len` at 0 makes that work automatically.
- All characters equal (`"aaaa"`): the answer is the whole string; still O(n²) work.
- Ties: any longest palindrome is accepted (`"bab"` or `"aba"`).

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `s = "babad"` | `"bab"` (or `"aba"`) |
| TC 2 | `s = "cbbd"` | `"bb"` |
| TC 3 | `s = "a"` | `"a"` |

## 8. Follow-up questions (from course doc)

- **Q: Give the O(n²) expand-around-centre solution.**
  **A:** Approach 3: `2n − 1` centres, each expanded in O(n) at most, with O(1) extra space. It beats the DP table (Approach 2), which has the same time but O(n²) memory.
- **Q: Mention Manacher's algorithm for O(n).**
  **A:** Manacher's algorithm (Approach 4) pads the string with `#` so every palindrome is odd, and tracks the palindrome that reaches furthest right. For a position inside it, the mirror position on the left already tells you a lower bound on its radius, so you only expand past the known right edge. That edge never moves back, so the whole scan is O(n). It's rarely expected in interviews beyond naming it and this one-sentence idea.
- **Q: Why is it filed under DP?**
  **A:** Because "is `s[i..j]` a palindrome" depends on "is `s[i+1..j−1]` a palindrome" (step 03). Expanding around a centre walks that same chain of sub-problems without storing them.

## 9. Threads

- **Q10 / Q2**: two pointers; here they move outwards instead of inwards.
- **Q11** Maximum Product Subarray: the other 1-D DP problem.
- **Q4 / Q9**: other "best substring" problems, solved with sliding windows.

## 10. One-line revision

> Try all 2n − 1 centres (characters and gaps), expand while ends match, keep the longest s[left+1 : right]; O(n²) time, O(1) space; Manacher does it in O(n).
