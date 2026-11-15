# Step 01 · 217. Contains Duplicate (Easy)

[← Roadmap](00-roadmap.md) · [Next: Missing Number →](02-lc0268-missing-number.md)

**Practise:** [LeetCode 217](https://leetcode.com/problems/contains-duplicate/)
**Skill it adds:** a hash set answers "have I seen this value before?" in O(1) average time.

## Problem in brief

Return `True` if any value appears at least twice in `nums`, otherwise `False`.

## Key idea

| Approach | Time | Space |
| --- | --- | --- |
| Compare every pair | O(n²) | O(1) |
| Sort, then compare neighbours | O(n log n) | O(1) to O(n) |
| **Hash set of seen values** | **O(n)** | **O(n)** |

Scan once. Before adding `x` to the set, check whether it's already there.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [hash set and hash map](../../course/primers/01-arrays-and-hashing.md#hash-set-and-hash-map)
3. [LeetCode Editorial](https://leetcode.com/problems/contains-duplicate/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+contains+duplicate)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        seen = set()
        for x in nums:
            if x in seen:
                return True
            seen.add(x)
        return False
```

</details>

**Time:** O(n) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,2,3,1]` | `True` |
| `[1,2,3,4]` | `False` |
| `[1,1,1,3,3,4,3,2,4,2]` | `True` |

## What changes in the next step

In Set Mismatch, the first `x` already in `seen` **is** the duplicate. We still need the **missing** number, and step 02 finds it with arithmetic instead of extra memory.
