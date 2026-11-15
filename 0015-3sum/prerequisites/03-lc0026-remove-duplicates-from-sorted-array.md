# Step 03 · 26. Remove Duplicates from Sorted Array (Easy)

[← Previous: Two Sum II](02-lc0167-two-sum-ii-input-array-is-sorted.md) · [Roadmap](00-roadmap.md) · [Next: All unique pairs →](04-concept-all-unique-pairs-two-sum-ii.md)

**Practise:** [LeetCode 26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
**Skill it adds:** **skipping duplicates** in a sorted array by comparing each item with the one before it.

## Problem in brief

The array is sorted in non-decreasing order. Change it **in place** so that each distinct value appears once at the front, in the original order. Return `k`, the number of distinct values. Whatever is left after the first `k` positions doesn't matter.

## Key idea

In a sorted array, copies of a value sit **next to each other**. So `nums[i]` is the first copy of a new value exactly when `nums[i] != nums[i − 1]`. Keep only those.

Use two pointers moving in the same direction: `i` reads every item, `k` is where the next kept value goes. Everything in `nums[0..k−1]` is already unique.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [skipping duplicates](../../course/primers/02-two-pointers.md#skipping-duplicates)
3. [LeetCode Editorial](https://leetcode.com/problems/remove-duplicates-from-sorted-array/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+remove+duplicates+from+sorted+array)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        k = 1                               # nums[0] is always kept
        for i in range(1, len(nums)):
            if nums[i] != nums[i - 1]:      # first copy of a new value
                nums[k] = nums[i]
                k += 1
        return k
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected `k` | First `k` items |
| --- | --- | --- |
| `[1,1,2]` | `2` | `[1,2]` |
| `[0,0,1,1,1,2,2,3,3,4]` | `5` | `[0,1,2,3,4]` |
| `[1]` | `1` | `[1]` |

## Pitfall

Keep the **first** copy of each block and skip the later ones, by comparing with the **previous** item. In 3Sum, skipping by comparing with the **next** item (`nums[i] == nums[i + 1]`) throws away the first copy, and then triplets that need two equal values, like `[-1,-1,2]`, are lost.

## What changes in the next step

Step 02 found one pair. Step 04 keeps going after a match to find **every** pair, and uses this step's "same as the previous one?" check to skip pairs it has already reported.
