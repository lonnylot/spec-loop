---
name: domain-modeling
description: >
  Use when pinning down domain terminology, changing CONTEXT.md, recording
  an ADR, or when a spec or conversation uses a new or conflicting domain
  term, or when a Domain terms line names a word CONTEXT.md does not define.
---

# Domain modeling

This skill is for **changing** the model. Reading `CONTEXT.md` for a word is a one-line habit any skill can do.

This pack assumes a **single context**: root `CONTEXT.md`, system ADRs in `docs/adr/`.

## When to use

- A new domain term appears, or the user used a synonym
- Spec and code disagree on a name or rule
- A hard-to-reverse trade-off is being chosen

## During the session

1. **Challenge the glossary.** If the user says a synonym and `CONTEXT.md` has a canonical term, name the conflict and pick one.
2. **Sharpen fuzzy words.** Propose a canonical term. Add it to `CONTEXT.md` in this change using [CONTEXT-FORMAT.md](CONTEXT-FORMAT.md). A feature Domain terms line is unfinished until each word has a `CONTEXT.md` entry. When a spec forbids the term as a title or field label, add those rivals under `_Avoid_`.
3. **Stress-test with a scenario.** Walk one concrete case that would break a fuzzy definition.
4. **Check the code.** If `CONTEXT.md` and the domain module disagree, surface it. Spec and glossary win until the user flips them.
5. **Glossary only.** `CONTEXT.md` has no implementation, no framework, no store. Decisions that are hard to reverse, surprising, and a real trade-off go in an ADR ([ADR-FORMAT.md](ADR-FORMAT.md)).

## ADR bar (all three)

Hard to reverse + surprising without context + real trade-off. Next number after the highest in `docs/adr/`. Skip easy reversals (a commit message is enough).

## After a glossary or ADR change

If a skill still uses the old word or the old decision, update that skill in the same change (`writing-for-agents`).
