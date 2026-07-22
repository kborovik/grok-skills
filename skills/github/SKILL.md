---
name: github
description: |
  Auto-fire gh-CLI workflow governor. Fires when an sdd skill or the operator
  runs a GitHub issue or pull-request operation — open an issue, start work on
  one, open a PR, merge a PR, or close one unmerged. Shapes the gh workflow:
  generic issue/PR structures, per-PR issue-linked branch,
  squash-merge with branch cleanup, `Closes #<issue>` linkage. Not for
  plain git ops (commit, push) nor `gh release` — the release skill owns version
  tag + release notes.
allowed-tools: run_terminal_command(gh *), run_terminal_command(git *)
user-invocable: false
---

# github — gh-CLI workflow governor (auto-fire)

Auto-fire sub-skill per the sub-skill-flags invariant (`user-invocable: false`, never `disable-model-invocation` — that hides the skill from model auto-invocation).
No hook — the skills-only invariant bans runtime interception; github = LLM-applied workflow shape on each gh issue/PR op, not a wrapper.
Ships PUBLISHED to every consumer → governs the consuming repo's own gh workflow.

Trigger lives in this frontmatter description only — fires on a GitHub issue or PR op, applies the gh-CLI shape per the github-workflow invariant.
Body LLM-facing → telegraph.

Repo-agnostic per the parametric-recipe invariant: every gh + git command runs against the cwd repo — no hardcoded `owner/repo` slug, no `--repo` flag, no repo-literal path. github-facing bodies (issue, PR) = steno per the github-facing-register invariant; commit subjects = fixed templates, verbatim.

## ENGAGE LOG

When this skill fires, emit one telegraph line before the op recipe: `engaged sdd:github — <ISSUE|BRANCH|PR|MERGE|CLOSE>`.
Operator must see the governor (auto-fire visibility).

## WHEN — fires on a gh issue/PR op:

- new issue requested → ISSUE
- start work on an issue (issue-linked branch) → BRANCH
- open a PR → PR
- merge a PR → MERGE
- close a PR unmerged → CLOSE

Not: plain git ops (commit, push) with no issue/PR, `gh release` (release skill owns version tag + notes).
No gh issue/PR op → no fire.

## ISSUE — `gh issue create`

`gh issue create --title "<summary>" --body <steno>` against the cwd repo (no `--repo` slug per the parametric-recipe invariant).
Title = one-line summary; body = steno per the github-facing-register invariant.
Body shape: problem statement + `## Acceptance` checklist (`- [ ]` bullets).
No fixed template scaffold beyond that heading.

## BRANCH — issue-linked branch

`gh issue develop <n> --checkout` — creates + checks out the issue-linked branch in place (native gh linkage; branch named by gh, never hand-named).
One branch per session.
Optional on the linear solo track (see LINEAR).

## PR — `gh pr create`

`gh pr create --title "<summary>" --body <steno>` from the linked branch.
Body = steno per the github-facing-register invariant + carries `Closes #<issue>` only after ACCEPTANCE-GATE ALLOW (or ADVISORY after advisory surfaced).
Generic structure: change summary + verification line, no fixed template assumed.
Optional on the linear solo track (see LINEAR).

## MERGE — squash + branch delete

Run ACCEPTANCE-GATE first when the PR body would auto-close an issue (`Closes #N` / `Fixes #N` / `Resolves #N`).
BLOCK → do not merge.
ALLOW → `gh pr merge <n> --squash --delete-branch` (commits squashed, remote branch deleted); post evidence comment per fragment if not already posted.
ADVISORY → surface advisory, then merge only after the advisory is stated.

`Closes #<issue>` in the PR body auto-closes the linked issue on merge → no separate `gh issue close`.
Optional on the linear solo track (see LINEAR).

## CLOSE — unmerged

PR abandoned, not merged → cleanup only, no squash:

1. `gh pr close <n>` — closes the PR, no merge commit.
2. `git branch -D <branch>` — local branch cleanup.

No squash, no `--delete-branch` merge path.
The linked issue stays open — nothing merged to close it.
Unmerged PR close does not run ACCEPTANCE-GATE (issue not closed).

## ACCEPTANCE-GATE — issue close

Load `skills/_fragments/ACCEPTANCE-GATE.md` before any path that closes an issue:

- PR body or commit with `Closes #N` / `Fixes #N` / `Resolves #N`
- `gh issue close N` after issue-linked work
- linear push that would close via trailer

BLOCK → no close trailer, no merge, no `gh issue close`; emit FAIL table.
ALLOW → close path proceeds; post Acceptance-evidence comment on the issue.
ADVISORY (no `## Acceptance`) → not silent-verified; surface advisory before close.

## LINEAR — solo track (no PR)

BRANCH / PR / MERGE are optional when the operator works solo on a linear SPEC:

```
/sdd:spec github issue N   # fold issue → SPEC §V / §T
/sdd:build                 # implement (main or local branch)
# ACCEPTANCE-GATE on close trailer
git push                   # Closes #N only after ALLOW (or ADVISORY surfaced)
```

Same gate as PR merge close.
Do not require BRANCH/PR/MERGE ceremony for solo linear work.

## NON-GOALS

- no hook / runtime interception (skills-only invariant — no hooks).
- no `gh release` — the release skill (REPO-LOCAL) owns version tag + release notes.
- no hardcoded repo slug, no `--repo` flag — every op runs against the cwd repo (parametric-recipe invariant).
- never edits SPEC.md or any skill body.
