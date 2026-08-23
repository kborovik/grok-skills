# POST-SPEC-CHAIN — post-spec-commit chain (canonical)

Loaded by spec After OK / POST-APPLY / NON-GOALS, github WHEN / PR, build LOAD / POST-LOOP / CHAIN / OUTPUT.
Owner = github PR recipe (github-workflow + write-serialize invariants).
chain runs once.
Spec After OK stops at draft PR.
No operator wait.

## Fold-produced ids

Ids = new §T this fold + existing `.` rows that received Acceptance notes this fold.
Not whole backlog.
Not `--all`.

## Spawn

Run write-capable `/sdd:build §T.<a>,§T.<b>,…` sub-agent on those fold-produced §T ids.
Spawn prompt ! `POST-SPEC-CHILD=1`.
Token implies `--no-chain`.
Child drops READY (parent runs review next).
Child never hops check or another build.

## Fail

Child verification fail → halt to parent.
No mid-loop interactive prompt.
No SPEC.md write.
Parent `gh pr comment` steno on draft PR (§T, class, Next).
Next leads `/sdd:build`.
Class b/c also `/sdd:spec <cause>` BACKPROP.
Do not run review after child fail.

## Remainder

Then load-and-run bundled Grok `review` sub-agent on the issue-linked branch vs default base.
Scratch writes only, no repo edits.
Spawn omits capability_mode read-only.
Then READY remainder.
