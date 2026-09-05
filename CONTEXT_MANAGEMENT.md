# Context Management

Context isolation is a correctness tool as well as a cost control.

- Start independent implementation units and reviews with fresh context.
- Prefer a fresh read-only review subagent in the ticket workspace; context
  isolation does not by itself require a separate task or worktree. When that
  subagent is available, a separate review task or thread is forbidden.
- Give agents the task, repository, acceptance criteria, and required policy;
  do not preload another agent's reasoning as trusted evidence.
- Inspect repository areas relevant to the assigned work rather than scanning
  by default.
- Carry forward concise decisions, results, blockers, exact revisions, and
  commands needed to reproduce evidence.
- For a remote-GitHub-capable implementation or fixer, carry authoritative
  repository identity, issue and PR numbers, head branch, base branch, current
  full 40-character head SHA, pinned workflow revision, scoped requested
  change, target-file/blob SHAs when prepared, relevant CI generation, and the
  prior review generation plus why it is current or invalid. State whether a
  local worktree was verified, unavailable, or an immutable environment
  limitation, and which remote capabilities were verified. This is lifecycle
  evidence, not a durable conversation identity.
- Do not carry forward full logs or transcripts unless they are the evidence
  under investigation.
- Retain context intentionally for tightly related follow-up work when doing so
  improves correctness or avoids needless rediscovery.

