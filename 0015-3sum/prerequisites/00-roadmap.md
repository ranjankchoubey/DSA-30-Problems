# Roadmap → Q10 · 15. 3Sum (Medium)

**Goal:** return every **unique** triplet `[a, b, c]` from the array with `a + b + c = 0`. No duplicate triplets in the output. Required: **O(n²)** using sorting and two pointers, with duplicates skipped at each level.

> 📚 **Before you start:** read [Primer 02 · Two Pointers](../../course/primers/02-two-pointers.md).

## Skills you need

- [ ] The complement idea: fixing one value turns the problem into "find two values that add up to `−x`"
- [ ] Finding a pair with a given sum in a **sorted** array with two pointers in O(n)
- [ ] Skipping duplicates in a sorted array by comparing with the previous item
- [ ] Collecting **all** unique pairs, not just one

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Two Sum](01-lc0001-two-sum.md) ↩ recap | Complement idea | [LC 1](https://leetcode.com/problems/two-sum/) |
| 02 | [Two Sum II – Input Array Is Sorted](02-lc0167-two-sum-ii-input-array-is-sorted.md) | Two pointers on a sorted array | [LC 167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) |
| 03 | [Remove Duplicates from Sorted Array](03-lc0026-remove-duplicates-from-sorted-array.md) | Skipping duplicates in a sorted array | [LC 26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) |
| 04 | [All unique pairs (Two Sum II)](04-concept-all-unique-pairs-two-sum-ii.md) | Every unique pair, with inner-level dedupe | — |
| ★ | [**3Sum** → NOTES](../NOTES.md) | Sort, fix `nums[i]`, run step 04 on the rest; dedupe at both levels: O(n²) | [LC 15](https://leetcode.com/problems/3sum/) |

## How the steps add up

- Step 01: a triplet `a + b + c = 0` is "`b + c = −a`", which is Two Sum with target `−a`.
- Step 02: on a **sorted** array, Two Sum needs no hash map. Two pointers find a pair in O(n) and O(1) space.
- Step 03: in a sorted array, duplicates sit next to each other, so "is this a repeat?" is just `nums[i] == nums[i − 1]`.
- Step 04: steps 02 and 03 together list every unique pair that hits a target.
- **Target:** sort once, loop over each distinct `nums[i]`, and run step 04 on the part to its right with target `−nums[i]`. That's n passes of O(n), so O(n²).

## Threads

- **Q1** Subarray Sum Equals K: the same Two Sum complement idea, applied to prefix sums.
- **Q2** Trapping Rain Water: opposite-end pointers again, moving the side that can't do better.

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
