# Step 03 · Concept: the is-palindrome DP table

[← Previous: Palindromic Substrings](02-lc0647-palindromic-substrings.md) · [Roadmap](00-roadmap.md) · [Target: Longest Palindromic Substring →](../NOTES.md)

> 📚 **New to this?** Primer: [table over substrings](../../course/primers/10-1d-dp.md#table-over-substrings)

**Skill it adds:** writing "is `s[i..j]` a palindrome?" as a **recurrence** on smaller substrings, and filling a table in the right order. This is why NeetCode files the target under DP.

## Idea

`s[i..j]` is a palindrome exactly when its two ends match **and** what's inside is a palindrome:

```
dp[i][j] = s[i] == s[j] and (j - i < 2 or dp[i+1][j-1])
```

- `j − i < 2` covers the base cases: one character (`i == j`) or two equal characters (`j == i + 1`), which have nothing inside.
- `dp[i][j]` needs `dp[i+1][j−1]`, a cell with a **bigger** `i`. So fill `i` from `n − 1` **down** to 0, and `j` from `i` up.
- Whenever a cell is `True`, compare its length `j − i + 1` with the best so far.

## Code

```python
def longest_palindrome_dp(s):
    n = len(s)
    dp = [[False] * n for _ in range(n)]
    best_start, best_len = 0, 1
    for i in range(n - 1, -1, -1):
        for j in range(i, n):
            if s[i] == s[j] and (j - i < 2 or dp[i + 1][j - 1]):
                dp[i][j] = True
                if j - i + 1 > best_len:
                    best_start, best_len = i, j - i + 1
    return s[best_start:best_start + best_len]
```

**Time:** O(n²) · **Space:** O(n²)

## Walk-through: `"cbbd"`

`T` = palindrome, `·` = not, blank = unused (`j < i`).

| i \ j | 0 `c` | 1 `b` | 2 `b` | 3 `d` |
| --- | --- | --- | --- | --- |
| 0 `c` | T | · | · | · |
| 1 `b` | | T | **T** | · |
| 2 `b` | | | T | · |
| 3 `d` | | | | T |

The only cell longer than 1 is `dp[1][2]` (`"bb"`), so the answer is `"bb"`. `dp[0][3]` is `·` because `c ≠ d`, without even looking inside.

## Test cases

| Input | Expected |
| --- | --- |
| `"babad"` | `"aba"` (`"bab"` is equally valid; this fill order finds `"aba"` first) |
| `"cbbd"` | `"bb"` |
| `"a"` | `"a"` |

## What changes in the target

The table answers every `(i, j)` question, but the longest palindrome only needs one answer. Step 02's expansion visits the same `True` cells, growing outwards from each centre, and stops at the first `·`. So it gets the same O(n²) time with **O(1)** space. The target uses expansion and keeps the longest → [NOTES](../NOTES.md).
