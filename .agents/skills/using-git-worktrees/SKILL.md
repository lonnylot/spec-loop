---
name: using-git-worktrees
description: >
  Use when dispatching spec-implementer, when starting feature work that
  must not share a working tree, when dispatching parallel feature
  agents, or when another skill needs an isolated git worktree.
---

# Using git worktrees

Two agents must not share one checkout. Every `spec-implementer` gets its own worktree and `feat/<slug>` branch. The orchestrator stays on the default branch.

## When to use

- Dispatching `spec-implementer` (`releasing-a-spec`) — including a single spec
- Two or more `feat/` branches will be edited at once (`parallel-feature-work`)
- A child agent would otherwise dirty the parent's tree
- The harness offers a worktree isolation mode — use that first

## Loop

1. **Detect.** `git rev-parse --git-dir` vs `--git-common-dir`. If they differ and you are not in a submodule (`git rev-parse --show-superproject-working-tree` empty), you are already in a worktree — stay there. Completion: you know whether this directory is already isolated.

2. **Place.** Prefer an existing ignored `.worktrees/` at the repo root. If it is missing, create it and add `.worktrees/` to `.gitignore` in the same change if it is not already ignored (`git check-ignore -q .worktrees`). Completion: `git check-ignore -q .worktrees` is true.

3. **Add.** From the main checkout, on current default branch:

   `git worktree add .worktrees/<slug> -b feat/<slug>`

   Then `cd` there and run whatever the product defines for install. Completion: the worktree is on `feat/<slug>` and is not the parent's cwd.

4. **Baseline.** Run the product's `test` script. If that script starts a TCP server, bind a checkout-unique port (`playwright-e2e`). Completion: exit 0, or you stop and report a dirty baseline.

Do not `git worktree add` when the harness already created isolation (spawn `isolation: worktree`, or equivalent). Do not commit `.worktrees/` contents.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "We'll take turns in one tree" | The other agent will overwrite your index. Isolate. |
| "The directory is surely ignored" | Run `git check-ignore`. |
| "Skip baseline, it's a fresh clone" | A red baseline makes every later failure ambiguous. |
