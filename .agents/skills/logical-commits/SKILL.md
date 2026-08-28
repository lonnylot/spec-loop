---
name: logical-commits
description: >
  Use when a spec criterion goes green, when finishing a slice, before
  opening or updating a pull request, when deciding whether to commit,
  when grouping by screen, when independently revertible ACs share a
  commit, when leftover checkboxes share one production path, when a
  spec-only commit lists every AC, reconstruct after tests-after
  already landed in the TDD slice, when a review-thread or CI-fix
  commit would land on HEAD, or when a dedicated test or Playwright
  flow would land after its production in that slice.
---

# Logical commits

One commit is one finished thought: one spec criterion (or one review/CI fix) with its spec, tests, and code together. Independently revertible production hunks are separate commits even on one screen. A spec-only commit that lists every AC is not a slice.

## When to commit

- A `tdd-loop` criterion is GREEN and the spec matches the code
- A single review thread or a single CI cause is fixed and green
- Spec status flip (`validated` / `released`) after the gate that allows it
- One production change several named screens mount, plus the shell it left — splitting those routes is empty / tests-after
- Leftover checked ACs that share one production path (`testing`) — fold into the commit that made them true

## When not to commit

- Tests are red (except a human-requested WIP, which is not a release commit)
- Two unrelated criteria in one tree
- One screen commit that lands multiple independent ACs
- Grouping by screen
- A spec-only commit that lists every AC
- Secrets, `.env`, or credentials
- On the default branch for feature work — branch first (`github-ci-loop`)

## How

1. `git status` and `git diff`. Stage only this slice.
2. Message names the criterion or review thread: `feat(orders): total stays within ±5%`.
3. Choose the slice:

   **Before the PR is the review unit (`tdd-loop`).** Commit spec + tests + production together. Completion: `git show --stat` lists all three when all three changed. A later test-only commit for the criterion you just implemented is tests-after — reconstruct into that production commit. A Playwright flow (or any dedicated test) in a later commit than the production that implemented that criterion, in this slice, is the same miss.

   If tests-after already landed in this slice, do not add another test-only commit. If the test commit is already the next commit, fixup onto the production commit. If it is not consecutive, do not cherry-pick the old patch (end-of-file `it()` context will conflict): from current HEAD, append that `it()` onto the production commit, then drop the later test-only commit. A subject that names two ACs with "and" is two commits when each has unique production; leftover checkboxes that share one path stay one commit (`testing`). Completion: `git log -p` of this slice shows that criterion's test in the same commit as its production.

   **After the PR is open.** A missing or weaker test found in review or CI is one review/CI commit on HEAD (`When to commit`). Do not reconstruct it into an earlier AC commit. Do not force-push to combine. Completion: `git log` shows the review/CI fix as a new commit on HEAD; the PR three-dot diff (`git diff <base>...<head>`) proves the AC.

   **Leftover checkboxes that share one production path.** If several checked ACs share one production hunk (early return / one handler path) and extra tests stay green without that hunk, unsplit (`testing`) — fold into the commit that made them true. Independently revertible production (status vs content-type vs body) still splits. Reconstruct-into-N is unfinished if N-1 commits would have empty unique production. Completion: each commit has unique production, or leftover shared-path ACs are one commit.

Prefer conventional prefixes (`feat`, `fix`, `docs`, `test`, `refactor`, `chore`). The subject (and body if needed) states what changed. Specs do not keep a Changelog section.

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "I'll commit once at the end" | One blob cannot be reviewed or reverted per criterion. |
| "Checkpoint before I forget" | Unfinished slices are not logical. Finish GREEN, then commit. |
| "Spec in a follow-up commit" | Same-commit spec update is the iron law. |
| "One screen is one commit" | Grouping by screen is the miss. Split independently revertible ACs. |
| "Spec first, then one feat" | Spec hunk lands with the first production that implements it. |
| "I'll fold tests-after in a follow-up on top of HEAD" (TDD slice) | Reconstruct into the production commit. A follow-up cannot un-do tests-after in this slice. |
| "Reconstruct the review test into the feat commit" | After the PR is open, one review/CI commit on HEAD is the slice. The ship unit is the PR three-dot diff. |
| "N leftover checkboxes means N commits" | Unsplit if they share one production path (`testing`). Fold into the commit that made them true. Independently revertible production still splits. |

## Red flags

Empty or "WIP" messages on a PR. Default-branch commits for a feature. Code committed without the spec hunk that describes it. Dedicated tests in a follow-up commit after the production that needed them in the TDD slice. Reconstructing a review-thread or CI-fix commit into an earlier AC commit. One commit that ships several independent ACs together. A commit subject that names two new ACs with "and" when each has unique production. N empty-production commits for leftover checkboxes that share one production path. Spec-only then one feat. Grouped by screen.
