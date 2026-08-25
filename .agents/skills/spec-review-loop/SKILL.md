---
name: spec-review-loop
description: >
  Use when a pull request exists, before merge or release, when addressing
  review comments, resume after context compaction, resume spec-implementer
  for review fixes, dispatch release-manager, or when a spec must be
  iterated with a separate reviewer until it is approved and released.
---

# Spec review loop

A **separate** agent reviews the PR against the spec and the repo rules and leaves comments. The orchestrator loads this skill. Repeat until that agent Approves, CI is green, and the release gate is clean. Then dispatch `release-manager`.

Same-context self-review does not count. This session does not write production code and does not merge.

## When to start

- PR is open (`github-ci-loop`)
- Local `releasing-a-spec` gate is green
- You want merge or "it's done"

## Loop

```
dispatch spec-reviewer (fresh context every time)
  → reviewer comments + verdict
  → Changes requested: resume spec-implementer (receiving-code-review)
      → push → wait CI
  → implementer replies on each thread with the fixing SHA (does not resolve)
  → dispatch reviewer again on new HEAD (fresh context, same AGENT.md)
  → reviewer resolves fixed blocking threads and leftover notes (GitHub resolve_thread)
  → Approve only after get_review_comments shows those threads is_resolved: true
  → Approve + CI green + release gate → dispatch release-manager
  → post-merge-improvement
```

### Resume

The loop does not end at a batch of commits or a context limit. On resume, read the PR (`gh pr view` or the harness GitHub API), checks on this HEAD, and unresolved review threads, then continue until Approve on this HEAD + green CI and you have dispatched `release-manager`. Completion: those reads ran in this session before any "are we done?"; the three Release holds are true or the next loop step is in progress. Do not ask the user whether to keep going.

### Dispatch reviewer

Start an isolated agent (subagent, second session, or the harness fresh-context tool). Seed it with `.agents/agents/spec-reviewer/AGENT.md`. **Fresh context every dispatch** — do not resume the reviewer's transcript. Give it only:

- Spec path(s) and the original task statement (verbatim)
- Base SHA and HEAD SHA
- PR number
- That it must read `AGENTS.md` and the spec itself

Do not pass your reasoning, the implementer's reasoning, or a verdict of your own.

### Address comments

Resume **the same** `spec-implementer` conversation for this spec (`.agents/agents/spec-implementer/AGENT.md`). Give it the blocking threads verbatim, PR number, and HEAD SHA. Do not fix production code in this session.

If that conversation is gone: new spawn of the same `AGENT.md`, seeded with spec path, PR, HEAD, worktree, blocking thread ids.

The implementer loads `receiving-code-review`. Notes are optional. Unclear comments: the implementer returns a need-user question; take it to the user before resuming them. Do not implement a batch in the orchestrator while any blocking item is unclear.

### Re-review

Re-dispatch on the new HEAD after CI is green. The reviewer must Approve **this** SHA. An Approve on an old SHA is void.

After the reviewer reports Approve, read `get_review_comments`. If any thread that is not still blocking has `is_resolved: false`, the review is incomplete — send the reviewer back to `resolve_thread`. A COMMENT that says "Resolving" is not enough.

### Release

Only when all three hold:

1. Reviewer verdict **Approve** on HEAD
2. GitHub checks green on HEAD
3. `releasing-a-spec` gate still green

Dispatch `.agents/agents/release-manager/AGENT.md` (fresh context) with spec path, PR number, expected HEAD SHA, owner/repo. Do not merge in this session. After it returns a merge SHA, run `post-merge-improvement`.

Review-cycle count is written by `release-manager`. The formula lives in that agent.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "I'll review my own diff" | Same context rubber-stamps. Dispatch the reviewer agent. |
| "Comments are nits, merge" | Blocking comments are the bar. Notes may land later. |
| "Reviewer approved yesterday" | Approve applies to a SHA. Re-review after new commits. |
| "Human can squash later" | Merge is the release act. Dispatch `release-manager` only after the three holds. |
| "I'll fix the comment here" | Resume `spec-implementer`. This session does not write production code. |
| "Enough review rounds; hand off" | The loop ends on Approve + green CI + `release-manager`. Time and leftover notes are not an exit. |
| "Should I keep going?" | Read the PR, checks, and threads this session. Resume. Do not ask. |

## Red flags

Implementing in the reviewer role. Implementing in the orchestrator session. Merging in this session instead of dispatching `release-manager`. Merging with Changes requested. Calling the spec `released` before the merge SHA exists. Implementer resolving review threads. Stopping the loop before Approve on this HEAD. Asking the user whether to keep going instead of reading the PR. Approving while leftover notes are still unresolved on the PR. Resuming the spec-reviewer transcript.
