---
name: releasing-a-spec
description: >
  Use when handed a spec to implement, asked to iterate on a spec, validate
  a spec, ship a feature, release without errors, or when about to
  commit a validated spec before the first criterion.
---

# Releasing a spec

Take a spec from ingest to `released` with a clean gate. Errors return you to RED, not to "ship anyway."

## When to use

- User pastes or points at a spec
- "Implement this" / "ship it" / "iterate until it's right"
- Feature status is `draft` or `validated`

Two or more specs in one session: load `parallel-feature-work` before this loop.

## Loop

```
ingest → completeness → iterate until validated
  → branch (github-ci-loop)
  → each criterion: tdd-loop → logical-commits
  → after first green commit: open PR (CI starts)
  → local release gate
  → spec-review-loop until Approve + CI green
  → merge → spec status=released → post-merge-improvement
```

### 1. Ingest

Read the spec (or write `docs/specs/features/<slug>.md` from `_TEMPLATE.md` and catalog it). Pull in `CONTEXT.md` and the system specs it touches. Completion: one path is the working spec. A second `features/<other>.md` or README catalog row on this `feat/` is another PR (`parallel-feature-work`).

### 2. Completeness (block validate until each box is yes)

- [ ] Problem and out-of-scope are explicit
- [ ] Acceptance criteria are testable and use `CONTEXT.md` terms. Every Domain terms word has a `CONTEXT.md` entry.
- [ ] Operator commands / npm scripts that change persisted state are acceptance criteria, not Assumptions
- [ ] Seams named (architecture spec if the product has one; otherwise the module interface tests will call)
- [ ] Events named or added if the product catalogs actions
- [ ] UI screens/primitives named or added if the product inventories them
- [ ] No open "TBD" that affects behavior

If a box is no: revise the spec with the user (or a written assumption they accept). Status stays `draft`. Completion: all boxes yes → set status `validated` and update `docs/specs/README.md`. Do not commit that validated file as a spec-only dump of every AC (`logical-commits`). The spec hunk (checkboxes, restates) lands with the first production that implements it.

### 3. Branch, implement, commit

Create `feat/<slug>` (`github-ci-loop`). For **one** criterion at a time: `tdd-loop` then `logical-commits`. After the first green commit, open the PR so CI runs. Load `keeping-specs-current`, `testing`, and any product skills that criterion touches.

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

Add product gate rows (observability, design system) when those specs exist. Any miss: write or fix a failing test, fix the product, commit, push, re-run **the whole table**. Review misses go through `spec-review-loop`.

### 5. Release

`verification-before-completion` applies to every row: quote fresh command output, not an earlier run. Only when the table is green: merge the PR, set spec status `released`, update the catalog, quote merge SHA and check evidence, then run `post-merge-improvement`. Completion: merge SHA exists; catalog says `released`; review-cycle count is on the PR body. No merge → not released.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "One flake, ship it" | The gate is zero errors. Fix or quarantine with a failing test that names the flake. |
| "Spec is good enough" | Completeness boxes are the validate bar. Unchecked = `draft`. |
| "Types pass so it's released" | Types are one row. CI and review Approve still required. |
| "I reviewed it myself" | Dispatch `spec-reviewer`. Self-review is not a gate row. |

## Red flags

Calling the work done while status is `draft`. Skipping a red gate row. Claiming tests pass without a fresh command in this session. Merging without Approve. A spec-only commit that lists every AC after completeness.

**All of these mean: not released. Return to the loop.**
