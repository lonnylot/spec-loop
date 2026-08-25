---
name: receiving-code-review
description: >
  Use when a spec-reviewer or human left pull-request comments, before
  implementing review feedback, or when feedback is unclear or looks wrong.
---

# Receiving code review

Verify, then act. Do not performatively agree. Do not implement a comment you have not checked against this repo.

This skill is for the **implementer** (`.agents/agents/spec-implementer/AGENT.md`). The orchestrator resumes that agent; it does not apply this skill in its own checkout. The reviewer is `.agents/agents/spec-reviewer/AGENT.md` via `spec-review-loop`.

## Pattern

1. Read every comment before changing code.
2. Restate each blocking item, or ask if it is unclear.
3. Check the spec, `CONTEXT.md`, and the code.
4. If it is right: `tdd-loop` + one `logical-commits` commit + push. A reply that names the grouping and does not split independently revertible ACs has not addressed a `logical-commits` thread.
5. If it is wrong: reply on the **thread** with spec/code evidence. Do not silently skip it.
6. If it conflicts with a user decision or a locked spec: stop and ask the user.

Unclear items block the whole batch. Do not implement 1–3 and 6 while 4–5 are fuzzy.

## Sources

- **User:** implement after you understand; still ask on scope.
- **`spec-reviewer` or other agents:** skeptical. Confirm it matches `docs/specs/` and the locked patterns. Push back when it invents a new stack or a synonym for a `CONTEXT.md` term.

## Order

Clarify first. Then: blocking / security → small fixes → logic. Each fix gets its own test. `verification-before-completion` before you say the thread is done.

## Thread replies

Reply on the inline GitHub thread (`gh` or the harness GitHub API), not as a new top-level PR comment. Include the fixing SHA. Do **not** resolve the thread — the `spec-reviewer` resolves it only after it re-validates the fix.

## Voice

State the requirement or the fix. No "great point" / "absolutely right" / thanks. If you pushed back and were wrong: "Checked X; you were right because Y. Fixing."
