# Agent home

Portable instructions for any coding agent. Project rules always load from the repo-root `AGENTS.md`.

| Path | What it is |
|------|------------|
| `../AGENTS.md` | Always-on rules |
| `../CONTEXT.md` | Domain language |
| `../docs/specs/` | Product source of truth |
| `skills/*/SKILL.md` | Procedures the working agent follows. Update with the spec when procedure changes. |
| `agents/*/AGENT.md` | Named agents spawned in a **fresh** context (`spec-reviewer`, `post-merge-improver`) |

Do not keep a parallel vendor-specific copy. This directory is the only agent home.
