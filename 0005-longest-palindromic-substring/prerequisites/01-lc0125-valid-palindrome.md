# Step 01 · 125. Valid Palindrome (Easy)

[← Roadmap](00-roadmap.md) · [Next: Palindromic Substrings →](02-lc0647-palindromic-substrings.md)

**Practise:** [LeetCode 125](https://leetcode.com/problems/valid-palindrome/)
**Skill it adds:** checking a palindrome with **two pointers** that start at the ends and move inwards.

## Problem in brief

Ignore every character that isn't a letter or a digit, and ignore upper/lower case. Return `True` if what's left reads the same forwards and backwards.

## Key idea

Put `left` at the start and `right` at the end. Skip anything that isn't alphanumeric (`str.isalnum()`). Compare the two characters in lower case: if they differ, it's not a palindrome. Otherwise move both inwards. If the pointers meet, it is a palindrome.

No need to build a cleaned copy of the string, so extra space is O(1).

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [opposite ends](../../course/primers/02-two-pointers.md#opposite-ends)
3. [LeetCode Editorial](https://leetcode.com/problems/valid-palindrome/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+valid+palindrome)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        left, right = 0, len(s) - 1
        while left < right:
            if not s[left].isalnum():
                left += 1
            elif not s[right].isalnum():
                right -= 1
            elif s[left].lower() != s[right].lower():
                return False
            else:
                left += 1
                right -= 1
        return True
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `"A man, a plan, a canal: Panama"` | `True` (`amanaplanacanalpanama`) |
| `"race a car"` | `False` (`raceacar`) |
| `" "` | `True` (nothing left, and an empty string is a palindrome) |

## What changes in the next step

Here we're told **where** the candidate starts and ends, and we walk **inwards**. To find palindromes **inside** a string, step 02 walks **outwards** from a centre instead, so one walk checks many substrings at once.
