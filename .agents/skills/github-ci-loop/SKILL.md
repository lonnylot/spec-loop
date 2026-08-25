---
name: github-ci-loop
description: >
  Use when starting feature work, creating a branch, opening or updating a
  GitHub pull request, waiting on CI, checks never started, or when
  local tests are green and the change must run on GitHub.
---

# GitHub CI loop

Feature work lives on a branch. A pull request is how CI validates it. Local green is not released.

## When to branch

Before the first file edit for a spec or bug, from the current default branch:

`feat/<spec-slug>` or `fix/<spec-slug>`

One validated spec (or one bug) per branch. Do not implement on `main` / `master`. Two feat/ branches at once: `using-git-worktrees` so each agent has its own checkout (`parallel-feature-work`).

## When to open the PR

After the **first** logical commit that is locally green. Open the PR then so CI runs on every later push. Draft is fine until you start `spec-review-loop`.

PR body names:

- Spec path and status
- Task statement
- How to run tests (from `package.json` or the product's documented scripts, not memory)

## CI

1. `git push -u origin HEAD`
2. Create or update the PR with `gh` if present; otherwise the harness GitHub integration.
3. Watch checks (`gh pr checks` or the PR status). Completion: every required check is green on **this** HEAD, or you have a failing log.
4. If the PR has no check runs and `.github/workflows/` exists, read `on:` and permissions before an empty retrigger commit. Fix the workflow so checks start. Empty commit + close/reopen is last resort and is not a logical commit for a criterion. Completion: `gh pr checks` lists runs. If workflows are missing, the next paragraph applies.
5. Red CI: treat the failure as a failing test. Reproduce, `tdd-loop`, `logical-commits`, push. Re-watch the whole suite.

If `.github/workflows/` is missing, add a workflow that runs the product's typecheck, test, and build scripts on `pull_request` and on pushes to the default branch **before** you claim CI is a gate. Do not invent script names — read `package.json` (or the equivalent).

## Merge

Merge only from `spec-review-loop` after Approve + green CI + `releasing-a-spec` gate. Use the repository's default merge method.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "Local is green, skip the PR" | CI is the shared gate. No PR, no release. |
| "I'll branch when it's done" | Work on the default branch has no CI loop. |
| "CI is flaky, merge anyway" | Quarantine with a failing test or fix the workflow. |
| "No checks; empty commit to retrigger" | Read `on:` and permissions first. Empty commit is last resort, not a criterion. |

## Tooling

`git` for branches and commits. GitHub via `gh` or the harness GitHub API. No vendor-specific agent tool is required.
