# Context Management

Context isolation is a correctness tool as well as a cost control.

- Start independent implementation units and reviews with fresh context.
- Prefer a fresh read-only review subagent in the ticket workspace; context
  isolation does not by itself require a separate task or worktree.
- Give agents the task, repository, acceptance criteria, and required policy;
  do not preload another agent's reasoning as trusted evidence.
- Inspect repository areas relevant to the assigned work rather than scanning
  by default.
- Carry forward concise decisions, results, blockers, exact revisions, and
  commands needed to reproduce evidence.
- Do not carry forward full logs or transcripts unless they are the evidence
  under investigation.
- Retain context intentionally for tightly related follow-up work when doing so
  improves correctness or avoids needless rediscovery.

