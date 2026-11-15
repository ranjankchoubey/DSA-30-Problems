# Primer 01 · Arrays and Hashing

Read this **before** the Arrays & Hashing ladders (Q8, Q7, Q6, Q1), or when a step's key idea doesn't make sense yet.

## Python lists (arrays)

A Python `list` is a dynamic array: items sit next to each other in memory, so jumping to an index is instant.

| Operation | Code | Time |
| --- | --- | --- |
| Read / write by index | `a[i]`, `a[i] = x` | O(1) |
| Append / pop at the end | `a.append(x)`, `a.pop()` | O(1) amortised |
| Insert / delete at the front or middle | `a.insert(0, x)`, `a.pop(0)` | O(n) |
| Search for a value | `x in a`, `a.index(x)` | O(n) |
| Sort | `a.sort()`, `sorted(a)` | O(n log n) |
| Slice copy | `a[i:j]` | O(j − i) |

## Hash set and hash map

A **hash set** (`set`) stores unique values. A **hash map** (`dict`) stores key → value pairs. Both find a key in **O(1) on average**, turning "search the whole array" (O(n)) into a single lookup.

```python
seen = set()
seen.add(5)
5 in seen                 # True, O(1)

count = {}
count[x] = count.get(x, 0) + 1          # frequency count

from collections import Counter, defaultdict
Counter("banana")                        # {'a': 3, 'n': 2, 'b': 1}
groups = defaultdict(list)
groups["key"].append(1)                  # no KeyError for missing keys
```

| Operation | set | dict |
| --- | --- | --- |
| Add / insert | `s.add(x)` O(1) | `d[k] = v` O(1) |
| Membership | `x in s` O(1) | `k in d` O(1) |
| Remove | `s.discard(x)` O(1) | `del d[k]` / `d.pop(k)` O(1) |
| Memory | O(n) | O(n) |

> "O(1) average": with very unlucky or adversarial keys, many keys share a slot and operations slow to O(n). In interviews, assume O(1) and mention the worst case if asked.

## Templates you'll reuse

### 1. Seen-before check (Contains Duplicate)

```python
seen = set()
for x in nums:
    if x in seen:
        ...                 # x is a repeat
    seen.add(x)
```

### 2. Complement lookup (Two Sum)

```python
index_of = {}
for i, x in enumerate(nums):
    if target - x in index_of:   # look up BEFORE inserting
        ...
    index_of[x] = i
```

### 3. Prefix sums

```python
prefix = [0]
for x in nums:
    prefix.append(prefix[-1] + x)
# sum(nums[i..j]) == prefix[j + 1] - prefix[i]
```

### 4. Prefix sum + hash map of counts (Subarray Sum Equals K)

```python
seen = {0: 1}               # the empty prefix
running = 0
for x in nums:
    running += x
    answer += seen.get(running - k, 0)
    seen[running] = seen.get(running, 0) + 1
```

### 5. Index marking (values in 1..n)

```python
for x in nums:
    i = abs(x) - 1
    nums[i] = -abs(nums[i])  # "value i+1 was seen"
```

## How to recognise the pattern

| You see… | Think… |
| --- | --- |
| "duplicate", "seen before", "unique" | hash set |
| "two values that add up to", "pair with difference" | complement lookup in a hash map |
| "frequency", "most common", "anagram" | `Counter` / dict of counts |
| "sum of a subarray / range", many range queries | prefix sums |
| "count subarrays with sum k", negative numbers allowed | prefix sum + hash map |
| values are exactly in `1..n` and O(1) extra space | index marking / cyclic placement |
| "everything except i", "left of i and right of i" | prefix and suffix passes |

## Common pitfalls

- Inserting into the map **before** looking up the complement, which pairs an element with itself.
- Forgetting the empty prefix (`{0: 1}` or a leading `0` in the prefix array).
- An off-by-one between inclusive and exclusive prefixes.
- Modifying the input (index marking) when the caller still needs it.
- Lists and dicts can't be set members or dict keys. Use a `tuple` or `frozenset` instead.

## Learn more

- NeetCode roadmap (Arrays & Hashing is the first node): <https://neetcode.io/roadmap>
- Python docs: [dict](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict) · [set](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) · [collections (Counter, defaultdict)](https://docs.python.org/3/library/collections.html)
- Visualise hashing: <https://visualgo.net/en/hashtable>
