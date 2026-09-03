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

## Issue handoff examples

End to end, with Git available:

> **User:** Work on ticket #123.
>
> **Coordinator:** Resolves the issue and repository policy, chooses and
> coordinates implementation and review roles, owns the isolated checkout and
> Git operations, validates the final diff, and completes the normal pull-request
> lifecycle without asking the user to select roles or approve routine steps.

File-only delegation, with Git available only to the coordinator:

> **Coordinator to implementation agent:** In the supplied workspace, edit only
> `docs/usage.md` for ticket #123 and report the changed files and validation.
> Do not perform Git operations.
>
> **Coordinator:** Integrates or observes those file changes in the authoritative
> checkout, inspects the diff, validates, and performs the authorized Git and
> pull-request steps.

If neither workspace has Git metadata, the same delegation remains file-only.
The coordinator records the directory boundary, inspects the changed files or a
before/after comparison, reports validation, and hands off the files while
stating that branch, commit, push, and pull-request steps were unavailable.

## Post-merge cleanup examples

Normal cleanup follows a confirmed successful merge. The coordinator verifies
that the ticket commits are preserved in the target branch, switches the
primary worktree to a non-ticket branch, confirms the recorded ticket worktree
is clean, removes that worktree, safely deletes the local ticket branch, and
prunes or equivalently refreshes remote-tracking references. For example:

```text
PR #123 merged; ticket commits are reachable from the updated default branch.
Primary worktree: main (usable and clean).
Removed clean ticket-owned worktree: ../consumer-issue-123
Deleted merged local branch: codex/issue-123
Pruned remote-tracking references.
```

Cleanup is guarded when local work is not proven safe to remove. For example:

```text
PR #123 merged successfully.
Cleanup skipped: ../consumer-issue-123 contains untracked notes.txt, and the
local ticket branch has one commit not proven reachable from preserved history.
The ticket-owned worktree and branch were left intact for recovery; unrelated
worktrees and branches were not touched. Refreshing remote-tracking references
succeeded. The merge result is unchanged; local cleanup requires follow-up.
```

