# Codex Agent Workflow

A small, reusable operating model for agent-driven software development. It
defines orchestration, fresh implementation lifecycles, independent review,
context isolation, and concise handoffs without embedding any consuming
project's architecture, commands, CI names, or release rules.

## Adopt in another repository

1. Add this repository as a Git submodule at `.agent-workflow` and pin the
   desired tag or commit.
2. Keep a short project-level `AGENTS.md` that first directs agents to read
   `.agent-workflow/AGENTS.md`, then states project-specific rules and required
   documents.
3. Initialize submodules in fresh clones, worktrees, and CI:
   `git submodule update --init --recursive`.
4. Keep architecture, invariants, test commands, release procedures, and other
   concrete details in the consuming project.

Project instructions may explicitly specialize the shared defaults. Direct user
instructions remain higher priority than both.

Before delegating from a consuming repository, run a preflight in the worktree
that will contain the change. For example, for an isolated worktree and the
recommended submodule path:

```sh
git worktree add ../consumer-issue-123 -b codex/issue-123
git -C ../consumer-issue-123 submodule update --init --recursive -- .agent-workflow
git -C ../consumer-issue-123 submodule status -- .agent-workflow
git -C ../consumer-issue-123 rev-parse HEAD:.agent-workflow
git -C ../consumer-issue-123/.agent-workflow rev-parse HEAD
test -f ../consumer-issue-123/.agent-workflow/AGENTS.md
test -f ../consumer-issue-123/.agent-workflow/IMPLEMENTATION_AGENT.md
test -f ../consumer-issue-123/.agent-workflow/REVIEW_AGENT.md
test -f ../consumer-issue-123/.agent-workflow/CONTEXT_MANAGEMENT.md
test -f ../consumer-issue-123/.agent-workflow/HANDOFF.md
```

The submodule status and `HEAD` must identify the exact gitlink commit recorded
by the consuming repository. Also verify that the protocol files required by
the delegated roles exist; see the coordinator preflight in
`ORCHESTRATION.md`. If the directory is empty, the scoped `submodule update`
above is the normal recovery step rather than a reason to bypass the pinned
revision.

## Update the pinned workflow

Fetch the submodule, check out the reviewed tag or commit, validate the consuming
project, and commit the changed submodule pointer. Never track an unpinned
floating branch as the effective workflow version.

## Versioning

Published tags identify stable workflow revisions. Consumers remain pinned until
they explicitly review and commit an update.

