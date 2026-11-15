# Primer 00 · How to approach a problem you've never seen

Read this **before your first problem**, and come back whenever you're stuck at the very beginning, with no idea where to start.

## First, the right mindset

**Patterns are knowledge you learn, not something you're expected to invent.** Nobody works out "use a hash set" without having learned that hash sets exist.

- The **first** problem of a new pattern is for **learning**: read the primer, try for a while, then study the solution properly.
- The **later** problems of the same pattern are for **practice**: now you should be able to recognise and apply it.

Reading a solution isn't failing. Reading it and never re-solving it is.

## The 6 steps

Example used throughout: [Contains Duplicate](../../0645-set-mismatch/prerequisites/01-lc0217-contains-duplicate.md): *"is any number repeated?"*

### 1. Understand it: restate and make tiny examples

Say the problem in one sentence of your own. Write 3–4 tiny inputs **including edge cases**, with the answer for each.

> `[3,1,4,1]` → True · `[1,2,3]` → False · `[]` → False · `[7]` → False

### 2. Solve one example by hand and watch what your brain does

On `[3,1,4,1]` you probably thought: *"3, is it anywhere else? No. 1, is it anywhere else? Yes!"* That thought **is** an algorithm.

### 3. Write the brute force, even if it's slow

```python
def contains_duplicate_brute(nums):
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] == nums[j]:
                return True
    return False                     # O(n²) time, O(1) space
```

A correct brute force is always progress. It gives you something to test against, and in an interview, saying it out loud earns credit.

### 4. Find the repeated work by asking what the slow part keeps asking

The inner loop keeps asking **"have I seen this number before?"** and answers by rescanning everything, which costs O(n) **each time**.

### 5. Find the tool that answers that question fast

Look up the question in the [toolbox](#the-toolbox) below.
"Have I seen x?" → **hash set**, an O(1) lookup → one pass, O(n):

```python
def contains_duplicate(nums):
    seen = set()
    for x in nums:
        if x in seen:
            return True
        seen.add(x)
    return False                     # O(n) time, O(n) space
```

(Another tool also works: **sort** so duplicates sit next to each other, which takes O(n log n) time.)

### 6. Test it, then state the complexity

Run your step 1 examples through the code **by hand**, edge cases first. Then state the time and space complexity, and the trade-off (here, O(n) extra memory buys O(n) time).

## The toolbox

| If the slow part keeps asking… | Use… | Learn it |
| --- | --- | --- |
| "Have I seen x?" / "How many times x?" | set / dict | [Primer 01](01-arrays-and-hashing.md#hash-set-and-hash-map) |
| "Is there a pair adding up to t?" | dict complement, or sort + two pointers | [Primer 01](01-arrays-and-hashing.md#2-complement-lookup-two-sum) · [Primer 02](02-two-pointers.md#opposite-ends) |
| "What's the sum of this range / subarray?" | prefix sums | [Primer 01](01-arrays-and-hashing.md#3-prefix-sums) |
| "What's the longest / shortest contiguous stretch that satisfies a rule?" | sliding window | [Primer 03](03-sliding-window.md#variable-size-window) |
| "What's the most recent unmatched item?" / "What's the next greater or smaller element?" | stack / monotonic stack | [Primer 04](04-stack.md#stack-basics) · [Primer 04](04-stack.md#monotonic-stack) |
| "Where does the answer flip from no to yes?" (sorted or monotonic) | binary search | [Primer 05](05-binary-search.md#binary-search-on-a-condition) |
| "How do I relink nodes, or insert or delete in O(1)?" | linked list (dummy node) | [Primer 06](06-linked-list.md#dummy-node) |
| "What's the current smallest or largest?" / "What are the top k?" | heap | [Primer 11](11-heap-priority-queue.md#heap-basics) |
| "Does a node's answer depend on its children's answers?" | recursion on trees | [Primer 07](07-trees.md#recursion-on-trees) |
| "What's the fewest number of steps?" (unweighted) | BFS | [Primer 08](08-graphs.md#bfs) |
| "In what order, given dependencies?" | topological sort | [Primer 08](08-graphs.md#topological-sort) |
| "Are these connected? Merge these groups." | union-find | [Primer 08](08-graphs.md#union-find) |
| "What's the cheapest path?" (weighted) | Dijkstra | [Primer 09](09-advanced-graphs.md#dijkstra) |
| "Does the best answer build on answers to smaller inputs?" | dynamic programming | [Primer 10](10-1d-dp.md#state-and-recurrence) |

## Let the constraints tell you the target

Count the operations: about 10⁸ simple operations take roughly 1 second.

| Input size n | Usually needs |
| --- | --- |
| n ≤ 20 | O(2ⁿ): trying every subset is fine |
| n ≤ 1,000 | O(n²): nested loops are fine |
| n ≤ 100,000 to 1,000,000 | **O(n log n) or O(n)**: sort, hash, two pointers, heap |
| n ≥ 10⁹ (a value, not a list length) | O(log n): binary search or math |

If your brute force is O(n²) and n = 200,000, you already know you need a faster tool before writing any code.

## When to look at the answer

| Time spent | If… | Do this |
| --- | --- | --- |
| 10 min | You can't even write a brute force | Go back to steps 1–2: make smaller examples and solve one by hand |
| 15–20 min | You have a brute force but no speed-up | Use the toolbox above and the step's **primer** link |
| 20–30 min | Still stuck | Use the step's **Stuck?** links (editorial or video), then the **solution** spoiler |
| After reading it | — | Close it, write it **from memory**, then re-solve it in 3 days without looking |
