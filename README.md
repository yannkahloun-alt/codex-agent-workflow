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

Create ticket worktrees outside the primary checkout by default. A sibling
directory is one simple option, as shown above; a coordinator-managed external
root is equally valid. Record the ticket identity, exact absolute worktree path,
exact local branch, and authoritative repository/worktree registration when the
worktree is created. Directory and branch naming conventions are not proof of
ownership.

## Keep the pinned workflow current before a ticket

Before starting a new ticket, compare the recorded submodule gitlink with the
exact commit selected by the consuming project's deterministic approved-stable
source. Prefer published stable tags or releases; for example, policy can select
the greatest non-prerelease Semantic Version tag in fetched tags, or an exact
approved tag. Never use a remote default branch or another floating ref as the
effective workflow version.

A different commit is not necessarily newer. Automatically propose it only
when its exact commit is provably a descendant of the current pin. An older,
divergent, or ancestry-unverifiable candidate leaves the pin unchanged and is
reported for consumer-policy handling or separately authorized manual
reconciliation.

An already-current pin is a no-op:

```text
Pinned workflow: 0123456789abcdef (workflow path .agent-workflow)
Approved stable:  0123456789abcdef (tag v1.4.0)
No bump required; create the ticket worktree from the refreshed default branch.
```

When an eligible newer approved commit exists, update it separately from ticket
work:

```text
Pinned workflow: 0123456789abcdef
Approved stable:  fedcba9876543210 (tag v1.5.0)
Reconciled or opened the dedicated workflow-bump branch and pull request,
changed only the gitlink, and ran the consumer's validation and independent
review. After merge, refreshed the default branch and rechecked the exact pin;
ticket work may now start.
```

If the stable source is unreachable, preserve availability by default:

```text
Approved-stable lookup failed: upstream unavailable.
The pin remains 0123456789abcdef, the last known-good workflow revision.
Proceeding with the ticket; consumer policy may instead require this to block.
```

A candidate that fails the consuming repository's gates is not adopted:

```text
Candidate fedcba9876543210 failed consumer validation.
The pin remains 0123456789abcdef; the ticket continues on that known-good
revision unless consumer policy requires a block.
```

Identify bump proposals by workflow path, old gitlink, and exact candidate
commit. Reuse the lowest-numbered open pull request with the same identity and
treat later matches as duplicates, subject to the consumer's close policy. This
makes retries and concurrent checks converge without changing a running ticket.
The full lifecycle, including validation, review, merge, default-branch refresh,
and the source repository's recursive-update exclusion, is specified in
`ORCHESTRATION.md`.

## Versioning

Published tags identify stable workflow revisions. Consumers remain exactly
pinned while the pre-ticket lifecycle validates, reviews, and commits any
approved update.

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
that the ticket commits are preserved in the target branch, updates or verifies
the exact recorded path and branch against the authoritative repository's
registered-worktree metadata, confirms the ticket worktree is clean, removes
that worktree, safely deletes the local ticket branch, prunes or equivalently
refreshes remote-tracking references, and leaves the primary worktree at the
merged target history on a non-ticket branch. For example:

```text
PR #123 merged; ticket commits are reachable from the updated default branch.
Primary worktree: main (usable, clean, and current with merged target history).
Removed clean ticket-owned worktree: ../consumer-issue-123
Deleted merged local branch: codex/issue-123
Pruned remote-tracking references.
```

A legacy in-repository worktree can make the primary checkout appear dirty:

```text
C:\_dev\consumer\
  .worktrees\
    issue-7\
```

Do not hide `.worktrees/` in `.gitignore` or exempt it from untracked-state
checks. If lifecycle state identifies that exact path and branch, Git still
registers the path for the authoritative repository with the expected branch,
the pull request is merged and preserved, and the ticket worktree is clean,
remove that exact registered worktree. Remove the workflow-created `.worktrees`
container only if it is then empty. Leave unrelated contents untouched. With
the sole untracked-path blocker gone, refresh the primary checkout and finish
the normal sequence:

```text
PR #8 merged; issue-7 commits are reachable from origin/main.
Verified registered worktree C:\_dev\consumer\.worktrees\issue-7 on
codex/issue-7 from recorded lifecycle ownership; worktree is clean.
Removed that exact worktree and its now-empty workflow-created container.
Deleted merged local branch codex/issue-7 and pruned/refreshed refs.
Primary worktree: main (clean and current with origin/main).
```

If the container holds any unrelated file or directory, preserve the container
and those contents. Genuine uncommitted or untracked user work in either
worktree continues to block the applicable destructive cleanup.

Cleanup is guarded when local work is not proven safe to remove. For example:

```text
PR #123 merged successfully.
Cleanup skipped: ../consumer-issue-123 contains untracked notes.txt, and the
local ticket branch has one commit not proven reachable from preserved history.
The ticket-owned worktree and branch were left intact for recovery; unrelated
worktrees and branches were not touched. Refreshing remote-tracking references
succeeded. The merge result is unchanged; local cleanup requires follow-up.
```

A dirty or diverged primary worktree is guarded the same way: do not overwrite
it to update the target branch, do not continue destructive cleanup, and report
the preserved local state and required follow-up.

