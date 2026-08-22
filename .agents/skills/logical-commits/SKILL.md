---
name: logical-commits
description: >
  Use when a spec criterion goes green, when finishing a slice, before
  opening or updating a pull request, when deciding whether to commit,
  when grouping by screen, when independently revertible ACs share a
  commit, when a spec-only commit lists every AC, or when a dedicated
  test or Playwright flow would land after its production.
---

# Logical commits

One commit is one finished thought: one spec criterion (or one review/CI fix) with its spec, tests, and code together. Independently revertible production hunks are separate commits even on one screen. A spec-only commit that lists every AC is not a slice.

## When to commit

- A `tdd-loop` criterion is GREEN and the spec matches the code
- A single review thread or a single CI cause is fixed and green
- Spec status flip (`validated` / `released`) after the gate that allows it
- One production change several named screens mount, plus the shell it left — splitting those routes is empty / tests-after

## When not to commit

- Tests are red (except a human-requested WIP, which is not a release commit)
- Two unrelated criteria in one tree
- One screen commit that lands multiple independent ACs
- Grouping by screen
- A spec-only commit that lists every AC
- Secrets, `.env`, or credentials
- On the default branch for feature work — branch first (`github-ci-loop`)

## How

1. `git status` and `git diff`. Stage only this slice.
2. Message names the criterion or review thread: `feat(orders): total stays within ±5%`.
3. Commit spec + tests + production code together. Completion: `git show --stat` lists all three when all three changed. A later test-only commit for a criterion whose production already landed is tests-after — not a logical slice. A Playwright flow (or any dedicated test) in a later commit than the production that implemented that criterion is the same miss, even after the PR is open.

Prefer conventional prefixes (`feat`, `fix`, `docs`, `test`, `refactor`, `chore`). The subject (and body if needed) states what changed. Specs do not keep a Changelog section.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "I'll commit once at the end" | One blob cannot be reviewed or reverted per criterion. |
| "Checkpoint before I forget" | Unfinished slices are not logical. Finish GREEN, then commit. |
| "Spec in a follow-up commit" | Same-commit spec update is the iron law. |
| "One screen is one commit" | Grouping by screen is the miss. Split independently revertible ACs. |
| "Spec first, then one feat" | Spec hunk lands with the first production that implements it. |

## Red flags

Empty or "WIP" messages on a PR. Default-branch commits for a feature. Code committed without the spec hunk that describes it. Dedicated tests in a follow-up commit after the production that needed them. One commit that ships several independent ACs together. A commit subject that names two new ACs with "and". Spec-only then one feat. Grouped by screen.
