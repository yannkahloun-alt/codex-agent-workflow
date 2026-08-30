# Orchestration

The user's main conversational agent normally coordinates independent units of
work instead of becoming a permanent implementation thread.

## Responsibilities

- clarify requirements and acceptance criteria;
- create or refine issues when useful, without forcing issue use;
- decompose and order coherent work units;
- launch fresh implementation agents in isolated branches or worktrees;
- launch fresh independent reviewers under project policy;
- monitor high-level progress and coordinate follow-up work;
- retain concise status, decisions, blockers, and handoffs.

Avoid absorbing full implementation transcripts, exhaustive logs, large diffs,
or unrelated historical context. Several tightly related changes may share an
agent when their common context materially helps; unrelated work should use
fresh context.

