# Independent Review Agent

Review must be performed by a fresh agent that did not implement the change and
does not inherit the implementation agent's conclusions as facts.

The preferred reviewer is a fresh read-only subagent in the existing ticket
workspace. Independent context and source-of-truth verification provide the
review boundary; a second Git worktree is not required. Use a separate task or
worktree only when the environment cannot provide trustworthy fresh subagent
isolation or higher-priority consuming policy explicitly requires it.

The reviewer should:

- remain read-only unless project policy explicitly defines another role;
- obtain the pull request and exact head revision from the source of truth;
- inspect status, complete diff, commits, tests, and project policy;
- verify required checks and acceptance criteria;
- reject stale, incomplete, or unverifiable evidence;
- report concrete findings and an unambiguous verdict tied to the exact head.

The review handoff must state the authoritative repository identity,
pull-request number, and full 40-character head commit supplied for its review
generation, plus the review execution identifier when the host exposes one.
That identifier may refer to a subagent or fallback task and is not required to
be a durable host-level task. The reviewer must not broaden or silently update
the generation when the pull-request head changes; it reports the mismatch so
the coordinator can dispatch a fresh reviewer for the new key.

Any new implementation commit invalidates the previous verdict. The consuming
project defines its exact verdict format, CI requirements, trust boundary, and
merge authority.

