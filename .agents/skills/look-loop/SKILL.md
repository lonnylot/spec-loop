---
name: look-loop
description: >
  Use when iterating the look of named screens, screenshot-and-iterate,
  a feature spec catalogs computed CSS, extra heading, image, landmark,
  or static page during look, or brand tokens or a11y during look.
---

# Look loop

Iterate the look of named screens. Brand tokens and a11y stay locked. The feature spec names screens and look ACs; computed CSS lives in the stylesheet those screens mount.

Screenshot-and-iterate is `spec-implementer`, not the orchestrator.

## When to use

- Look of named screens
- Screenshot-and-iterate
- A feature spec lists computed CSS
- Extra heading, image, landmark, or static page during look
- Brand tokens or a11y during look

## Loop

1. **Lock tokens and a11y.** Keep brand tokens and a11y as the locked spec already states them. Iterate look on named screens (spacing, type, composition) with those locks unchanged. A token or a11y change is its own validated spec. Completion: this pass leaves token and a11y bullets as they were, or a separate spec owns that change.

2. **Name the screens.** Quote the feature spec's named screens. One look pass is one named screen, or one stylesheet several named screens mount (`logical-commits`). Viewport meta copied onto sibling HTML is that shell, not extra chrome (`logical-commits`). Look ACs name screens, primitives, or visible chrome. Completion: you can name the screen or the shared stylesheet; look ACs do not list computed CSS.

3. **Look may grow.** An extra heading, image, landmark, or static page the look needs is an acceptance criterion in the same commit (`keeping-specs-current`). Completion: that chrome has a checked AC on this HEAD, or it is not in the tree.

4. **Skip a new Validate only for extra look chrome.** Look-may-grow skipping a *new* validate applies only to extra look chrome on a spec the user already Validated. Still open the PR after the first green commit (`github-ci-loop`) and enter `spec-review-loop`. Merge is still `release-manager`. Completion: the spec was already `validated`; the new AC is look chrome; a PR exists; review and merge still follow those skills.

5. **Screenshot-and-iterate.** In the `spec-implementer` worktree, screenshot the named screen, change look, screenshot again, until the named screen matches the locked tokens and the look ACs. Completion: the implementer's tree has the look pass; the orchestrator checkout has no production look files.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "CSS values belong in the feature spec" | The spec names screens. Computed CSS lives in the stylesheet. |
| "I'll tweak the brand token while I'm here" | Tokens and a11y stay locked. A token change is its own spec. |
| "Extra heading is just look, skip the AC" | Extra heading, image, landmark, or static page is an AC in the same commit. |
| "Look-may-grow means skip review" | Skip a *new* Validate only. Still `spec-review-loop`; still `release-manager`. |
| "Orchestrator can screenshot faster" | Screenshot-and-iterate is `spec-implementer`. |
| "Viewport meta on the sibling page is extra chrome" | It is that shell (`logical-commits`). |

## Red flags

Feature spec lists computed CSS. Brand token or a11y restated during look. Extra heading, image, landmark, or static page with no AC. Viewport meta on sibling HTML treated as extra chrome. Look PR skipped `spec-review-loop` or merged without `release-manager`. Orchestrator screenshot-and-iterate.

**All of these mean: load this skill. Put the AC or the lock back.**
