# Start Here

How to use this repo to learn (and teach) the 30 course problems. The course map with status is in [INDEX.md](INDEX.md).

## The route through one problem

```
INDEX.md → NNNN-slug/prerequisites/00-roadmap.md → (primer) → step 01 → … → last step → NNNN-slug/NOTES.md
        → solve on LeetCode (Submit) → git pull → mark ✅ in INDEX.md
```

1. **[INDEX.md](INDEX.md):** find the problem and click **prerequisites** in the Study column.
2. **`00-roadmap.md`:** read the goal, open the linked **primer** if the pattern is new, then follow the steps table.
3. **Step files:** each starts with `← Previous · Roadmap · Next →`. Try each step yourself; if stuck, use its **Stuck?** section. *Recap* steps (↩) link to the full lesson in an earlier ladder.
4. **The last step's Next link** goes to the problem's **`NOTES.md`**. Solve it on LeetCode, then use NOTES for approaches (spoilers), proof, dry run, pitfalls, course test cases, and follow-ups.

Every step links back to its roadmap, every last step leads to NOTES, and every NOTES has a **Start here** link back to the roadmap.

At the bottom of every NOTES, **Course navigation** links to the previous problem and to the **next problem's roadmap**, in teaching order. It also flags when a new pattern starts and which primer to read. You can go through the whole course from Q8 to Q11 without returning to the index.

## Workflow per problem

1. Open `NNNN-slug/prerequisites/00-roadmap.md` and work through the steps in order.
   Practise steps in the **NeetCode editor** (syncs to the separate `neetcode-submissions` repo) or with LeetCode **Run**, not **Submit**.
2. Solve the course problem on **LeetCode** in Python and **Submit**. LeetHub-3.0 pushes `NNNN-slug.py` + `README.md` into the folder.
   Use the spoiler in `NOTES.md` only after a real attempt.
3. `git pull` (always pull first, because LeetHub pushes from the browser).
4. Mark the problem ✅ in [INDEX.md](INDEX.md) and add anything you learned to `NOTES.md`.
5. `git add -A && git commit -m "notes: NNNN-slug" && git push`

## How to study when you're stuck

Every prerequisite step offers help in increasing levels, so you only see the answer when you need it:

1. **Key idea**: the hint in the step file.
2. **Primer**: the pattern's beginner page in [course/primers/](course/primers/), covering the data structure, Python syntax, templates and pitfalls.
3. **LeetCode Editorial** and **NeetCode video**: linked in the step's *Stuck?* section.
4. **Solution**: hidden in a spoiler block in the step file.

Then re-solve the step from scratch the next day without looking. If you can't, it isn't learned yet.

## Repo layout

```
DSA-30-Problems/
├── README.md                  # landing page + LeetHub topics (auto, don't edit the marked block)
├── START_HERE.md              # this file: how to use the repo
├── INDEX.md                   # course map: 30 problems, status, teaching order, threads
├── course/                    # course question PDF
│   └── primers/               # one beginner primer per pattern (data structure, Python syntax, templates)
├── templates/NOTES_TEMPLATE.md
└── NNNN-problem-slug/          # folder name = LeetHub's name (4-digit number + LeetCode URL slug)
    ├── NNNN-problem-slug.py   # LeetHub (auto): your latest accepted submission
    ├── README.md              # LeetHub (auto): problem statement
    ├── NOTES.md               # handwritten: pattern, approaches, proof, dry run, test cases, follow-ups
    │                          #   (optimal solution is inside a collapsed "spoiler" block)
    └── prerequisites/         # handwritten ladder from basics up to this problem
        ├── 00-roadmap.md      # start here: goal, skills, step map, threads
        ├── 01-lcNNNN-slug.md  # a LeetCode problem step
        └── 02-concept-slug.md # a technique with no single matching problem
```

Course problem folders are created **before** you solve them (with `NOTES.md` + `prerequisites/`).
When you submit on LeetCode, LeetHub adds its `.py` and `README.md` to the **same** folder; the file names differ, so nothing clashes.

## Adding a new problem

`mkdir -p NNNN-slug/prerequisites && cp templates/NOTES_TEMPLATE.md NNNN-slug/NOTES.md`
(use exactly the LeetCode URL slug with a 4-digit number, e.g. `0015-3sum`).

## Rules

- Don't edit LeetHub-owned files (`NNNN-slug.py`, the problem's `README.md`) or anything between the `LeetCode Topics` markers in `README.md`. LeetHub overwrites them.
- Put alternative or brute-force code in `NOTES.md`. Resubmitting on LeetCode replaces the synced `.py`.
- 🔒 Premium problems can't be synced without LeetCode Premium. Create `NNNN-slug/NNNN-slug.py` manually with the same naming, or practise the free alternative listed in [INDEX.md](INDEX.md).
