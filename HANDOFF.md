# Handoffs

A handoff should let the next role act without inheriting unnecessary context.

Include:

- the task and current outcome;
- the branch, pull request, and exact revision when applicable;
- for independent review, the repository identity, pull-request number, full
  40-character head commit, stable review-task identity, and any same-generation
  duplicate identities and conservatively aggregated outcome;
- acceptance criteria satisfied or still open;
- validation performed and its result;
- post-merge cleanup performed, skipped, or failed, including preserved local
  resources and the reason when cleanup is incomplete;
- material decisions, risks, blockers, and required next action.

Exclude routine command noise, full transcripts, and conclusions the next role
must independently verify. Review handoffs must preserve reviewer independence.

