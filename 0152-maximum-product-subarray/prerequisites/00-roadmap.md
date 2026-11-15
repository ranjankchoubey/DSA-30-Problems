# Roadmap → Q11 · 152. Maximum Product Subarray (Medium)

**Goal:** return the largest product of any contiguous, non-empty subarray. Values can be negative or zero. O(n) time, O(1) space.

> 📚 **Before you start:** read [Primer 10 · 1-D DP](../../course/primers/10-1d-dp.md).

## Skills you need

- [ ] Define a DP state and recurrence, then fill it bottom-up with O(1) memory
- [ ] Kadane's idea: keep the best value of a subarray that **ends at** `i`
- [ ] Track a positive and a negative quantity together, because a negative number swaps them
- [ ] Treat zero as a reset

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Climbing Stairs](01-lc0070-climbing-stairs.md) | DP state and recurrence (first full DP lesson) | [LC 70](https://leetcode.com/problems/climbing-stairs/) |
| 02 | [Maximum Subarray](02-lc0053-maximum-subarray.md) | Kadane: best value ending at `i` | [LC 53](https://leetcode.com/problems/maximum-subarray/) |
| 03 | [Maximum Length of Subarray With Positive Product](03-lc1567-maximum-length-of-subarray-with-positive-product.md) | Track positive and negative together; zero resets | [LC 1567](https://leetcode.com/problems/maximum-length-of-subarray-with-positive-product/) |
| ★ | [**Maximum Product Subarray** → NOTES](../NOTES.md) | Track the max **and** min product ending at `i` | [LC 152](https://leetcode.com/problems/maximum-product-subarray/) |

## How the steps add up

- Step 01: DP = name the state, write the recurrence, start from the base cases, keep only what the next step needs.
- Step 02: the state is "best sum of a subarray **ending at** `i`". Either extend the previous one or start fresh.
- Step 03: with products, a negative number turns the best into the worst and back again, so keep **both** sides. A zero resets them.
- **Target:** step 02's recurrence with step 03's pair of states: `max` and `min` product ending at `i`.

## Threads

- **Q7** Product of Array Except Self: products, zeros, and left/right passes (also an alternative solution here).
- **Q12** Longest Palindromic Substring: the other 1-D DP problem.
- **Q5** Sliding Window Maximum: another "best over contiguous ranges" problem.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
