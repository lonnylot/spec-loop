# Skill mechanics

How a **skill** differs from other agent docs. Writing quality is in [SKILL.md](SKILL.md).

## Invocation

- **Model-invoked** — has a `description` so the agent can load it on its own (and other skills can name it). Always also reachable by name. Use this for every skill unless it must be human-only.
- **User-invoked** — `disable-model-invocation: true`. The human is the index. Use only for rare, expensive, or dangerous procedures.

Write `description` as triggering conditions ("Use when…"), not a summary of the workflow. A workflow in the description becomes a shortcut agents follow instead of the body.

`name`: letters, numbers, hyphens. Body starts after the frontmatter.

## Split by invocation

Pay a new always-loaded description only when a distinct leading word should fire on its own, or another skill must reach it.

## Router

If user-invoked skills pile up, one user-invoked router names them. It can only hint; it cannot fire user-invoked skills.
