---
name: writing-for-agents
description: >
  Use when creating or editing AGENTS.md, a skill under .agents/skills,
  an agent under .agents/agents, or when a spec change requires updating
  those procedures.
---

# Writing for agents

A spec change that changes **how work is done** is unfinished until the matching skill (or `AGENTS.md` pointer) is updated in the same change.

The packaging differs (AGENTS.md vs skill vs AGENT.md); the writing does not: the agent should take the same *process* every run.

When the file is a skill, also read [SKILL-MECHANICS.md](SKILL-MECHANICS.md).

## When a spec moves a skill

| Spec change | Skill that must move with it |
|-------------|------------------------------|
| Domain terms, CONTEXT.md, ADR | `domain-modeling` |
| Test layers or seams | `testing`, `tdd-loop` |
| Commit / branch / PR / CI | `logical-commits`, `github-ci-loop` |
| Review or release gate | `spec-review-loop`, `releasing-a-spec`, `spec-reviewer` |
| After merge, learn from review | `post-merge-improvement`, `post-merge-improver` |
| Two or more feat/ PRs at once | `parallel-feature-work`, `using-git-worktrees`, `releasing-a-spec` |
| Playwright / flow CI | `playwright-e2e`, `testing` |
| Module / seam / adapter vocabulary | `codebase-design` |
| Assumption vs acceptance criterion | `keeping-specs-current` |
| Restated AC with leftover sections / leftover test title / leftover Restates | `keeping-specs-current` |
| Domain terms line without a `CONTEXT.md` entry | `keeping-specs-current`, `domain-modeling` |
| Independently revertible ACs / grouping by screen / spec-only | `logical-commits` |
| Extra visible chrome without an AC | `keeping-specs-current` |
| How agents write docs | this skill |
| Product architecture / UI kit / telemetry / domain rules | the product skill that owns that fact — add a row here when you add that skill |

Completion: `git diff` shows spec + skill together when the procedure changed.

## Pointers

A **context pointer** (skill `description`, or an `AGENTS.md` line) names out-of-context material and the **branches** that should load it.

- Front-load the leading word.
- One trigger per distinct branch. Collapse synonyms.
- Do not repeat identity the body already carries.

## Two loads

- **Context load** — always-on: `AGENTS.md`, every skill description.
- **Cognitive load** — the human must remember a doc that has no pointer.

Put procedure behind a skill. Keep `AGENTS.md` as iron laws + pointers.

## Hierarchy

1. In-file steps (what to do, in order), each with a **completion criterion** (done vs not-done, exhaustive).
2. In-file reference (rules consulted on demand).
3. Disclosed reference (sibling file, loaded only when that branch fires).

Inline what every branch needs. Disclose what only some branches need.

## Levers

- **Leading words** already in the model (`seam`, `red`, `released`) beat coined jargon.
- **One home per fact.** Specs own product facts. Skills own procedures. `AGENTS.md` points.
- **Environment is truth** for scripts and versions (`package.json`). Do not cache them here.
- **Positive instructions.** State the target behaviour. A ban earns its place only as a hard guardrail, paired with the target.
- **Prune** no-ops and sediment. If a sentence does not change behaviour versus the default, delete the sentence.

## This pack's homes

- Skills: `.agents/skills/<name>/SKILL.md`
- Named agents: `.agents/agents/<name>/AGENT.md`
- Always-on: `AGENTS.md`
- Do not add a parallel vendor directory.
