# Orchestration

The user's main conversational agent normally coordinates independent units of
work instead of becoming a permanent implementation thread.

For an issue handoff such as `work on ticket #N`, the coordinator owns the
end-to-end outcome. It selects roles internally and continues through the
repository-defined issue lifecycle without asking the user to orchestrate
agents or approve routine transitions.

## Responsibilities

- clarify requirements and acceptance criteria;
- create or refine issues when useful, without forcing issue use;
- decompose and order coherent work units;
- launch fresh implementation agents in isolated branches or worktrees;
- launch fresh independent reviewers under project policy;
- monitor high-level progress and coordinate follow-up work;
- retain concise status, decisions, blockers, and handoffs.
- own the Git and workspace boundary: choose the authoritative checkout,
  isolate work as policy requires, integrate delegated file changes, inspect
  the final diff, and perform authorized branch, commit, push, and pull-request
  operations, including guarded cleanup after a successful merge;
- keep delegated agents' write scope explicit and do not imply that a
  file-editing assignment transfers repository or Git ownership.

Avoid absorbing full implementation transcripts, exhaustive logs, large diffs,
or unrelated historical context. Several tightly related changes may share an
agent when their common context materially helps; unrelated work should use
fresh context.

## Pre-ticket workflow freshness

In a consuming repository, resolve workflow freshness before creating the
ticket branch or worktree and before delegating ticket work. This check applies
only to tickets that have not started. Never change the workflow gitlink in a
running ticket worktree; it continues with the exact known-good revision with
which it began.

Consumer policy must identify the workflow submodule path, its authoritative
upstream repository, and a deterministic approved-stable selector. Prefer
published stable tags or releases. A suitable default is the greatest stable
Semantic Version tag in the fetched tag set, excluding prereleases; policy may
instead name an approved release series or an exact approved tag. Resolve the
selected tag to a commit and use that exact commit throughout the comparison,
proposal, and merge. Do not treat a remote default branch, a moving branch, or
an unrecorded notion of "latest" as an approved source.

Run the freshness lifecycle from a clean, refreshed default branch:

1. Read the exact workflow gitlink recorded by the consuming repository and
   resolve the approved-stable source to an exact candidate commit.
2. If the source cannot be reached or the approved selector cannot be resolved,
   record the lookup failure and continue the ticket from the pinned known-good
   commit. Consumer policy may explicitly require this condition to block.
3. If the candidate equals the gitlink, make no workflow change and proceed
   with the ticket.
4. A differing candidate is eligible for an automatic bump only when it is
   provably a descendant of the pinned commit. If it is older, belongs to a
   divergent history, or ancestry cannot be established, do not adopt it
   automatically. Report the condition and continue from the pin unless
   consumer policy requires a block or a separately authorized manual
   reconciliation.
5. For an eligible newer candidate, reconcile any existing workflow-bump pull
   requests before creating one. The proposal identity is the tuple of workflow
   path, old gitlink, and exact candidate commit. Reuse the lowest-numbered open
   pull request with that exact identity and treat later matches as duplicates;
   refresh state before creation so concurrent coordinators do not knowingly
   create another proposal. Consumer policy controls whether duplicate or stale
   proposals are closed, but selection of the proposal to continue must remain
   deterministic.
6. Create or update a dedicated workflow-bump branch and pull request from the
   refreshed default branch. Change only the submodule pointer to the exact
   candidate, then run the consuming repository's required validation and
   independent review. Merge only under its normal merge policy. A candidate
   that fails validation or review leaves the consuming repository's pin
   unchanged; continue from the pinned revision unless consumer policy says to
   block, and report the rejected candidate.
7. After a successful bump merge, refresh the consuming repository's default
   branch and repeat the exact-pin comparison. Only then create the ticket
   branch or worktree and perform the delegation preflight below.

When this repository is itself the configured workflow source, skip this
consumer update lifecycle. It must not open a recursive workflow-bump proposal
against itself.

## Consuming-repository preflight

Before delegating work, verify from the consuming repository that its configured
shared-workflow submodule is initialized at the exact commit recorded by the
superproject. Do not infer readiness from a branch name, tag, or remote default.
Confirm that the checked-out workflow contains the protocol files required for
the assigned roles, including `AGENTS.md`, `IMPLEMENTATION_AGENT.md`,
`REVIEW_AGENT.md`, `CONTEXT_MANAGEMENT.md`, and `HANDOFF.md` when those roles use
them. Give each delegated agent the consuming repository's instructions, the
applicable protocol files, and the verified workflow revision.

