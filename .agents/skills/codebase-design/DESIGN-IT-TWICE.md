# Design it twice

When an interface is in doubt, produce at least two **radically different** shapes before coding. First idea is rarely the deepest.

## 1. Frame

Write the constraints, the dependency category from [DEEPENING.md](DEEPENING.md), and a sketch that is **not** a proposal. Show the user.

## 2. Two or three designs

Each design must differ in kind, not in renaming:

- Minimize the interface (1–3 entry points, max leverage)
- Optimize the common caller
- Ports at every I/O dependency

Each design lists: interface (types, errors, invariants), a caller example, what is hidden, adapters, trade-offs on **depth**, **locality**, and **seam placement**.

Use `CONTEXT.md` names.

If the harness can run isolated agents, give each design to a fresh agent so they do not converge. If not, write them yourself in separate passes without looking at the previous until compare.

## 3. Compare and pick

Present designs, then recommend one (or a hybrid). Update `docs/specs/architecture.md` with the winner before implementation when that file exists.
