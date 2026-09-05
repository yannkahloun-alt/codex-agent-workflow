# Implementation Agent

A coherent implementation unit should normally start with fresh context and an
isolated branch or worktree. There are two execution modes:

- **Local authoritative-worktree mode** is the preferred and default mode when
  the coordinator can verify a local checkout, its repository identity, and its
  ownership of the ticket branch/worktree.
- **Remote-GitHub implementation mode** is a constrained fallback only when no
  authoritative local worktree is available in the execution environment and
  the consuming repository permits GitHub API writes. It is an alternative
  transport for the same ticket lifecycle, not a relaxation of policy.

1. Read the assigned request or issue and its acceptance criteria.
2. Load the shared workflow and the consuming project's required instructions.
3. Inspect only relevant documentation, code, and tests.
4. Establish or reproduce the contract before editing.
5. Implement the smallest coherent change.
6. Add regression coverage for behavior changes where applicable.
7. Run only automated checks that higher-priority policy or direct user
   instruction identifies as genuinely CI-unavailable. Do not run a routine
   test, lint, static-analysis, coverage, mutation, or equivalent quality gate
   locally when an equivalent required CI gate exists; CI owns that authoritative
   exact-head validation. Adding or updating regression coverage remains part of
   implementation.
8. Review the workspace and complete diff, including appropriate status and
   diff-integrity inspection, and correct findings.
9. In local mode, commit and push the coherent change, then reconcile or create the ticket's
   single draft implementation pull request so authoritative CI can validate its
   exact head. Do not require CI-equivalent local gates before that draft PR.
10. Reuse that same pull request for every later commit, CI fix, review fix, and
    readiness transition. A new head starts a new CI/review generation; it does
    not authorize another implementation pull request.
11. Provide a concise handoff and end this implementation lifecycle.

The coordinator may explicitly assign file-only work. In that case, edit only
the named workspace and file scope, report every changed file and validation
result, and leave Git operations to the coordinator. Absence of Git metadata
does not authorize initializing a repository or substituting another checkout.

If selected work is deferred, follow the consuming project's issue-history and
communication rules before switching tasks.

## Remote-GitHub implementation protocol

Use this protocol only after all of the following preconditions are evidenced
from authoritative GitHub data: immutable repository identity (prefer the host
repository ID), the assigned issue identity, the existing ticket PR and its
existing head branch, the PR base branch and protection status, and the exact
pinned workflow revision. Read the consuming repository's `AGENTS.md` and the
role protocol files at that exact pinned revision before preparing a change.
The target must be the existing ticket branch, must differ from the PR base
branch, and must not be `main` or another protected/base branch. Reconcile the
existing ticket PR; remote execution never creates a replacement PR.

For each scoped ticket or review fix:

1. Resolve the authoritative repository, issue, PR, head branch, base branch,
   and current PR head. Record the full 40-character head SHA.
2. Fetch every target file from that head and record its current blob SHA.
   Prepare only the requested scoped replacement.
3. Immediately before each write, re-fetch the PR and require its head to
   still equal the recorded 40-character SHA. Re-fetch the target blob SHA and
   require it still equals the value used to prepare that replacement.
4. Write only that replacement to the existing ticket branch using a
   SHA-guarded GitHub file-update primitive. Never force-push, update a ref
   directly, rewind a ref, or write the base/protected branch.
5. Obtain the resulting commit and re-fetch the PR. Require the PR head to
   transition from the pre-write SHA to the expected resulting 40-character
   SHA. Fetch the resulting target file and inspect the resulting commit and
   full PR diff to prove that all changes remain in scope.

Any failed identity, branch, head, blob, expected-transition, or scope check
is a concurrency or authority conflict: stop the write sequence, reconcile the
new authoritative state, and either prepare a new scoped change against it or
report a blocker. Never overwrite newer work blindly. A remote write that moves
the PR head invalidates every earlier CI and independent-review generation.
Hand off the new exact head and require fresh authoritative CI and a fresh
independent read-only review keyed to that head before merge. All normal final
live reconciliation and merge guards still apply.

