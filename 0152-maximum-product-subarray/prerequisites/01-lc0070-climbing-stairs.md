# Step 01 · 70. Climbing Stairs (Easy)

[← Roadmap](00-roadmap.md) · [Next: Maximum Subarray →](02-lc0053-maximum-subarray.md)

**Practise:** [LeetCode 70](https://leetcode.com/problems/climbing-stairs/)
**Skill it adds:** your **first full DP lesson**: name a state, write a recurrence, set base cases, then fill it bottom-up and keep only what you need.

## Problem in brief

A staircase has `n` steps. Each move climbs either 1 or 2 steps. Return how many different sequences of moves reach the top exactly.

## Key idea

This problem is small, but it shows every part of dynamic programming. Learn the four questions here; every DP problem in the course asks them again.

### 1. What is the state?

Let `ways(i)` = the number of ways to stand exactly on step `i`. The answer is `ways(n)`.

### 2. What is the recurrence?

Look at the **last** move. To stand on step `i`, you either came from step `i − 1` (a 1-step move) or from step `i − 2` (a 2-step move). These two groups don't overlap and cover everything, so:

```
ways(i) = ways(i - 1) + ways(i - 2)
```

### 3. What are the base cases?

`ways(0) = 1` (standing at the bottom: one way, do nothing) and `ways(1) = 1` (one 1-step move).

### 4. In what order, and what do you keep?

Each value needs the two before it, so compute `i = 2, 3, …, n` in increasing order. Only the last two values are ever read, so two variables replace the whole table.

| i | 0 | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- | --- |
| ways(i) | 1 | 1 | 2 | 3 | 5 | **8** |

### Why not plain recursion?

Writing the recurrence directly as a recursive function is correct, but each call makes two more, and the same values are recomputed over and over: `ways(n − 2)` is computed twice, `ways(n − 3)` three times, and so on. That's **O(2ⁿ)**.

```python
def climb_stairs_naive(n):
    if n <= 1:
        return 1
    return climb_stairs_naive(n - 1) + climb_stairs_naive(n - 2)
```

Two fixes, both O(n), give the two styles of DP:

- **Top-down (memoisation):** keep the recursion, but cache each answer the first time it's computed.
- **Bottom-up (tabulation):** fill the values from the base cases upwards in a loop. No recursion, and the table can often shrink to a few variables.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [what is dynamic programming](../../course/primers/10-1d-dp.md#what-is-dynamic-programming)
3. [LeetCode Editorial](https://leetcode.com/problems/climbing-stairs/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+climbing+stairs)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

Top-down, with a cache:

```python
from functools import cache

class Solution:
    def climbStairs(self, n: int) -> int:
        @cache
        def ways(i):
            if i <= 1:
                return 1
            return ways(i - 1) + ways(i - 2)
        return ways(n)
```

Bottom-up, keeping only the last two values:

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        two_back, one_back = 1, 1          # ways(0), ways(1)
        for _ in range(2, n + 1):
            two_back, one_back = one_back, one_back + two_back
        return one_back
```

</details>

**Time:** O(n) for both · **Space:** O(n) top-down (cache + call stack), O(1) bottom-up

## Test cases

| Input | Expected |
| --- | --- |
| `n = 2` | `2` (1+1, 2) |
| `n = 3` | `3` (1+1+1, 1+2, 2+1) |
| `n = 5` | `8` |

## Pitfall

In the bottom-up loop, update both variables **at once** (`a, b = b, a + b`). Updating `one_back` first and then reading it for `two_back` uses the new value by mistake.

## What changes in the next step

Here the state was "ways to reach step `i`". Step 02 uses the same recipe on an array, with a new kind of state that the target needs: "the best subarray that **ends exactly at** `i`".
