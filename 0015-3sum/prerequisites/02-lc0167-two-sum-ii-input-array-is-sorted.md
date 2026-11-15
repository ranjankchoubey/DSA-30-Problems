# Step 02 · 167. Two Sum II – Input Array Is Sorted (Medium)

[← Previous: Two Sum](01-lc0001-two-sum.md) · [Roadmap](00-roadmap.md) · [Next: Remove Duplicates from Sorted Array →](03-lc0026-remove-duplicates-from-sorted-array.md)

**Practise:** [LeetCode 167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
**Skill it adds:** **two pointers from opposite ends** of a sorted array, finding a pair in O(n) time and O(1) space.

## Problem in brief

The array is sorted in non-decreasing order. Find the two numbers that add up to `target` and return their positions **1-based**, as `[index1, index2]` with `index1 < index2`. Exactly one answer exists, and you must use only O(1) extra space, so no hash map.

## Key idea

Put `l` at the smallest value and `r` at the largest, then look at `s = numbers[l] + numbers[r]`:

| `s` vs `target` | Why | Move |
| --- | --- | --- |
| `s == target` | found it | return |
| `s < target` | `numbers[l]` plus the **largest** value left is still too small, so `numbers[l]` is in no pair | `l += 1` |
| `s > target` | `numbers[r]` plus the **smallest** value left is still too big, so `numbers[r]` is in no pair | `r -= 1` |

Each step throws away one value that can't be in the answer, so at most `n − 1` steps.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [opposite ends](../../course/primers/02-two-pointers.md#opposite-ends)
3. [LeetCode Editorial](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+two+sum+ii+input+array+is+sorted)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        l, r = 0, len(numbers) - 1
        while l < r:
            s = numbers[l] + numbers[r]
            if s == target:
                return [l + 1, r + 1]      # 1-based
            if s < target:
                l += 1
            else:
                r -= 1
        return []
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `numbers = [2,7,11,15], target = 9` | `[1,2]` |
| `numbers = [2,3,4], target = 6` | `[1,3]` |
| `numbers = [-1,0], target = -1` | `[1,2]` |

## Pitfall

The answer is **1-based**. Returning `[l, r]` is the most common wrong answer on this problem.

## What changes in the next step

Here exactly one pair exists, so we stop at the first match. 3Sum needs **all** matches with **no repeats**, and a sorted array can hold many copies of a value. Step 03 shows how to skip copies in a sorted array.
