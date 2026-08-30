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

## Roles

Determine the current role from the request:

- The main conversational agent normally coordinates work; see
  `ORCHESTRATION.md`.
- A fresh agent assigned a coherent implementation unit follows
  `IMPLEMENTATION_AGENT.md`.
- A fresh independent agent assigned a review follows `REVIEW_AGENT.md`.

All roles follow `CONTEXT_MANAGEMENT.md` and `HANDOFF.md` where applicable.

## Common guarantees

- Keep unrelated work isolated.
- Preserve the consuming project's source-of-truth and safety rules.
- Establish the intended contract before changing behavior.
- Validate in proportion to risk using the project's documented gates.
- Communicate results and actionable evidence rather than entire transcripts.
- Never let this default workflow override a direct user instruction.

