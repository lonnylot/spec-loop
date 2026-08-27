---
name: tdd-loop
description: >
  Use when implementing any feature or bugfix, before writing production
  code, when adding an operator CLI or npm script that changes persisted
  state, a host-path constructor, the orchestrator is about to write
  production code, or when the user mentions TDD, red-green-refactor,
  the build loop, or looping.
---

# TDD loop

The build/test loop. Spec criterion first. Then red. Then green. Then one logical commit.

```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

Write code before the test? Delete it. Start over. Do not keep it as reference. Do not adapt it.

## When to use

Always for features, fixes, and behavior changes. Exceptions only if the human says so: throwaway prototypes, generated code, config-only files. An operator CLI or npm script that changes persisted state is production code, not config-only.

If this session is the orchestrator (`releasing-a-spec` dispatching agents), do not run this loop here — dispatch `.agents/agents/spec-implementer/AGENT.md`.

## Loop

1. **Criterion.** One independent behavior from a `validated` spec bullet (or `draft` only while exploring a test that you will throw away). If the bullet contains "and" (any case) or independent env/branch cases, split — one `it` per case — unless they share one early return (`testing`). Completion: independently revertible cases are one `it` each; a shared early-return title stays one `it` (those packed words are that one behavior).

2. **RED.** Load `testing`. Write one failing test at the seam the testing spec (or this pack's default layers) names. If the criterion is a browser flow, that test is Playwright (`playwright-e2e`) and lands with that production (`logical-commits`). After an AC that names an architecture-named production adapter, the RED test must fail if the documented composition root (host `fetch` / `listen` / equivalent) never constructs that adapter. Injecting the runtime is not that proof (`testing`). Copying the constructor identifier into source is a parse test (`testing`). Run it. Completion: it fails because the behavior is missing, not because of a typo; the assertion would also fail a weaker reading of the criterion (that skill); deleting the host-path constructor would fail this test. If it passes, or if swapping in the weaker algorithm would still pass, you tested existing behavior — fix the test.

3. **GREEN.** Smallest code that passes that test. No extra branches, events, or UI. Extra chrome without an AC is `keeping-specs-current`. A leftover POST that skips the new path is an extra branch — delete it or name it Out of scope (`keeping-specs-current`). Run the test (and neighbors you touched). Completion: the test passes; output is clean.

4. **Sync.** If the slice taught a tighter rule, update the spec in the same tree (`keeping-specs-current`). A tighter fake / test-double parse is a tighter rule.

5. **Commit.** One logical commit for this criterion (`logical-commits`). Then the next criterion — do not batch. Grouping by screen is batching.

Bugs: write the failing reproduction first, then fix.

Work sits on a feature branch (`github-ci-loop`). After the first green commit, the PR is open so CI runs on every push.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "Too simple to test" | The test is shorter than the debug cycle. |
| "I'll test after" | Tests-after describe memory. Delete the code. RED first. |
| "I already clicked through it" | Manual is not replayable. Automate the criterion. |
| "Keep this as reference" | You will adapt it. That is tests-after. Delete. |
| "TDD will slow me down" | Untested behavior ships wrong. |
| "Operator CLI is just a chore script" | It changes persisted state. RED first. |

## Red flags

Code before test. Test passes on first run. Cannot explain the red. "Just this once."

**All of these mean: delete production code. Start at RED.**
