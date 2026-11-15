# Step 02 · 647. Palindromic Substrings (Medium)

[← Previous: Valid Palindrome](01-lc0125-valid-palindrome.md) · [Roadmap](00-roadmap.md) · [Next: The is-palindrome DP table →](03-concept-dp-is-palindrome-table.md)

**Practise:** [LeetCode 647](https://leetcode.com/problems/palindromic-substrings/)
**Skill it adds:** **expanding around a centre**, for both odd-length and even-length palindromes.

## Problem in brief

Count how many substrings of `s` are palindromes. Substrings at different positions count separately, even if they contain the same letters.

## Key idea

Every palindrome has a **centre**: one character (odd length, like `aba`) or the gap between two characters (even length, like `abba`). A string of length n has `2n − 1` centres.

From each centre, set `left` and `right` and expand while `s[left] == s[right]`. **Each successful step is one more palindrome.** As soon as the ends differ, stop: a bigger one around this centre would need these ends to match.

Checking every substring separately is O(n³). Expanding reuses the inner palindrome, so it is O(n²).

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [expand around centre](../../course/primers/10-1d-dp.md#expand-around-centre)
3. [LeetCode Editorial](https://leetcode.com/problems/palindromic-substrings/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+palindromic+substrings)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def countSubstrings(self, s: str) -> int:
        count = 0
        for centre in range(len(s)):
            # odd length: centre on s[centre]; even: centre between centre and centre+1
            for left, right in ((centre, centre), (centre, centre + 1)):
                while left >= 0 and right < len(s) and s[left] == s[right]:
                    count += 1
                    left -= 1
                    right += 1
        return count
```

</details>

**Time:** O(n²) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `"abc"` | `3` (`a`, `b`, `c`) |
| `"aaa"` | `6` (`a` ×3, `aa` ×2, `aaa`) |
| `"abba"` | `6` (`a`, `b`, `b`, `a`, `bb`, `abba`) |

## Pitfall

Forgetting the **even** centres. `"abba"` would then report 4: `bb` and `abba` have no middle character.

## What changes in the next step

Expanding stops at the first mismatch, which only works because "`s[i..j]` is a palindrome" depends on "`s[i+1..j−1]` is a palindrome". Step 03 writes that dependency down as a **DP table**, which is why NeetCode files this problem under DP.
