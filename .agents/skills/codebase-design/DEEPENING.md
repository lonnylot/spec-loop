# Deepening

How to deepen a cluster of shallow modules. Vocabulary is in [SKILL.md](SKILL.md).

## Dependency categories

| Kind | Example | How to test |
|------|---------|-------------|
| In-process | A pure domain function | Call the interface directly. No adapter. |
| Local-substitutable | SQLite via a repository port | In-memory adapter in unit tests. |
| Remote, owned | A service you run | Port + in-memory adapter. |
| True external | A vendor HTTP API | Injected port; fake adapter in tests. |

## Seam discipline

- One adapter is indirection. Two adapters (prod + test, or two vendors) make the seam real.
- Internal seams stay private. Do not put them on the module interface just so tests can reach them.

## Replace, don't layer

Once tests exist at the deepened interface, delete the old tests that pinned the shallow pieces. New tests assert observable outcomes through the interface and survive internal refactors.
