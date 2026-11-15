# Step 03 · Concept: circular array, iterate 2n

[← Previous: Next Greater Element I](02-lc0496-next-greater-element-i.md) · [Roadmap](00-roadmap.md) · [Target: Next Greater Element II →](../NOTES.md)

> 📚 **New to this?** Primer: [monotonic stack](../../course/primers/04-stack.md#monotonic-stack)

**Skill it adds:** indexing a circle with `i % n`, and turning a circular problem into a linear one by reading the array **twice**.

## Idea

In a circle of `n` items, the items after index `i` are `i+1, …, n−1, 0, …, i−1`. In one formula, that's `(i + k) % n` for `k = 1 … n−1`.

1. **Brute force:** for each `i`, walk `k = 1 … n−1` and stop at the first bigger value. O(n²).
2. **Unroll the circle:** in `nums + nums`, the `n − 1` items after position `i` are exactly the clockwise items after `i`. So run the step 02 stack pass (by index, because values can repeat) on the doubled array and keep the first `n` answers. O(n).

## Code

```python
def next_greater_circular_brute(nums):
    n = len(nums)
    answer = [-1] * n
    for i in range(n):
        for k in range(1, n):
            j = (i + k) % n
            if nums[j] > nums[i]:
                answer[i] = nums[j]
                break
    return answer


def next_greater_circular_doubled(nums):
    n = len(nums)
    doubled = nums + nums
    answer = [-1] * (2 * n)
    stack = []                            # indices into doubled; values decreasing
    for i, x in enumerate(doubled):
        while stack and doubled[stack[-1]] < x:
            answer[stack.pop()] = x
        stack.append(i)
    return answer[:n]
```

**Brute force:** O(n²) time · O(1) extra space
**Doubled array:** O(n) time · O(n) extra space for the copy

## Walk-through: `nums = [5, 4, 3]`, doubled `[5, 4, 3, 5, 4, 3]`

| i | x | popped (answer set) | stack after (indices) |
| --- | --- | --- | --- |
| 0 | 5 | — | [0] |
| 1 | 4 | — | [0, 1] |
| 2 | 3 | — | [0, 1, 2] |
| 3 | 5 | 2 → 5, then 1 → 5 | [0, 3] |
| 4 | 4 | — | [0, 3, 4] |
| 5 | 3 | — | [0, 3, 4, 5] |

First `n` answers: `[-1, 5, 5]`. Indices 1 and 2 only found their answer because the array was **repeated**.

## Test cases

| Input | Expected |
| --- | --- |
| `[1, 2, 1]` | `[2, -1, 2]` |
| `[1, 2, 3, 4, 3]` | `[2, 3, 4, -1, 4]` |
| `[5, 4, 3]` | `[-1, 5, 5]` |

## What changes in the target

- Don't build `doubled`. Loop `i` over `range(2 * n)` and read `nums[i % n]`.
- Only the first `n` answers matter, so **push indices only while `i < n`**. The second lap only pops.

That keeps O(n) time and drops the copy → [NOTES](../NOTES.md).
