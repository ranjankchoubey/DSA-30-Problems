# Roadmap → Q6 · 128. Longest Consecutive Sequence (Medium)

**Goal:** given an **unsorted** array, return the length of the longest run of consecutive integers (e.g. 1,2,3,4) in **O(n)**. Sorting is not allowed.

> 📚 **Before you start:** read [Primer 01 · Arrays and Hashing](../../course/primers/01-arrays-and-hashing.md).

## Skills you need

- [ ] O(1) "is x present?" checks with a hash set
- [ ] Counting the longest run in one scan
- [ ] Knowing the sorted O(n log n) baseline and how it handles duplicates
- [ ] Seeing why a naive hash-set walk can still be O(n²)

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Contains Duplicate](01-lc0217-contains-duplicate.md) ↩ recap | Hash set membership | [LC 217](https://leetcode.com/problems/contains-duplicate/) |
| 02 | [Max Consecutive Ones](02-lc0485-max-consecutive-ones.md) | Longest run in one scan | [LC 485](https://leetcode.com/problems/max-consecutive-ones/) |
| 03 | [Sort, then count runs](03-concept-sort-then-count-runs.md) | O(n log n) baseline; handling duplicates | — |
| 04 | [Naive hash-set walk](04-concept-naive-hash-set-walk.md) | No sorting, but O(n²) in the worst case, and why | — |
| ★ | [**Longest Consecutive Sequence** → NOTES](../NOTES.md) | Start walking only at the beginning of a run: O(n) | [LC 128](https://leetcode.com/problems/longest-consecutive-sequence/) |

## How the steps add up

- Step 02 counts a run while a condition holds.
- Step 03 sorts so that "consecutive" means "adjacent".
- Step 04 drops sorting and uses step 01's set to ask "is x+1 there?", but walks the same run many times.
- **Target:** walk only from values whose `x−1` is missing, so each element is visited once.

## Threads

- **Q8** Set Mismatch: hash set membership.
- **Q1** Subarray Sum Equals K: hash structures turn an O(n²) scan into O(n).

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
