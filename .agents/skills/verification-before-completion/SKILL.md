---
name: verification-before-completion
description: >
  Use when about to claim work is complete, fixed, passing, or released,
  before a commit or pull request, or whenever a status claim needs
  evidence.
---

# Verification before completion

```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

If you have not run the proving command in this turn, you cannot claim it passes.

## Gate

Before any success / done / green / released wording:

1. Name the command or check that would prove it (`package.json` scripts, `gh pr checks`, reviewer verdict).
2. Run it fresh. Read the full output and the exit code.
3. Claim only what that output shows. If it fails, say it failed.

Skip a step = the claim is false.

## What proves what

| Claim | Fresh evidence |
|-------|----------------|
| Tests pass | Test script, 0 failures |
| Types / lint clean | Those scripts, 0 errors |
| Bug fixed | The reproduction test fails without the fix, passes with it |
| CI green | Checks on **this** HEAD |
| Spec released | Merge SHA + catalog status + `releasing-a-spec` table |
| Reviewer approved | Verdict on this SHA, not yesterday's |
| Agent finished | Diff in the worktree, not the agent's report |

## Red flags

"Should pass." "Looks good." "Done!" before a command in this turn. Trusting a subagent. Using an earlier run as proof.

**All of these mean: run the command, then speak.**
