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
  operations;
- keep delegated agents' write scope explicit and do not imply that a
  file-editing assignment transfers repository or Git ownership.

Avoid absorbing full implementation transcripts, exhaustive logs, large diffs,
or unrelated historical context. Several tightly related changes may share an
agent when their common context materially helps; unrelated work should use
fresh context.

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

