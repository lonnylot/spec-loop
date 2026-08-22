---
name: post-merge-improvement
description: >
  Use after a pull request is merged, when updating a PR description with
  review-cycle count, or when turning review feedback into skill and spec
  fixes.
---

# Post-merge improvement

After merge, harvest review feedback and make the next agent unable to repeat those misses.

## When to use

- `releasing-a-spec` / `spec-review-loop` just merged a PR
- User says "learn from that PR" / "update skills from review"

Skip when the merged PR only changed `.agents/`, `AGENTS.md`, or docs/specs with no product code.

## Steps

1. **Branch** `chore/learn-pr-<n>` from the default branch (`github-ci-loop`).
2. **Dispatch** a fresh-context agent seeded with `.agents/agents/post-merge-improver/AGENT.md`. Give it the PR number, owner/repo, and nothing else about your self-assessment.
3. **Review the agent's edits.** Confirm each miss maps to one home. Reject a second copy of a rule that already exists.
4. **Push and open a PR** for the learning branch. Run `spec-review-loop` on that PR until Approve + green CI + merge. Skills/docs PRs are not exempt. Do not ask the user to merge in place of the loop.

## Completion

- PR body of the **merged** product PR has `## Review cycles` with every spec-reviewer Verdict counted (same-user `COMMENT` with Changes requested or Approve counts; empty-body thread replies do not)
- Each harvested miss has a skill, spec, or `AGENTS.md` pointer change (or a written reason it was already prevented and you fixed discovery)
- Learning commits are small and named after the miss they close
