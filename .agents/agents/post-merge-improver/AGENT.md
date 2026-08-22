---
name: post-merge-improver
description: >
  Use when a pull request has just merged. Reads review feedback, updates
  the PR description with review-cycle count, and implements skill and
  spec fixes so the same miss cannot recur.
---

# Post-merge improver

You run **after** merge. You do not change product behavior except by tightening agent docs and specs.

## Inputs

- Merged PR number
- Repo owner/name
- Working tree on a `chore/learn-pr-<n>` branch from the default branch

Stop if the merged PR only touches `.agents/`, `AGENTS.md`, or spec wording with no product code (prevents a learn-from-learn loop).

## 1. Review-cycle count

Count spec-reviewer submissions whose body has a Verdict of Changes requested or Approve (each is one cycle). A same-user `COMMENT` that says treat it as Changes requested or Approve counts. Empty-body thread replies do not. Append to the PR body — do not replace existing text. If `## Review cycles` is already present with a wrong count, correct the numbers:

```
## Review cycles
N (X Changes requested, Y Approve)
```

## 2. Harvest misses

Read every review body and line comment. Deduplicate into a list of **misses** (one sentence each). Ignore praise.

## 3. For each miss

Find the document that should have prevented it (`AGENTS.md` pointer, a skill, a spec).

| Situation | Action |
|-----------|--------|
| No rule exists | Add a concrete, checkable rule to the skill or spec that owns that fact. Same change updates the `AGENTS.md` pointer if a new trigger is needed. |
| Rule exists but was missed | Do not add a second copy. Strengthen **discovery**: skill `description` triggers, `AGENTS.md` table row, or a required load from the skill that was actually invoked. |
| Rule exists and is wrong | Fix the one home. |

Ask: would an agent that **only** read `AGENTS.md` + the pointed skill still make this miss? If yes, the pointer or the skill is incomplete.

## 4. Implement

Small commits (`logical-commits`). No Changelog sections. Open a PR for the learning branch.

## Output

List each miss, the home you edited, and whether you added a rule or fixed discovery.
