# {Feature name}

Status: draft

## Problem

One paragraph: who, what, why.

## Acceptance criteria

Testable bullets. Each becomes one test name. One behavior per bullet; "and" (any case) means split (`testing`). Restating a bullet renames that test. A leftover title that now includes the restated words must assert those words (or drop them from the leftover title).

- [ ] …

## Domain terms

Terms from `CONTEXT.md` this feature uses. New terms are added to `CONTEXT.md` in the same change. Every listed word already exists there, or is added in this change. Forbidding a term as a visible title or field label updates that term's `_Avoid_`.

## Seams

Which architecture seams this feature crosses. Name them when the product has an architecture spec; otherwise name the module interface the tests will call.

## Events

If the product catalogs actions (observability spec): `action` names from that catalog (add rows there if new). Otherwise omit this section.

## UI

If the product inventories screens and primitives (design-system spec): screens and primitives this feature uses. Starting or stopping compose of an existing primitive updates that inventory. Otherwise omit this section.

## Assumptions

Environment facts (hostname, test adapter, platform flags). A shippable operator command or npm script that changes persisted state is an acceptance criterion so `tdd-loop` can name it — do not park it here.

## Out of scope

Explicit non-goals.

## Restates

Released specs whose bullets this change rewrites. Listing a path without rewriting that file is unfinished. When Restates names a system spec, leftover locked claims this HEAD does not implement are rewritten or given ACs (`keeping-specs-current`).
