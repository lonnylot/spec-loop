---
name: playwright-e2e
description: >
  Use when adding or changing Playwright tests, a test:e2e script, a
  script-existence guard, a browser user flow, the CI job that runs
  flow tests, a unique TCP port per checkout for flow tests,
  Playwright webServer env, blanking live API keys, an
  empty-state flow after a mutating spec file, or an
  already-mounted UI re-open. Also when updating a Flow row in a
  testing spec.
---

# Playwright e2e

Flow tests prove the catalogued user path through a real browser. Domain and app unit tests stay on fakes. This skill is how to place and run Playwright. Criteria live in the testing spec (when the product has one) and the feature spec.

## When to use

- New Playwright spec, `test:e2e` script, or CI flow job
- Register / login / a named user journey in a browser
- "Do we need e2e for this?"
- Flow tests start a TCP server; two checkouts would share a port

## Loop

1. **Quote the criterion.** Open the feature spec and the Flow row in the testing spec if it exists. Test name is that criterion. The Playwright spec lands in the same commit as the production that implements that criterion (`logical-commits`). Completion: deleting the flow would fail this test for that reason.

2. **Script and CI.** Read `package.json` (or the product's equivalent). If `test:e2e` is missing, add it (Playwright's documented runner) in the same change. If CI has no job that runs that script, add one on `pull_request` — unit tests + build do not prove a click path. A unit test that only checks `scripts["test:e2e"]` is a non-empty string stays green if the script is `true` or `echo`. Completion: the manifest and the workflow name the same script; any script-existence test asserts the runner token (`playwright test`), not `length > 0`.

3. **Unique checkout port.** If flow tests start a TCP server (Playwright `webServer`, or equivalent), bind a port unique to this checkout. Derive environment `PORT` (or the product's equivalent) from the worktree path; the product's Playwright config reads that value. `reuseExistingServer` (or equivalent) is false unless that process belongs to this checkout. Completion: a second checkout can run the same flow suite at the same time without a bind failure, and does not attach to the first checkout's server. `workers: 1` is not that proof.

4. **Seams in the test server.** The documented test adapter constructs auth, persistence, and any model/HTTP fakes **once per Node process** so register, login, and later requests in the same test still see that state (`codebase-design` composition root). Keep that process-global state **inside one test** so a multi-step flow still sees the account. Reset the fake store/mail **between files** so an empty-state spec still passes after a mutating file has run. Playwright's webServer env blanks live API keys so the suite cannot reach third-party hosts. Point any file database at a temp path. Completion: a second request in the same test still sees the registered account; empty-state still passes after a mutating file ran (file order does not matter); `workers: 1` is not that proof; the spec does not call the live vendor. A leftover identifier in wiring source is not this proof (`testing`).

5. **Drive the UI the user sees.** Prefer role + accessible name (the same label the screen already has). Add `data-testid` only when no accessible name exists, and only in the same change as the screen. One flow per test. A re-open after a panel is already mounted is close, then follow-up submit, then assert open without clicking the trigger — first-paint `data-state="open"` is not this proof (`testing`). Completion: the test fails if that button or heading is removed.

6. **Session.** Register (or login) through the UI in the test. Do not mint a session cookie in the spec unless a documented test-only helper exists. Completion: an unauthenticated visit still redirects to the login route (or the product's documented unauthenticated path).

7. **Prove it.** Run the `test:e2e` script fresh (`verification-before-completion`). Completion: exit 0, or you have a failing log.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "Unit tests already click the action" | App tests fake the browser. Flow is Playwright. |
| "We'll add the CI job later" | Local green is not the shared gate. |
| "Hit the real vendor, it's more realistic" | Live third-parties in CI are nondeterministic and leak keys. Fake the seam. |
| "The server on localhost is already up" | It may be another checkout. Unique port; reuse only when that process is this checkout. |
