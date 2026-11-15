# 0025. Reverse Nodes in k-Group

| Course Q# | LeetCode | Difficulty | NeetCode Pattern | Status |
| --- | --- | --- | --- | --- |
| Q18 | [25](https://leetcode.com/problems/reverse-nodes-in-k-group/) | Hard | Linked List | ⬜ |

> **Start here:** [prerequisites/00-roadmap.md](./prerequisites/00-roadmap.md)
> **Code:** `0025-reverse-nodes-in-k-group.py` (auto-synced by LeetHub after you submit) · **Problem statement:** `README.md` (auto-synced by LeetHub)

## 1. Case scenario

**Batch Reversal in Playlist:** a playlist is a singly linked list. Reverse the songs in batches of `k`. A final batch with fewer than `k` songs stays as it is. Only relink nodes (don't change values), with O(1) extra space. Constraints: n ≤ 5000, 1 ≤ k ≤ n.

## 2. Pattern recognition

- **Pattern:** Linked List, in-place reversal of sub-lists
- **Signals in the statement:** "reverse", "groups of k", "only relink nodes", "O(1) extra space". Together these point to the `prev / curr / nxt` reversal run on one group at a time, with a dummy node in front.
- **Core idea in one line:** for each group, check that `k` nodes exist, reverse exactly those `k`, then hook the node before the group to the new front and the old front to the next group.

## 3. Approaches

### Approach 1: Copy values into an array

Read the values, reverse each full chunk of `k`, write them back. Simple, but it **changes values** and uses O(n) space, so the doc rules it out. **Time:** O(n) · **Space:** O(n)

### Approach 2: Recursive

If fewer than `k` nodes remain, return `head` unchanged. Otherwise solve the rest of the list (after the first `k` nodes) recursively, then reverse the first `k` nodes onto that result. **Time:** O(n) · **Space:** O(n/k) call stack

<details>
<summary><b>Show the recursive solution</b></summary>

```python
class Solution:
    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        node = head
        for _ in range(k):                     # are there k nodes?
            if node is None:
                return head                    # fewer than k: leave as is
            node = node.next
        prev = self.reverseKGroup(node, k)     # node = start of the next group
        curr = head
        for _ in range(k):                     # reverse this group onto the solved rest
            nxt = curr.next
            curr.next = prev
            prev, curr = curr, nxt
        return prev
```

</details>

### Approach 3: Iterative with a dummy node (optimal)

`group_prev` is the node before the current group (starts at `dummy`). Find the group's `k`-th node; stop if the list runs out. Reverse the group with `prev` starting at `group_next`, so the reversed group already links forward. Then reconnect and move `group_prev` to the group's old first node. **Time:** O(n) · **Space:** O(1)

<details>
<summary><b>Show optimal solution</b> (try it on LeetCode first)</summary>

```python
class Solution:
    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        dummy = ListNode(0, head)
        group_prev = dummy                     # node just before the current group
        while True:
            kth = group_prev
            for _ in range(k):                 # find the group's k-th node
                kth = kth.next
                if kth is None:
                    return dummy.next          # fewer than k left: leave them
            group_next = kth.next
            prev, curr = group_next, group_prev.next
            while curr is not group_next:      # reverse the group (step 01)
                nxt = curr.next
                curr.next = prev
                prev, curr = curr, nxt
            first = group_prev.next            # old first node, now the group's last
            group_prev.next = kth              # kth is the group's new first node
            group_prev = first
```

</details>

## 4. Why it works

- **Invariant:** at the top of the loop, everything from `dummy` to `group_prev` is finished, and `group_prev.next` is the first node of the next unprocessed group.
- The `k`-step walk checks the group is complete **before** changing anything, so a short last group is never touched.
- Starting the reversal with `prev = group_next` makes the group's old first node point to `group_next` automatically, which connects the back end of the group.
- `group_prev.next = kth` connects the front end. The old first node is now the group's last, so it becomes the next `group_prev`, and the invariant holds again.
- Each node is visited twice (once by the `k`-step walk, once by the reversal): O(n) time and a fixed number of pointers, O(1) space.

## 5. Dry run: TC 2, `head = [1,2,3,4,5], k = 3` → `[3,2,1,4,5]`

| Round | `group_prev` | `kth` found? | `group_next` | After reconnecting | Next `group_prev` |
| --- | --- | --- | --- | --- | --- |
| 1 | dummy | 3 (walked 1, 2, 3) | 4 | dummy → 3 → 2 → 1 → 4 → 5 | 1 |
| 2 | 1 | no: 4, 5, then `None` | | return `dummy.next` = **[3,2,1,4,5]** | |

## 6. Edge cases & pitfalls

- **Short last group** (TC 1's `5`, TC 2's `4,5`): check for `k` nodes **before** reversing, or you'd have to undo the reversal.
- **`k = 1`** (TC 3): every group is one node, so the list is unchanged.
- **`k = n`**: the whole list is reversed once.
- Start the reversal with `prev = group_next`, not `None`, or the group gets cut off from the rest of the list.
- Save `group_prev.next` (the old first node) **before** setting `group_prev.next = kth`.

## 7. Test cases (from course doc)

| Test | Input | Expected |
| --- | --- | --- |
| TC 1 | `head = [1,2,3,4,5], k = 2` | `[2,1,4,3,5]` |
| TC 2 | `head = [1,2,3,4,5], k = 3` | `[3,2,1,4,5]` |
| TC 3 | `head = [1], k = 1` | `[1]` |

## 8. Follow-up questions (from course doc)

- **Q: Explain the iterative approach.**
  **A:** A dummy node sits before the head. For each group: walk `k` steps from `group_prev` to find the `k`-th node (if the list ends first, stop). Remember `group_next = kth.next`. Reverse the group with the usual three-pointer loop, starting `prev` at `group_next`. Then set `group_prev.next = kth` and move `group_prev` to the group's old first node. Only a few pointers exist at any time, so the extra space is O(1). See §3–§4.
- **Q: Explain the recursive approach and its stack cost.**
  **A:** Check that `k` nodes exist. If not, return `head` as it is. Otherwise recursively solve the list that starts after the first `k` nodes, then reverse the first `k` nodes onto the returned head. The recursion makes one call per full group plus one for the remainder: **⌊n/k⌋ + 1 stack frames**, so **O(n/k) extra space**. That breaks the O(1)-space requirement. Worst case is `k = 1`: with n = 5000 that is 5001 frames, more than Python's default recursion limit of 1000, so it raises `RecursionError`. The iterative version has no such limit. Time is O(n) for both.

## 9. Threads

- **Q13** LRU Cache: the full reversal lesson, pointer relinking and dummy nodes.
- **Q19** Merge k Sorted Lists: a dummy node plus a `tail` pointer while building the answer.

## 10. One-line revision

> Dummy before head; per group, walk k steps (stop if short), reverse with prev = group_next, then group_prev.next = kth and group_prev = old first node.
