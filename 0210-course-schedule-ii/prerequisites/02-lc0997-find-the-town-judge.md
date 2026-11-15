# Step 02 · 997. Find the Town Judge (Easy)

[← Previous: Adjacency list](01-concept-adjacency-list.md) · [Roadmap](00-roadmap.md) · [Next: Course Schedule →](03-lc0207-course-schedule.md)

**Practise:** [LeetCode 997](https://leetcode.com/problems/find-the-town-judge/)
**Skill it adds:** **in-degree and out-degree**. Count arrows into and out of each node in one pass.

## Problem in brief

People are labelled `1..n`. Each pair `[a, b]` in `trust` means "a trusts b". The judge, if one exists, trusts **nobody** and is trusted by **everybody else**. Return the judge's label, or `-1` if there isn't exactly one such person.

## Key idea

Treat each pair as an arrow `a → b`. Then the judge is the node with **out-degree 0** and **in-degree n − 1**. Two counter arrays filled in one pass over `trust` are enough. You don't even need the adjacency list.

## Stuck?

Try for 15–20 minutes first. Then, in order:
1. Re-read the **Key idea** above.
2. Primer: [graph representation](../../course/primers/08-graphs.md#graph-representation)
3. [LeetCode Editorial](https://leetcode.com/problems/find-the-town-judge/editorial/) · [NeetCode video](https://www.youtube.com/results?search_query=neetcode+find+the+town+judge)
4. Open the solution below.

## Solution

<details>
<summary><b>Show solution</b></summary>

```python
class Solution:
    def findJudge(self, n: int, trust: List[List[int]]) -> int:
        indegree = [0] * (n + 1)          # labels are 1..n
        outdegree = [0] * (n + 1)
        for a, b in trust:
            outdegree[a] += 1
            indegree[b] += 1
        for person in range(1, n + 1):
            if indegree[person] == n - 1 and outdegree[person] == 0:
                return person
        return -1
```

</details>

**Time:** O(n + E) · **Space:** O(n), where E = `len(trust)`.

## Test cases

| Input | Expected |
| --- | --- |
| `n = 2, trust = [[1,2]]` | `2` |
| `n = 3, trust = [[1,3],[2,3]]` | `3` |
| `n = 3, trust = [[1,3],[2,3],[3,1]]` | `-1` (3 trusts 1, so out-degree ≠ 0) |

## Pitfall

`n = 1` with no pairs: person 1 has in-degree 0 = n − 1 and out-degree 0, so the answer is `1`, not `-1`. The formula handles it; a special case that returns `-1` for an empty `trust` would be wrong.

## What changes in the next step

Here in-degree was only **counted**. In Course Schedule, in-degree 0 means "no prerequisites left", so those courses can be taken now. Taking one lowers its neighbours' in-degrees, which may free them up in turn. That loop is Kahn's algorithm.
