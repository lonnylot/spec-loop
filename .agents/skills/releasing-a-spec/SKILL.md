---
name: releasing-a-spec
description: >
  Use when handed a spec to implement, asked to iterate on a spec, validate
  a spec, ship a feature, release without errors, dispatch spec-implementer,
  or when about to commit a validated spec before the first criterion.
  The orchestrator loads this skill and does not write production code.
---

# Releasing a spec

Take a spec from ingest to `released` with a clean gate. The **orchestrator** loads this skill: validate with the user, dispatch `spec-implementer`, run `spec-review-loop`, dispatch `release-manager`. Errors return you to the implementer at RED, not to "ship anyway."

This session does not write production code. Completeness and catalog rows are spec work; they are not an implementation.

## When to use

- User pastes or points at a spec
- "Implement this" / "ship it" / "iterate until it's right"
- Feature status is `draft` or `validated`

Two or more specs in one session: load `parallel-feature-work` before this loop.

## Loop

```
ingest → completeness → iterate until validated
  → worktree + feat/<slug> (using-git-worktrees)
  → dispatch spec-implementer in that worktree
      (resume that conversation for this spec)
  → spec-review-loop until Approve + CI green
      (fresh spec-reviewer every dispatch;
       Changes requested → resume spec-implementer)
  → dispatch release-manager (merge + catalog released)
  → post-merge-improvement
```

### 1. Ingest

Read the spec (or write `docs/specs/features/<slug>.md` from `_TEMPLATE.md` and catalog it). Pull in `CONTEXT.md` and the system specs it touches. Completion: one path is the working spec. A second `features/<other>.md` or README catalog row on this `feat/` is another PR (`parallel-feature-work`).

### 2. Completeness (block validate until each box is yes)

- [ ] Problem and out-of-scope are explicit
- [ ] Acceptance criteria are testable and use `CONTEXT.md` terms. Every Domain terms word has a `CONTEXT.md` entry.
- [ ] Operator commands / npm scripts that change persisted state, and host-path construction of an architecture-named adapter, are acceptance criteria, not Assumptions
- [ ] Seams named (architecture spec if the product has one; otherwise the module interface tests will call)
- [ ] Events named or added if the product catalogs actions
- [ ] UI screens/primitives named or added if the product inventories them
- [ ] No open "TBD" that affects behavior

If a box is no: revise the spec with the user (or a written assumption they accept). Status stays `draft`. Completion: all boxes yes → set status `validated` and update `docs/specs/README.md`. Do not commit that validated file as a spec-only dump of every AC (`logical-commits`). The spec hunk (checkboxes, restates) lands with the first production that implements it.

### 2b. Look-may-grow

Look-may-grow skipping a *new* validate applies only to extra look chrome on a spec the user already Validated (`look-loop`). Still `spec-review-loop` after the first green commit; merge is still `release-manager`.

### 3. Isolate and dispatch

Stay on the default branch. Create a worktree on `feat/<slug>` (`using-git-worktrees`). Write the validated spec (and catalog row) in that worktree if those files are not already there. Do **not** commit a spec-only dump of every AC (`logical-commits`).

Dispatch `.agents/agents/spec-implementer/AGENT.md` with cwd = that worktree. Give it only: spec path, task statement (verbatim), worktree path, that it must read `AGENTS.md` and the spec. Do not pass a solution or a verdict.

Resume **that same conversation** for this spec (review fixes included). A new spawn of the same `AGENT.md` is only when that conversation is gone — then seed spec path, PR, HEAD, worktree, blocking thread ids.

Completion: the implementer's Return block has a PR number and HEAD SHA, or a need-user question you take to the user. Production files in this (orchestrator) tree means you implemented — delete that work and dispatch.

### 4. Release gate

Run **fresh** verification (read commands from `package.json` or the product's documented scripts; do not claim from an old run):

| Gate | Passes when |
|------|-------------|
| Spec | Status `validated`; criteria match tests |
| TDD | Each new test was seen RED, then GREEN |
| Types / unit / e2e | Exit 0, zero failures, zero errors |
| Spec sync | Each logical commit that changed behavior updated the spec |
| Language | No new undeclared synonym |
| Branch / PR | Work is on `feat/` or `fix/`, PR open |
| CI | Required GitHub checks green on HEAD |
| Review | `spec-reviewer` **Approve** on this HEAD |

Add product gate rows (observability, design system) when those specs exist. Any miss except review: resume `spec-implementer` with the failing row. Review misses go through `spec-review-loop`. Re-run **the whole table** after the implementer returns.

### 5. Release

`verification-before-completion` applies to every row: quote fresh command output, not an earlier run. Only when the table is green: dispatch `release-manager`. After it returns a merge SHA, run `post-merge-improvement`. Completion: merge SHA exists; catalog says `released`; review-cycle count is on the PR body. No merge → not released.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "One flake, ship it" | The gate is zero errors. Fix or quarantine with a failing test that names the flake. |
| "Spec is good enough" | Completeness boxes are the validate bar. Unchecked = `draft`. |
| "Types pass so it's released" | Types are one row. CI and review Approve still required. |
| "I reviewed it myself" | Dispatch `spec-reviewer`. Self-review is not a gate row. |
| "I'll just implement here, it's faster" | Dispatch `spec-implementer`. This session does not write production code. |

## Red flags

Calling the work done while status is `draft`. Skipping a red gate row. Claiming tests pass without a fresh command in this session. Merging without Approve. A spec-only commit that lists every AC after completeness. Implementing in the orchestrator session. Merging instead of dispatching `release-manager`.

**All of these mean: not released. Return to the loop.**
