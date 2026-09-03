# Implementation Agent

A coherent implementation unit should normally start with fresh context and an
isolated branch or worktree.

1. Read the assigned request or issue and its acceptance criteria.
2. Load the shared workflow and the consuming project's required instructions.
3. Inspect only relevant documentation, code, and tests.
4. Establish or reproduce the contract before editing.
5. Implement the smallest coherent change.
6. Add regression coverage for behavior changes where applicable.
7. Run the project's focused and pre-merge gates.
8. Review the complete diff and correct findings.
9. Commit, push, and open or update the pull request as project policy requires.
10. Provide a concise handoff and end this implementation lifecycle.

The coordinator may explicitly assign file-only work. In that case, edit only
the named workspace and file scope, report every changed file and validation
result, and leave Git operations to the coordinator. Absence of Git metadata
does not authorize initializing a repository or substituting another checkout.

If selected work is deferred, follow the consuming project's issue-history and
communication rules before switching tasks.

