---
name: testing
description: >
  Use when adding or placing tests, choosing seams, writing unit or
  Playwright tests, mapping acceptance criteria to tests, checking a
  weaker-reading fixture, splitting an "and" (any case) test name,
  an early-return "and" that should stay one criterion,
  proving a screen/shows criterion (not a source grep), leftover test
  filename after a deleted SUT, leftover test title after a restated
  AC, or updating a testing spec.
---

# Testing

The product's testing spec owns layers and coverage bars when that file exists. Tests prove spec criteria through public seams.

## When to use

- New test file or test layer
- "What do we test?" / "Do we need e2e?"
- Coverage of a feature spec

## Default layers

If the product has `docs/specs/testing.md`, use that. Otherwise:

| Layer | Proves | Runner |
|-------|--------|--------|
| Domain | Pure logic at a module interface | Unit (read the product's test script) |
| App | Orchestration with fake adapters | Unit + fakes |
| Flow | A catalogued user path in a real browser | Playwright (`playwright-e2e`) |

A criterion that names a screen, "shows", or "lists" is proven at the app or flow layer that fails if that UI is deleted.

## Steps

1. **Quote the criterion.** Open the spec. Write the test name as the criterion. If that name contains "and" (any case) or independent env/branch cases, split into one `it` each — only when the cases are independently revertible production (status vs content-type vs body; membership 404 vs happy path). Do not split an early-return reject (returns 400 and does not insert / does not put / leaves version N) into separate ACs if they share one production path: those are one criterion. If the next AC would be green without new production, fold the proof into this commit or do not split the bullet (`logical-commits`). The assertion must fail a weaker implementation of those words.

   Weaker readings this step exists to catch:

   | Criterion shape | Weaker fixture that must not stay green |
   |-----------------|-----------------------------------------|
   | OR / two parser arms | One fixture both arms can pass |
   | Tie-break | A single row; no second row sharing the primary key |
   | Uniqueness after remove | Sequential add-twice (length reused as id) |
   | ±N% / band | Only one side of the band |
   | Union member on a Result | A typed const assigned the member and asserted equal to itself |
   | "imported only from X" | A walk that skips a sibling directory or a new file in a claimed directory |
   | "a name the previous state did not list" | A name already on the previous fixture |
   | Non-empty placeholder | `/placeholder=/` on `placeholder=""` |
   | One row's own text | Document-level `toContain` or `[\s\S]*` that can cross a tag |
   | Packed "and" | One `it` covering independent cases |

   When a production module is deleted, rename or delete its dedicated test file in the same change. When an AC is restated, rename the `it` / `test()` that quoted the old words. Renaming is unfinished until that leftover test's assertions fail a weaker reading of the new words, or the leftover title drops the new words. A restated "does not show X" inverts the assertion (`not.toContain`); dropping `toContain` is not the proof. Completion: deleting the feature, or swapping in the weaker algorithm, fails this test for that reason.

2. **Pick the layer.** A `readFileSync` of source for an import string or class name is a parse test — it stays green if the mount is deleted and the string remains. An "only this route" / "no other route shows X" criterion is proven by rendering every other page so a second mount fails. Proving the named route has X, or that one former second surface is gone, is not that proof. Rendering a leaf widget in isolation does not prove a screen "shows" bullet. A FormData/parser test proves a parse criterion only. If Playwright is out of scope, render the composed markup the route mounts, or narrow the spec bullet. Completion: the test does not mount the framework to prove a pure domain function. Deleting the named UI fails a "shows" test.

3. **Confirm the seam.** Fake adapters at the seams the architecture spec (or `codebase-design`) names. If the criterion names a persistence behavior of a specific adapter, the test constructs that adapter — faking the port only proves the orchestrator called it. A `readFileSync` of wiring for a fake identifier is a parse test. Proof is a call under the test adapter, or a flow that fails if those constructors are unused. Completion: no mock of a domain collaborator that is not an adapter. No live third-party in tests.

4. **Independent expected values.** Pull numbers, event names, and catalog fields from the spec tree. Do not reimplement the unit under test inside the assertion.

5. **Telemetry contract.** If the product catalogs actions: for each new `action`, assert `capture` was called with that name (fake Telemetry). A property allowlist is proven against the observability catalog, not a handwritten table exercised on one action.

## Commands

Run whatever the product defines. Read scripts from the environment; do not invent npm script names.

## Anti-patterns

- Implementation-coupled (private methods, internal mocks; a class not in the spec)
- Tautological (`expect(fn(x)).toEqual(copyOfFn(x))`)
- Horizontal slicing (all tests, then all code) — use `tdd-loop` vertical slices
