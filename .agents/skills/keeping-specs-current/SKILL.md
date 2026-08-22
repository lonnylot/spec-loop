---
name: keeping-specs-current
description: >
  Use when adding a feature, changing behavior, fixing a bug, writing or
  reviewing a pull request, when two spec bullets can conflict, when
  restating an AC while UI/Events/Assumptions still describe the old
  behavior, when Restates omits a released spec that still describes
  deleted behavior, when Restates names leftover files whose bullets
  were not rewritten, when leftover action POST remains after a deleted
  control, when a leftover test title still quotes a restated AC, when
  a Domain terms line names a word CONTEXT.md does not define, when
  tightening a fake or test-double parse, when adding an operator CLI,
  or whenever CONTEXT.md, docs/specs, or docs/adr may be missing or stale.
---

# Keeping specs current

Specs are the product. Code implements them. A change with no matching spec update is unfinished.

## When to use

- Any behavior change, including "tiny" ones
- New feature, bug, or refactor that alters what the system does
- Suspected drift between code and `docs/specs/`

## Iron law

```
NO BEHAVIOR WITHOUT A SPEC.
NO CODE CHANGE WITHOUT THE MATCHING SPEC UPDATE IN THE SAME CHANGE.
```

Status on every spec: `draft` → `validated` → `released`.

## Steps

1. **Name the spec.** Open `docs/specs/README.md`. Identify the system spec and/or `docs/specs/features/<slug>.md` this change implements. Completion: you can point at a path. If none exists, create it from `docs/specs/features/_TEMPLATE.md` and add a catalog row **before** code.

2. **Read before edit.** Read that spec, `CONTEXT.md` (terms), and any linked system spec. When the change adds a caller, npm script, or CLI that reuses a constructor, search other feature specs for exclusive "only X" / "constructed only" bullets the new caller would falsify. Completion: you can quote the criterion you are implementing and every exclusive bound the new caller touches.

3. **Edit the spec first.** New or changed rules, events, primitives, seams, or terms go into the spec (and `CONTEXT.md` / other system specs they belong in). Extra visible chrome without an AC is a behavior change — add an AC first or omit it. A feature Domain terms line that names a word `CONTEXT.md` does not define is unfinished. When a spec forbids using a term as a visible title or field label, that term's `_Avoid_` lists title / field label. Do not add a Changelog section — the commit message is the history. If two bullets cannot both hold, write the resolution in the spec before `tdd-loop`. Silent drop is not a resolution.

   Restating or deleting an AC is unfinished until UI, Events, Assumptions, Used by, every spec that AC restates, and the `it` / `test()` titles that quoted the old words no longer describe the deleted behavior. Search `docs/specs/` and `it("` / `test("` titles for the deleted words. Add every spec hit to Restates and rewrite the leftover bullets. Rename leftover test titles (`testing`). A leftover title that now includes the restated words must prove those words or drop them (`testing`). Listing a path in Restates without rewriting that file is unfinished. Completion of that search: grep for the deleted phrase is empty except the restated sentences; every Restates path now uses the new words; no leftover `it` / `test()` still quotes the old words.

   Deleting a visible control is unfinished until the action no longer accepts that POST, or Out of scope names the leftover branch. Restate an exclusive script-name bound as the constructor file (and the scripts allowed to run it) so both specs still hold. A shippable operator command or npm script that changes persisted state is an acceptance criterion — Assumptions are environment facts, not a place to park the command. Completion: the spec describes the intended behavior, including clash cases; leftover sections of the same file agree; status is at least `draft`.

4. **Implement via `tdd-loop`.** Do not start production code until step 3 is done and, for ship work, `releasing-a-spec` has marked the spec `validated`.

5. **Sync on the way out.** If implementation taught a tighter rule, update the spec in the same logical commit (`logical-commits`). A tighter fake adapter / test-double parse is a behavior change — restate that bullet in the same commit, or revert. Flip catalog status only through `releasing-a-spec`. Completion: the commit that lands the behavior also lands the spec hunk.

6. **Move the procedure.** If the spec change alters *how* work is done (folders, seams, events, review, terms, exclusive "constructed only" sentences), update the matching skill in the same change (`writing-for-agents`). The spec→skill map lives in that skill. Completion: no skill still teaches the old procedure.

## Which file owns the fact

| Fact | File |
|------|------|
| Term | `CONTEXT.md` |
| Feature slice | `docs/specs/features/<slug>.md` |
| Hard-to-reverse trade-off | `docs/adr/NNNN-slug.md` (only if hard to reverse, surprising, and a real trade-off) |
| Agent procedure | `.agents/skills/<name>/SKILL.md` — same change as the spec, via `writing-for-agents` |
| Stack, module, seam | `docs/specs/architecture.md` when the product has one |
| Domain rule | `docs/specs/business.md` when the product has one |
| Test layer, seam list | `docs/specs/testing.md` when the product has one |
| Primitive, token | `docs/specs/design-system.md` when the product has one |
| Event or span name | `docs/specs/observability.md` when the product has one |

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "Tiny change, skip the spec" | Tiny changes rot the catalog. Update the spec. |
| "I'll document after it works" | After-the-fact docs describe memory, not intent. Spec first. |
| "The test is the spec" | Tests prove a criterion. The criterion lives in `docs/specs/`. |
| "ADR for this rename" | Easy reversals are a commit message, not ADRs. |

## Red flags

- About to write production code and cannot name the spec path
- Spec still says something the code will no longer do
- New synonym for a `CONTEXT.md` term
- A Domain terms line names a word `CONTEXT.md` does not define
- Catalog has no row for a new feature file
- A new operator CLI or npm script lives only under Assumptions
- Exclusive "only script X" wording still stands after a second script calls the same constructor
- An AC is restated but UI / Events / Assumptions still describe the deleted behavior
- Restates omits a released spec that still describes the deleted behavior
- Restates names a leftover file whose bullets still use the deleted words
- A deleted control still has an action POST the spec does not name
- An AC is restated but a `test()` title still quotes the old words
- Two numbered rules in the same spec cannot both hold
- A fake / test-double parse tightened with a new unit test and no spec hunk
- Extra visible chrome with no AC

**All of these mean: stop coding. Update the spec.**
