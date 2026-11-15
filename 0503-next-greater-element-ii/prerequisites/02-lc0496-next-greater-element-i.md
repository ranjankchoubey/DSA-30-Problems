# Step 02 · 496. Next Greater Element I (Easy)

[← Previous: Daily Temperatures](01-lc0739-daily-temperatures.md) · [Roadmap](00-roadmap.md) · [Next: Circular array, iterate 2n →](03-concept-circular-array-iterate-2n.md)

**Practise:** [LeetCode 496](https://leetcode.com/problems/next-greater-element-i/)
**Skill it adds:** computing the next greater element for **every** value in one pass, then answering lookups from a hash map.

## Problem in brief

All values are distinct, and every value of `nums1` also appears in `nums2`. For each `x` in `nums1`, find `x` inside `nums2` and return the first value to its right in `nums2` that is larger than `x`, or `-1` if there isn't one.

## Key idea

| Approach | Time | Space |
| --- | --- | --- |
| For each query, find `x` in `nums2` and scan right | O(m · n) | O(1) |
| **One monotonic-stack pass over `nums2` + hash map** | **O(m + n)** | **O(n)** |

Run the step 01 pass over `nums2` once, recording `next_greater[value] = the value that popped it`. Values still on the stack at the end have no bigger value. Each query is then a dictionary lookup. Since the values are distinct, the stack can hold values and the map can use them as keys.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [monotonic stack](../../course/primers/04-stack.md#monotonic-stack)
3. [LeetCode Editorial](https://leetcode.com/problems/next-greater-element-i/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+next+greater+element+i)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        next_greater = {}
        stack = []                        # values still waiting; decreasing bottom → top
        for x in nums2:
            while stack and stack[-1] < x:
                next_greater[stack.pop()] = x
            stack.append(x)
        return [next_greater.get(x, -1) for x in nums1]
```

</details>

**Time:** O(m + n) · **Space:** O(n)

## Test cases

| Input | Expected |
| --- | --- |
| `nums1 = [4,1,2], nums2 = [1,3,4,2]` | `[-1,3,-1]` |
| `nums1 = [2,4], nums2 = [1,2,3,4]` | `[3,-1]` |
| `nums1 = [6,2], nums2 = [2,6,1,5]` | `[-1,6]` |

## Pitfall

Values left on the stack after the loop never found a bigger value. `.get(x, -1)` gives them `-1`. Forgetting this causes a `KeyError`.

## What changes in the next step

Next Greater Element II is **circular**: a value left on the stack at the end may still find a bigger value back at the **start**. Values can also repeat, so the answer must be stored by **index**, not by value. Step 03 shows how to walk a circle with `i % n`.
