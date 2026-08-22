# ADR format

ADRs live in `docs/adr/` as `NNNN-slug.md`. Scan for the highest number and increment.

```md
# {Short title}

{1-3 sentences: context, decision, why.}
```

Optional only when they earn it: Status (`proposed | accepted | deprecated | superseded by ADR-NNNN`), Considered Options, Consequences.

Write one when **all three** hold: hard to reverse, surprising without context, real trade-off. A rename does not.
