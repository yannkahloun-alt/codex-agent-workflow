# Handoffs

A handoff should let the next role act without inheriting unnecessary context.

Include:

- the task and current outcome;
- the branch, the ticket's single implementation pull-request number and state,
  and exact revision when applicable; state whether the authoritative CI
  generation for that exact head has passed, failed, is pending, or is
  unavailable;
- for independent review, the repository identity, pull-request number, full
  40-character head commit, verdict or findings, review execution identity when
  available, and any known same-generation duplicate executions with their
  conservatively aggregated outcome;
- acceptance criteria satisfied or still open;
- validation performed and its result;
- post-merge cleanup performed, skipped, or failed, including preserved local
  resources and the reason when cleanup is incomplete;
- material decisions, risks, blockers, and required next action.

When an implementation pull request has been created, retain its number as the
named ticket's lifecycle identity. Do not replace it after a changed head, CI
failure, review finding, restart, or lost review state. If it was closed,
reopen it when safely possible; if it cannot continue, report the concrete
limitation rather than creating another implementation pull request.

Exclude routine command noise, full transcripts, and conclusions the next role
must independently verify. Review handoffs must preserve reviewer independence.
An ephemeral subagent does not need a durable host-level task identity. If its
exact-head verdict cannot be recovered, the next coordinator must obtain a
fresh exact-head review rather than infer approval.

