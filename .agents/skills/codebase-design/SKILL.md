---
name: codebase-design
description: >
  Use when designing or reshaping a module, placing a seam, adding an
  adapter, judging depth, or when testing needs the module / interface /
  seam / adapter vocabulary.
---

# Codebase design

Design **deep modules**: a lot of behaviour behind a small interface, at a clean seam, tested through that interface.

If the product has `docs/specs/architecture.md`, locked seams live there. This skill is the vocabulary and the tests for whether a new module earns its keep.

## Glossary (use these words)

**Module** — anything with an interface and an implementation (function, file, or slice). _Avoid_: unit, component, service.

**Interface** — everything a caller must know: types, invariants, error modes, ordering, performance. _Avoid_: API, signature (too narrow).

**Implementation** — what is inside. Distinct from **adapter**.

**Seam** — where the interface lives; where you can swap behaviour without editing the caller. _Avoid_: boundary.

**Adapter** — a concrete thing that satisfies an interface at a seam (a SQLite repository, an in-memory repository, an HTTP client).

**Depth** — leverage at the interface: behaviour per unit of interface the caller must learn. Deep = small interface, large behaviour. Shallow = interface almost as big as the body.

**Leverage** — what callers get from depth.

**Locality** — what maintainers get: change and bugs sit in one place.

## Principles

- Depth is a property of the **interface**, not line count.
- **Deletion test:** delete the module. If complexity vanishes, it was a pass-through. If it reappears across callers, it was earning its keep.
- **The interface is the test surface.** Callers and tests cross the same seam.
- **One adapter = hypothetical seam. Two adapters = real one.** Real seams are the rows in the architecture spec when that file exists — do not keep a local example that contradicts that table.

## Testability

Accept dependencies; do not construct them in domain:

```ts
// yes
function saveOrder(order: Order, orders: OrderRepository) {}

// no
function saveOrder(order: Order) {
  const orders = sqliteOrderRepository(getSqlite())
}
```

Return Results from expected failures. Do not mutate an aggregate in place from the adapter.

## When the shape is in question

- Classify dependencies and deepen with [DEEPENING.md](DEEPENING.md).
- Compare alternative interfaces with [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md).

Then update `docs/specs/architecture.md` if a seam row changed, and the product's placement skill if placement rules changed (`writing-for-agents`).
