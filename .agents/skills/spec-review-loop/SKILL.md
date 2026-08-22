---
name: spec-review-loop
description: >
  Use when a pull request exists, before merge or release, when addressing
  review comments, or when a spec must be iterated with a separate reviewer
  until it is approved and released.
---

# Spec review loop

A **separate** agent reviews the PR against the spec and the repo rules and leaves comments. You address them. Repeat until that agent Approves, CI is green, and the release gate is clean. Then merge and mark the spec `released`.

Same-context self-review does not count.

## When to start

- PR is open (`github-ci-loop`)
- Local `releasing-a-spec` gate is green
- You want merge or "it's done"

## Loop

```
dispatch spec-reviewer (fresh context)
  → reviewer comments + verdict
  → Changes requested: fix (tdd-loop + logical-commits) → push → wait CI
  → reply on each thread with the fixing SHA (do not resolve)
  → dispatch reviewer again on new HEAD
  → reviewer resolves fixed blocking threads and leftover notes (GitHub resolve_thread)
  → Approve only after get_review_comments shows those threads is_resolved: true
  → Approve + CI green + release gate → merge
  → update PR body with review-cycle count
  → spec status=released
  → post-merge-improvement
```

### Dispatch

Start an isolated agent (subagent, second session, or the harness fresh-context tool). Seed it with `.agents/agents/spec-reviewer/AGENT.md`. Give it only:

- Spec path(s) and the original task statement (verbatim)
- Base SHA and HEAD SHA
- PR number
- That it must read `AGENTS.md` and the spec itself

Do not pass your reasoning or a verdict of your own.

### Address comments

Load `receiving-code-review`. For each **blocking** comment: confirm it against the spec and the code. If it is right, fix with `tdd-loop`, one `logical-commits` commit, push. If it is wrong, reply on the thread with the spec/code evidence; do not silently ignore it.

Notes are optional. Unclear comments: ask on the thread before coding. Do not implement a batch while any blocking item is unclear.

### Re-review

Re-dispatch on the new HEAD after CI is green. The reviewer must Approve **this** SHA. An Approve on an old SHA is void.

After the reviewer reports Approve, read `get_review_comments`. If any thread that is not still blocking has `is_resolved: false`, the review is incomplete — send the reviewer back to `resolve_thread`. A COMMENT that says "Resolving" is not enough.

### Release

Only when all three hold:

1. Reviewer verdict **Approve** on HEAD
2. GitHub checks green on HEAD
3. `releasing-a-spec` gate still green

Then merge the PR, set the spec (and catalog) to `released`, quote the merge SHA plus the check evidence, write the review-cycle count into the PR description, and run `post-merge-improvement`.

**Review cycles** = number of spec-reviewer submissions whose body has a Verdict of Changes requested or Approve (each is one cycle). A same-user `COMMENT` that says treat it as Changes requested or Approve counts. Empty-body thread replies do not. Append (do not replace) a `## Review cycles` section to the PR body:

```
## Review cycles
N (X Changes requested, Y Approve)
```

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "I'll review my own diff" | Same context rubber-stamps. Dispatch the reviewer agent. |
| "Comments are nits, merge" | Blocking comments are the bar. Notes may land later. |
| "Reviewer approved yesterday" | Approve applies to a SHA. Re-review after new commits. |
| "Human can squash later" | Merge is the release act. Do it only after the three holds. |
| "Enough review rounds; hand off" | The loop ends on Approve + green CI + merge. Time and leftover notes are not an exit. |

## Red flags

Implementing in the reviewer role. Merging with Changes requested. Calling the spec `released` before the merge SHA exists. Implementer resolving review threads. Stopping the loop before Approve on this HEAD. Approving while leftover notes are still unresolved on the PR.
