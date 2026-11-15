# Roadmap → Q8 · 645. Set Mismatch (Easy)

**Goal:** `nums` should contain every number 1..n exactly once, but one number is duplicated and one is missing. Return `[duplicate, missing]` in **O(n) time and O(1) extra space**.

> 🧠 **First problem of the course?** Read [Primer 00 · How to approach a problem you've never seen](../../course/primers/00-how-to-approach-a-new-problem.md) first.
>
> 📚 **Before you start:** read [Primer 01 · Arrays and Hashing](../../course/primers/01-arrays-and-hashing.md).

## Skills you need

- [ ] Detect a repeated value
- [ ] Find the missing value in 1..n
- [ ] Do both in O(1) extra space by using the array itself to mark what you've seen

## Steps

| # | Step | Skill it adds | Practise |
| --- | --- | --- | --- |
| 01 | [Contains Duplicate](01-lc0217-contains-duplicate.md) | A hash set detects a repeat | [LC 217](https://leetcode.com/problems/contains-duplicate/) |
| 02 | [Missing Number](02-lc0268-missing-number.md) | Sum formula / XOR find the missing value | [LC 268](https://leetcode.com/problems/missing-number/) |
| 03 | [Find All Numbers Disappeared in an Array](03-lc0448-find-all-numbers-disappeared-in-an-array.md) | Index marking for O(1) extra space | [LC 448](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/) |
| ★ | [**Set Mismatch** → NOTES](../NOTES.md) | Find the duplicate and the missing number together | [LC 645](https://leetcode.com/problems/set-mismatch/) |

## How the steps add up

- Step 01 finds the duplicate but needs a set (O(n) space).
- Step 02 finds the missing number with arithmetic (O(1) space).
- Step 03 replaces the set with sign flips inside the array.
- **Set Mismatch** asks step 01's question and step 02's question, and answers both with step 03's trick.

## Threads

- Hash set lookups come back in **Q6** Longest Consecutive Sequence.
- The same index-marking trick solves LC 442 (Find All Duplicates) and LC 41 (First Missing Positive).

> Practise each step in the NeetCode editor, or on LeetCode using **Run** (not **Submit**), so only course problems sync to this repo.
