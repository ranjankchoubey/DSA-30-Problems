# Primer 02 · Two Pointers

Read this **before** the Two Pointers ladders (Q10, Q2), or when a step's key idea doesn't make sense yet.

## Two pointers basics

A "pointer" here is just an **index** into a list. Two pointers means keeping two indices, usually `l` and `r`, and moving each one forward or backward based on a simple rule. Each pointer only moves in one direction, so together they make at most `n` moves. That turns many O(n²) "check every pair" scans into one O(n) pass.

```python
nums = [4, 1, 3, 2]
nums.sort()                        # [1, 2, 3, 4], in place, O(n log n)
l, r = 0, len(nums) - 1            # first and last index
nums[l], nums[r] = nums[r], nums[l]  # swap in one line
```

| Operation | Code | Time |
| --- | --- | --- |
| Read / write by index | `nums[l]`, `nums[r] = x` | O(1) |
| Sort in place / sorted copy | `nums.sort()`, `sorted(nums)` | O(n log n) |
| Swap two items | `a[i], a[j] = a[j], a[i]` | O(1) |
| One two-pointer pass | `while l < r: ...` | O(n) |
| Check every pair (what two pointers replaces) | two nested loops | O(n²) |

The three shapes you'll meet:

| Shape | Pointers start at | Typical problems |
| --- | --- | --- |
| Opposite ends | `0` and `n − 1`, moving inward | pair with a target sum, Container With Most Water, Trapping Rain Water, palindromes |
| Same direction (read/write) | both at the start; one reads, one writes | Remove Duplicates, Move Zeroes |
| Two arrays | one pointer per array | merging two sorted arrays |

## Opposite ends

Start with the widest choice (`l = 0`, `r = n − 1`). At each step, look at the pair and **throw away one end** that can't be part of a better answer.

```python
def pair_with_sum(nums, target):
    """nums must be sorted. Returns the indices of one pair, or None."""
    l, r = 0, len(nums) - 1
    while l < r:
        s = nums[l] + nums[r]
        if s == target:
            return l, r
        if s < target:
            l += 1          # nums[l] + the biggest value left is still too small
        else:
            r -= 1          # nums[r] + the smallest value left is still too big
    return None
```

**Why throwing an end away is safe:** if `nums[l] + nums[r] < target`, then `nums[l]` plus **any** value left in the range is also too small, because `nums[r]` is the largest one. So `nums[l]` can't be in any answer. The same argument in reverse discards `nums[r]`.

The same "discard the side that can't help" rule drives:
- **Container With Most Water:** the shorter wall limits the area, so move the shorter wall.
- **Trapping Rain Water:** the side with the smaller max already knows its water level, so process that side.

## Same direction

One pointer (`read`) visits every item. The other (`write`) marks where the next kept item goes. Everything before `write` is the finished answer.

```python
def remove_duplicates_sorted(nums):
    """Keep the first copy of each value in place; return how many are kept."""
    if not nums:
        return 0
    write = 1
    for read in range(1, len(nums)):
        if nums[read] != nums[read - 1]:    # first copy of a new value
            nums[write] = nums[read]
            write += 1
    return write
```

With **two sorted arrays**, give each array its own pointer and always take the smaller head:

```python
def merge_sorted(a, b):
    i = j = 0
    out = []
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            out.append(a[i])
            i += 1
        else:
            out.append(b[j])
            j += 1
    return out + a[i:] + b[j:]
```

## Skipping duplicates

In a **sorted** array equal values sit next to each other. To use each distinct value only once, keep the **first** of each block and skip the rest.

```python
# Outer loop: use each distinct value of nums[i] once.
for i in range(len(nums)):
    if i > 0 and nums[i] == nums[i - 1]:
        continue
    ...

# Inner two pointers: after recording a match, step l past its copies.
l += 1
r -= 1
while l < r and nums[l] == nums[l - 1]:
    l += 1
```

Compare with the **previous** item (`nums[i − 1]`), not the next one. Skipping forward (`nums[i] == nums[i + 1]`) throws away the first copy and loses answers that need two equal values, like `[-1, -1, 2]`.

## Prefix and suffix maximums

Some problems need "the largest value **left** of `i`" and "the largest value **right** of `i`". Build both with one pass in each direction:

```python
def prefix_suffix_max(h):
    n = len(h)
    left = [0] * n           # left[i]  = max(h[0..i])
    right = [0] * n          # right[i] = max(h[i..n-1])
    for i in range(n):
        left[i] = max(left[i - 1], h[i]) if i else h[i]
    for i in range(n - 1, -1, -1):
        right[i] = max(right[i + 1], h[i]) if i < n - 1 else h[i]
    return left, right
```

This costs O(n) extra space. In Trapping Rain Water, two pointers from opposite ends replace both arrays with two variables, `left_max` and `right_max` (see the Q2 notes for why that's safe).

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "sorted array", "pair / triplet with sum" | sort, then opposite-end pointers |
| "in place", "O(1) extra space", "remove / keep some items" | same-direction read/write pointers |
| "unique triplets", "no duplicate answers" | sort, then skip duplicates at each level |
| "two sorted arrays / lists" | one pointer per array |
| "palindrome", "reverse in place" | opposite ends moving inward |
| "area between two walls", "water between bars" | opposite ends; move the shorter / smaller-max side |
| needs "max to the left and max to the right of i" | prefix and suffix max, or two pointers |

## Common pitfalls

- Forgetting to **sort** first. The discard argument only holds on sorted data.
- Using `l <= r` when the two pointers must be different items. Use `l < r`.
- Skipping duplicates by comparing with the **next** item, which drops valid answers.
- Forgetting `l < r` inside the duplicate-skipping `while`, which can run past the end.
- Sorting loses the original indices. If the answer needs them, sort `(value, index)` pairs or use a hash map instead.

## Learn more

- NeetCode roadmap (Two Pointers is the second node): <https://neetcode.io/roadmap>
- Python docs: [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html) · [list methods](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
- Visualise sorting: <https://visualgo.net/en/sorting>
