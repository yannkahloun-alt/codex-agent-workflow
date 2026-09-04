# Reusable Codex Agent Workflow

This repository defines default operating rules for agent-driven software
development. A consuming project supplies its own architecture, invariants,
commands, release policy, and any explicit specializations.

## Instruction precedence

Apply instructions in this order:

1. the user's explicit request;
2. the consuming project's `AGENTS.md` and project policy;
3. this shared workflow;
4. the taskless startup default below.

Project rules may specialize this workflow, but should say so explicitly.

## Taskless startup

When started interactively without a task, do not scan the repository. Ask:

> Which issue would you like to work on?

An explicit request—whether or not it refers to an issue—bypasses this default
and should be followed directly.

## Issue handoff

A prompt such as `work on ticket #N` is sufficient authorization to carry that
issue through its full repository-defined lifecycle: inspect the issue and
policy, implement it, validate it, arrange independent review when required,
and perform the repository's normal Git and pull-request steps, including the
repository-prescribed merge after all required validation, CI, and independent
review gates pass. Do not ask the user to choose an agent role or separately
approve each routine lifecycle step. Role selection and delegation are internal
workflow decisions. The coordinator retains ownership of Git and workspace
boundaries as described in `ORCHESTRATION.md`.

This authorization is bounded by the named issue, the instruction precedence
above, and the consuming project's policies. Higher-priority consuming policy
may require separate human approval for merge, in which case the coordinator
must stop at that gate. The handoff does not authorize unrelated or destructive
work, release, deployment, publication, credential changes, or other separately
controlled actions. Requests that are not issue handoffs remain governed by
their own wording; they are not silently converted into the full issue
lifecycle.

For the normal lifecycle, a named ticket owns one implementation pull request.
Its exact head may change through CI or review fixes, but those changes create
new validation and review generations on the same pull request, not replacement
pull requests. A separate workflow-freshness maintenance pull request remains a
pre-ticket prerequisite and is not the named ticket's implementation pull
request.

## Roles

Determine the current role from the request:

- The main conversational agent normally coordinates work; see
  `ORCHESTRATION.md`.
- A fresh agent assigned a coherent implementation unit follows
  `IMPLEMENTATION_AGENT.md`.
- A fresh independent agent assigned a review—normally a read-only subagent in
  the ticket workspace—follows `REVIEW_AGENT.md`.

All roles follow `CONTEXT_MANAGEMENT.md` and `HANDOFF.md` where applicable.

## Common guarantees

- Keep unrelated work isolated.
- Preserve the consuming project's source-of-truth and safety rules.
- Establish the intended contract before changing behavior.
- Validate in proportion to risk using the project's documented gates.
- Communicate results and actionable evidence rather than entire transcripts.
- Never let this default workflow override a direct user instruction.

