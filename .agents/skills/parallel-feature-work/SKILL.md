---
name: parallel-feature-work
description: >
  Use when two or more feature specs may proceed at once, when adding a
  second feature spec file or README catalog row on a feat/ already
  implementing another slug, when dispatching separate implementer
  agents, or when deciding merge order across open feat/ PRs.
---

# Parallel feature work

One validated spec per agent, per branch, per worktree. The parent coordinates. Children implement.

## When to use

- "Run these in separate agents"
- Two specs do not share a required merge-before relationship
- You are about to `releasing-a-spec` more than one slug this session
- Adding `docs/specs/features/<other>.md` or a second README catalog row on a `feat/` that already implements another slug

Do **not** parallelize when one spec's acceptance criteria name the other's UI or seam.

## Loop

1. **Specs first, serially.** The parent writes or completes every feature spec (`releasing-a-spec` completeness) and catalogs the rows in `docs/specs/README.md`. Children do not invent assumptions. Completion: each spec is `validated`; no open TBD that affects behavior.

2. **Independence.** Two specs may run together only if they can land in either order without the other SHA. Shared files need an **owner** in the dispatch (one agent writes that path; the other does not). Completion: a written owner table, or you run them sequentially.

3. **Waves.** Later slices that compose earlier UI wait until those PRs merge. Completion: each wave's merge-before list is explicit.

4. **Dispatch.** For each independent spec: `using-git-worktrees`, then a fresh-context agent seeded with `releasing-a-spec` plus the spec path, task statement, file owners, and "do not edit another agent's paths." Each child opens its own `feat/<slug>` PR and runs `spec-review-loop`. Completion: one PR number per spec.

5. **Catalog and shared specs.** Children update only their `docs/specs/features/<slug>.md`. The parent (or the first merge) updates `docs/specs/README.md` status flips after each merge. Two children must not both rewrite the same system spec hunk — put the shared rule in before dispatch. Completion: `git diff` on each PR does not fight over the same catalog lines.

6. **Integrate.** Merge in the wave's recorded order. After each merge, remaining worktrees rebase onto default. Then the next wave. Completion: every slug is `released` or still queued.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "Four agents, four checkouts of main in one dir" | That is one dirty tree. Worktrees. |
| "They can both bump the catalog" | They will conflict. Parent owns the index. |
| "The later flow can start now and fill in the seam later" | The flow criterion names that seam. Wait or split the spec. |
| "I'll draft the sibling spec on this feat/" | One PR number per spec. Open `feat/<other>`. |
