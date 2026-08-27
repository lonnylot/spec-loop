# {product}

{One sentence: the outcome this product exists for.}

This file and `.agents/` are the only project agent instructions. They are harness-agnostic.

## New agents — start here

1. Read this file, then `CONTEXT.md`, then `docs/specs/README.md`.
2. Treat specs as the product. Code is an implementation of a spec. A change that is not in a spec is unfinished.
3. Load the skill for the work you are doing **before** you edit code (table below). Skills live in `.agents/skills/`. Named agents live in `.agents/agents/`.
4. If you cannot name the spec file you are implementing or updating, stop and write or find it.

After ten minutes you should be able to answer: what outcome this app exists for; which spec is source of truth for the current task; what must never ship (unspecced behavior, untested behavior, silent actions).

## Spec-first (iron law)

```
NO BEHAVIOR WITHOUT A SPEC.
NO CODE CHANGE WITHOUT THE MATCHING SPEC UPDATE IN THE SAME COMMIT.
NO FEATURE WORK ON THE DEFAULT BRANCH.
NO SHIP UNTIL CI IS GREEN, A SEPARATE REVIEWER APPROVES THIS HEAD, AND THE RELEASE GATE IS CLEAN.
```

- Write or update the spec **before** production code.
- Keep the spec **current**: if implementation teaching contradicts the spec, fix the spec in the same logical commit (or revert the code).
- A stale spec is a defect. Discovering drift and leaving it is a failed task.
- Status lives on the spec: `draft` → `validated` → `released`. Only `validated` specs may be implemented. Only `released` specs may be called done.
- A spec change that changes **how work is done** updates the matching skill in the same change (`writing-for-agents`).

**REQUIRED:** `keeping-specs-current` on every behavior change. **REQUIRED:** `releasing-a-spec` when taking a spec from idea to ship (orchestrator: validate, dispatch `spec-implementer`, review, dispatch `release-manager`). **REQUIRED:** `verification-before-completion` before any done/green/released claim. The orchestrator session does not write production code.

### Where truth lives

| Kind | Path | Owns |
|------|------|------|
| Ubiquitous language | `CONTEXT.md` | Domain terms only. No implementation. |
| Spec catalog | `docs/specs/README.md` | Index + status of every spec. |
| Feature specs | `docs/specs/features/<slug>.md` | One vertical slice. Template: `docs/specs/features/_TEMPLATE.md`. |
| Hard decisions | `docs/adr/NNNN-slug.md` | Irreversible trade-offs only. |
| Procedures | `.agents/skills/*/SKILL.md` | How to write, test, review, and release. |
| Named agents | `.agents/agents/*/AGENT.md` | Roles: `spec-implementer` (resume per spec), `spec-reviewer` (fresh each dispatch), `release-manager` (merge), `post-merge-improver`. |

Add product-owned system specs (architecture, business, testing, design, observability) to this table when those files exist. Environment (`package.json`, configs, CI workflows) owns installed versions, scripts, and CI. Specs do not cache that.

## Skills

Load the matching skill **before** the work. Do not re-derive the procedure from this file.

| Skill | Reach for it when |
|-------|-------------------|
| `keeping-specs-current` | Any behavior change; suspected spec drift; leftover sections after restating an AC; leftover locked claims in a restated system spec; host-path construction parked in Assumptions |
| `writing-for-agents` | Editing AGENTS.md, skills, or agents; spec changed a procedure |
| `domain-modeling` | New or conflicting domain term; CONTEXT.md; ADR |
| `codebase-design` | Module shape, seam, adapter, depth |
| `testing` | Test placement, seams, mapping criteria, weaker-reading fixtures, early-return "and" that should stay one criterion |
| `tdd-loop` | Implementing or fixing; before production code; host-path constructor; orchestrator about to write production code |
| `logical-commits` | A criterion is green; deciding whether / what to commit; reconstruct after tests-after |
| `github-ci-loop` | Starting work; branching; opening a PR; waiting on CI; checks never started; unmergeable PR; rebase onto default |
| `spec-review-loop` | PR exists; merge/release; resume after context compaction; rebase voids Approve |
| `post-merge-improvement` | PR just merged; learn from review into skills/docs |
| `receiving-code-review` | Implementing or pushing back on review comments |
| `verification-before-completion` | About to claim done, green, fixed, or released |
| `releasing-a-spec` | Given a spec to implement; iterating; validating; shipping; dispatch spec-implementer |
| `parallel-feature-work` | Two or more feature specs at once; separate implementer agents |
| `using-git-worktrees` | Dispatch spec-implementer; isolated checkout for a feat/ branch; parallel agents |
| `playwright-e2e` | Browser user flows; flow CI job; test-server fakes; empty-state reset between files; unique checkout port for flow tests |

Handed a spec, or asked to implement/ship: `releasing-a-spec` (orchestrator). Two or more specs this session: `parallel-feature-work` first. Implementing any slice: dispatch `.agents/agents/spec-implementer/AGENT.md` (`tdd-loop` lives there). Isolated tree: `using-git-worktrees` for every implementer. Ready to review: `spec-review-loop` (fresh `spec-reviewer` every dispatch; resume the implementer on Changes requested). Ready to merge: dispatch `.agents/agents/release-manager/AGENT.md`. After merge: `post-merge-improvement`. Browser flow tests: `playwright-e2e`. Spec changed a procedure: `writing-for-agents`.

## GitHub loop (summary)

Full procedure is in the skills above. The always-on rule:

1. Orchestrator: validate the spec with the user. Stay on the default branch.
2. Worktree + `feat/<slug>` or `fix/<slug>` (`using-git-worktrees`). Dispatch `spec-implementer` there. Resume that conversation for this spec.
3. Implementer: one logical commit per green criterion (spec + tests + code). Open a PR after the first green commit so CI runs on every push.
4. Orchestrator: dispatch a **separate** `spec-reviewer` on a **fresh** context every time. Same-session self-review does not count. Changes requested → resume the implementer (`receiving-code-review`).
5. Repeat until **Approve** on this HEAD + green checks. Approve is incomplete until leftover review threads are GitHub-resolved (`is_resolved: true`), not just replied.
6. Dispatch `release-manager` to merge, write review-cycle count, and set the spec `released`. Unmergeable: resume `spec-implementer` to rebase onto default, then a fresh `spec-reviewer` (Approve is this HEAD). Then `post-merge-improvement`.

## Invariants

- Specs stay current. Drift is a bug.
- Tests and callers share the same seam.
- Feature work is never committed on the default branch.
- The orchestrator session does not write production code. Dispatch `spec-implementer`.
- Release requires green GitHub CI, a separate reviewer Approve on HEAD, and a clean release gate. Merge is `release-manager`.

## Anti-patterns

| Excuse | Reality |
|--------|---------|
| "Tiny change, skip the spec" | Tiny changes rot the catalog. Update the spec. |
| "I'll document after it works" | After-the-fact docs describe what you built, not what you meant. Spec first. |
| "Tests after is the same" | Tests-after confirm memory. Tests-first specify. Delete untested production code and start RED. |
| "I'll commit once at the end" | One blob cannot be reviewed per criterion. |
| "Work on main, PR later" | No branch, no CI loop. |
| "I'll just implement in this session" | Dispatch `spec-implementer`. The orchestrator does not write production code. |
| "I'll review my own PR" | Dispatch `spec-reviewer`. Same context is not a review. |
| "I'll merge it myself" | Dispatch `release-manager`. |
| "I'll just resolve the conflict here" | Resume `spec-implementer`. Rebase is coding. |
| "Local is green, skip CI" | GitHub checks on the PR are the shared gate. |
