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

Skip bundled `review` when the diff matches the github-workflow review skip (doc-or-comment diff).
Doc-or-comment: every changed path is in {`SPEC.md`, `SPEC.archive.md`, `.spec/check-extras.md`, `SPEC-FORMAT.md`, `README.md`, `AGENTS.md`, `designs/**`}.
Or every changed line outside that set is comment or whitespace.
`skills/**`, any script, or any other non-comment line → other diffs still run review.
Diff = `git diff <default-base>...HEAD` plus `git diff -U0 <default-base>...HEAD` for lines outside the doc set.
Other diffs: load-and-run bundled Grok `review` sub-agent on the issue-linked branch vs default base.
Scratch writes only, no repo edits.
Spawn omits capability_mode read-only.
Skip continues READY remainder (no findings).
Then READY remainder.
