# Implementation Agent

A coherent implementation unit should normally start with fresh context and an
isolated branch or worktree.

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
9. Commit and push the coherent change, then reconcile or create the ticket's
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