An empty submodule directory is recoverable: run
`git submodule update --init --recursive -- <workflow-path>` from the consuming
worktree, then repeat the exact-revision and file checks. Optional skills or
tooling may help load the protocol; when unavailable, read the checked-in files
directly. That fallback does not permit replacing or skipping verification of
the superproject's pinned commit.

Do not delegate until the preflight passes. Report a blocker precisely: name the
worktree and submodule path, the expected and observed revisions (when
available), any missing protocol file, and the failing recovery command or
access limitation. Avoid describing a merely uninitialized but recoverable
submodule as a permanent blocker.

## Delegation across workspace boundaries

When a delegated agent can edit files but its workspace has no usable Git
metadata, the coordinator remains responsible for repository state:

- If the coordinator has a Git checkout, assign an explicit file scope and
  destination workspace. Treat the agent's output as file-only, copy or apply
  it into the coordinator's authoritative checkout if the workspaces are not
  shared, and perform all status, diff, validation, commit, push, and
  pull-request operations there.
- If no Git metadata exists anywhere, do not invent branch, commit, or
  pull-request evidence. Establish an explicit directory boundary, have the
  delegated agent report every changed file and validation result, inspect the
  resulting files or a before/after comparison, and hand off the file changes
  with Git lifecycle steps clearly marked unavailable for the next owner.

In either case, delegated agents must not initialize a repository, select a
different checkout, or claim Git completion unless the coordinator explicitly
transfers that authority under higher-priority project policy.

## Ticket worktree placement and ownership

Create ticket worktrees outside the primary repository working tree by default,
for example as a sibling directory or under another coordinator-managed
external root. The path need not follow a particular naming convention. The
invariant is that it is not beneath the primary checkout unless higher-priority
repository or environment policy explicitly requires that layout. Creating the
worktree must not by itself introduce untracked state into the primary
checkout.

At creation time, retain lifecycle state that ties the resource to the ticket:

- the ticket or issue identity;
- the exact absolute worktree path;
- the exact local ticket branch; and
- the authoritative repository identity and Git worktree registration.

Carry this state through delegation, merge, cleanup, and final handoff. Names
such as `.worktrees`, `issue-123`, or `codex/*` are organizational labels, not
ownership evidence. Before removal, compare the recorded identity with current
`git worktree list --porcelain` metadata from the authoritative repository and
verify that the exact path is still registered there with the expected branch,
or with an equivalent detached state whose ticket commits are proven preserved.
Do not remove a path that is absent, registered to another repository, or
associated with an unexpected branch.

## Post-merge cleanup

After a pull request merges successfully, the coordinator must explicitly
follow the merge with an attempt to clean up only the local branch and worktree
created for that ticket. Cleanup is a separate post-merge step: a cleanup
failure does not undo or change the reported merge result, but its exact state
and required follow-up belong in the final handoff.

Before deleting anything, verify from the repository's source of truth that the
pull request merged and that the ticket branch's work is present in the
preserved target history. Validate the exact recorded worktree path, repository,
and branch against Git's registered-worktree metadata, then confirm that ticket
worktree has no uncommitted or untracked files. Higher-priority repository
policy may impose additional removal guards. Inspect the primary or default
worktree as well: genuine uncommitted, untracked, or diverged state there blocks
destructive cleanup.

Remove the verified ticket worktree before updating the primary or default
worktree when a legacy workflow-created worktree beneath the primary checkout
is itself the only untracked-path blocker. This bounded exception does not apply
when the primary worktree has any other local state. After removal, remove its
container directory only if the workflow created that exact container and it is
actually empty; leave any unrelated files or directories untouched. Never
recursively delete a container based on a name such as `.worktrees`.

Refresh or prune remote-tracking references so local state does not imply that
a deleted remote branch still exists. Update the primary or default worktree to
the merged target history, or verify that it is already current with that
history, and ensure it is usable on a non-ticket branch. Uncommitted, untracked,
or diverged state in that worktree blocks the update and further destructive
cleanup unless the only reported untracked state was the now-removed, exactly
verified legacy worktree/container. Preserve and report all other state instead
of overwriting it.

Delete the exact local ticket branch only after its worktree is removed and the
branch is merged or its commits are otherwise proven preserved. Uncommitted,
untracked, or unmerged work blocks destructive cleanup: preserve the affected
worktree and branch and report the evidence instead of forcing removal or
deletion. Do not clean up worktrees, branches, or files merely because their
names resemble the ticket; their ownership must be established from recorded
lifecycle identity and current Git metadata. Final handoff must report cleanup
completion or the exact ownership, registration, branch, cleanliness, policy,
or primary-worktree condition that prevented it.

