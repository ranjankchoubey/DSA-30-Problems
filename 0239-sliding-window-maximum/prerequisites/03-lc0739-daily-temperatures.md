# Step 03 · 739. Daily Temperatures (Medium)

[← Previous: Brute force and heap](02-concept-brute-force-and-heap.md) · [Roadmap](00-roadmap.md) · [Next: Monotonic deque →](04-concept-monotonic-deque.md)

**Practise:** [LeetCode 739](https://leetcode.com/problems/daily-temperatures/)
**Skill it adds:** the **monotonic stack**. Keep a stack whose values stay in sorted order, and pop everything a new value "beats". Each element is pushed once and popped at most once, so it runs in O(n).

## Problem in brief

You get a list of daily temperatures. For each day, return how many days you have to wait until a **strictly warmer** day. If no warmer day comes, the answer for that day is `0`.

## Key idea

The brute force checks every later day for every day: O(n²). Instead, scan left to right and keep a **stack of days still waiting** for a warmer day.
- The waiting days' temperatures **decrease** from the bottom of the stack to the top. (If a warmer day were above a colder one, the colder one would already have its answer.)
- When today's temperature `t` is warmer than the day on top, today **is** that day's answer: pop it and record `today − that day`. Repeat while the new top is also colder.
- Then push today: it is now waiting too.
- Store **indices**, not temperatures, because the answer is a distance between days.

**Why O(n):** each index is pushed exactly once and popped at most once. A single day can pop many others, but across the whole scan there are at most `n` pops in total.

## Stuck?

Try for 15–20 minutes first, using the [6-step method](../../course/primers/00-how-to-approach-a-new-problem.md). Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [monotonic stack](../../course/primers/04-stack.md#monotonic-stack)
3. [LeetCode Editorial](https://leetcode.com/problems/daily-temperatures/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+daily+temperatures)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        answer = [0] * len(temperatures)
        stack = []                           # indices still waiting; temps decrease upward
        for i, t in enumerate(temperatures):
            while stack and temperatures[stack[-1]] < t:
                j = stack.pop()              # day i is the first warmer day after day j
                answer[j] = i - j
            stack.append(i)
        return answer                        # days left on the stack keep 0
```

Walk-through on `[73,74,75,71,69,72,76,73]` (the stack is shown as temperatures, bottom → top):

| i | temp | popped (day → answer) | stack after |
| --- | --- | --- | --- |
| 0 | 73 | — | [73] |
| 1 | 74 | day 0 → 1 | [74] |
| 2 | 75 | day 1 → 1 | [75] |
| 3 | 71 | — | [75, 71] |
| 4 | 69 | — | [75, 71, 69] |
| 5 | 72 | day 4 → 1, day 3 → 2 | [75, 72] |
| 6 | 76 | day 5 → 1, day 2 → 4 | [76] |
| 7 | 73 | — | [76, 73] |

Days 6 and 7 are never popped, so they keep `0`. Result: `[1,1,4,2,1,1,0,0]`.

</details>

**Time:** O(n) · **Space:** O(n) (the stack, e.g. on a falling sequence)

## Test cases

| Input | Expected |
| --- | --- |
| `[73,74,75,71,69,72,76,73]` | `[1,1,4,2,1,1,0,0]` |
| `[30,40,50,60]` | `[1,1,1,0]` |
| `[30,60,90]` | `[1,1,0]` |

## Pitfall

- Use a **strict** `<`. An equal temperature is not warmer, so the waiting day must stay on the stack.
- Pushing temperatures instead of indices loses the information you need to compute `i − j`.

## What changes in the next step

A stack only removes from the **top** (the newest end). A sliding window also has to drop its **oldest** element when it slides past it. Step 04 uses a **deque**, which pops from both ends: from the back like this stack, and from the front when an element leaves the window. It also keeps the **largest** value at the front, which is the window's maximum.
