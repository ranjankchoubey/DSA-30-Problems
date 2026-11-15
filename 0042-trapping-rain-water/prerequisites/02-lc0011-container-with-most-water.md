# Step 02 · 11. Container With Most Water (Medium)

[← Previous: Find Pivot Index](01-lc0724-find-pivot-index.md) · [Roadmap](00-roadmap.md) · [Next: Water at one index →](03-concept-water-at-index.md)

**Practise:** [LeetCode 11](https://leetcode.com/problems/container-with-most-water/)
**Skill it adds:** two pointers at **both ends**, always moving the **shorter** side.

## Problem in brief

`height[i]` is a vertical line at position `i`. Choose two lines; with the x-axis they form a container holding `min(height[i], height[j]) × (j − i)` water. Return the largest amount any pair can hold. The lines in between don't block anything.

## Key idea

Start with the widest container, `l = 0` and `r = n − 1`. The **shorter** line sets the water level. Every other container that uses the shorter line is **narrower** and its level is **at most** that short line, so none of them can hold more. So the shorter line can be dropped: move its pointer inward. Moving the taller one can never help.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [opposite ends](../../course/primers/02-two-pointers.md#opposite-ends)
3. [LeetCode Editorial](https://leetcode.com/problems/container-with-most-water/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+container+with+most+water)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        l, r = 0, len(height) - 1
        best = 0
        while l < r:
            area = min(height[l], height[r]) * (r - l)
            best = max(best, area)
            if height[l] < height[r]:
                l += 1              # the left line is the limit; drop it
            else:
                r -= 1              # the right line is the limit (or a tie)
        return best
```

</details>

**Time:** O(n) · **Space:** O(1)

## Test cases

| Input | Expected |
| --- | --- |
| `[1,8,6,2,5,4,8,3,7]` | `49` (indices 1 and 8: min(8, 7) × 7) |
| `[1,1]` | `1` |
| `[4,3,2,1,4]` | `16` (the two outer 4s: 4 × 4) |

## Pitfall

On a tie either pointer may move. Both lines limit every narrower container that uses them, so dropping either one is safe.

## What changes in the next step

Here only the **two chosen walls** matter. In Trapping Rain Water every bar holds its own column of water, and the bars in between **do** matter. Step 03 works out the water above a single bar.
