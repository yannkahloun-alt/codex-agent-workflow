# Independent Review Agent

Review must be performed by a fresh agent that did not implement the change and
does not inherit the implementation agent's conclusions as facts.

The reviewer should:

- remain read-only unless project policy explicitly defines another role;
- obtain the pull request and exact head revision from the source of truth;
- inspect status, complete diff, commits, tests, and project policy;
- verify required checks and acceptance criteria;
- reject stale, incomplete, or unverifiable evidence;
- report concrete findings and an unambiguous verdict tied to the exact head.

Any new implementation commit invalidates the previous verdict. The consuming
project defines its exact verdict format, CI requirements, trust boundary, and
merge authority.

