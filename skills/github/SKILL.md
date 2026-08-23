---
name: github
description: |
  Auto-fire gh-CLI workflow governor. Fires when an sdd skill or the operator
  runs a GitHub issue or pull-request operation — open an issue, start work on
  one, open a PR, push an issue-linked branch with an open PR, mark a PR ready,
  merge a PR, or close one unmerged. Shapes the gh workflow:
  generic issue/PR structures, per-PR issue-linked branch,
  draft PR then (spec-fold) `/sdd:build --all` write-capable sub-agent then bundled `review` sub-agent then READY remainder (no operator wait),
  operator-run code complete then review-apply then `gh pr ready`,
  squash-merge with branch cleanup, `Closes #<issue>` at merge.
  Not for plain git ops (commit, or push with no issue/PR) nor `gh release`
  — the release skill owns version tag + release notes.
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

When this skill fires, emit one telegraph line before the op recipe: `engaged sdd:github — <ISSUE|BRANCH|PR|PUSH|READY|MERGE|CLOSE>`.
Operator must see the governor (auto-fire visibility).

## WHEN — fires on a gh issue/PR op:

- new issue requested → ISSUE
- start work on an issue (issue-linked branch) → BRANCH
- open a PR → PR (`--draft`; no review; no Closes); spec-fold PR continues post-spec-commit chain
- issue-linked `git push` w/ open PR → PUSH
- operator-run issue-linked code complete → READY
- post-spec-commit remainder (review already ran as sub-agent) → READY remainder
- merge a PR → MERGE
- close a PR unmerged → CLOSE

Not: plain git ops (commit, push with no issue/PR), `gh release` (release skill owns version tag + notes).
No gh issue/PR op → no fire.

Every worked GitHub issue ! one issue-linked PR: BRANCH then PR (`--draft`).
Spec-fold PR → post-spec-commit chain runs once — owner = this PR three-step list (github-workflow invariant): `/sdd:build --all` write-capable sub-agent then bundled `review` sub-agent then READY remainder; no operator wait.
Spec After OK stops at draft PR.
Later issue-linked commits → PUSH.
After operator-run issue-linked code complete → READY.
Close → MERGE (ACCEPTANCE-GATE then add Closes then squash).

## ISSUE — `gh issue create`

`gh issue create --title "<summary>" --body <steno> [--label <class>]` against the cwd repo (no `--repo` slug per the parametric-recipe invariant).
Title = one-line summary; body = steno per the github-facing-register invariant.
Body shape: problem statement + `## Acceptance` checklist (`- [ ]` bullets).
`--label` optional.
Caller-named class in {enhancement, bug, documentation} → pass `--label <class>`.
Missing label in cwd repo → `gh label create <class>` then retry.
No fixed template scaffold beyond that heading.

## BRANCH — issue-linked branch

`gh issue develop <n> --checkout` — creates + checks out the issue-linked branch in place (native gh linkage; branch named by gh, never hand-named).
One branch per session.
Required when starting work on an issue.

## PR — `gh pr create --draft`

Fires when opening a PR (spec fold after SPEC.md commit on issue-linked branch).
Never review-at-create.
Never `Closes`/`Fixes`/`Resolves` trailer @ create.

`gh pr create --draft --title "<summary>" --body <steno>` from the linked branch.
Body = steno per the github-facing-register invariant.
Generic structure: change summary; no close trailer; no fixed template.

**After spec APPLY commit + this draft PR** (github-workflow invariant; chain runs once; owner = this PR three-step list; no operator wait):

1. run write-capable `/sdd:build --all` sub-agent (child drops READY; write-serialize exclusion).
2. load-and-run bundled Grok `review` skill as sub-agent on the issue-linked branch vs default base (not slash-dispatch `/review`; recipe-step-no-dispatch invariant).
3. READY remainder — parse findings; apply open bug + suggestion; `git push` (PUSH); `gh pr ready`.

## PUSH — `git push` issue-linked branch w/ open PR

Fires on later issue-linked commits while a PR is open.
`git push` the issue-linked branch.
Plain git push w/ no issue/PR still out of scope — no fire.

## READY — review-apply then `gh pr ready`

Required after issue-linked code complete.
Never skip.
Post-spec-commit path already ran `review` as sub-agent → start at remainder (step 2).

1. load-and-run bundled Grok `review` skill on the issue-linked branch vs default base (not slash-dispatch `/review`; recipe-step-no-dispatch invariant).
   Skip when post-spec-commit already ran `review` as sub-agent.
2. Parse findings.
3. Apply open bug + suggestion (nits listed; apply unless operator declines).
4. `git push` (PUSH).
5. `gh pr ready`.
Recipe pauses (Next: merge when approved).

## MERGE — ACCEPTANCE-GATE then add Closes then squash

Run ACCEPTANCE-GATE first.
BLOCK → do not add close trailer; do not merge.
ALLOW → add `Closes #<issue>` to PR body then `gh pr merge <n> --squash --delete-branch` (commits squashed, remote branch deleted); post evidence comment per fragment if not already posted.
ADVISORY → surface advisory, then add Closes + merge only after the advisory is stated.

`Closes #<issue>` in the PR body auto-closes the linked issue on merge → no separate `gh issue close`.

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

BLOCK → no close trailer, no merge, no `gh issue close`; emit FAIL table.
ALLOW → close path proceeds; post Acceptance-evidence comment on the issue.
ADVISORY (no `## Acceptance`) → not silent-verified; surface advisory before close.

## NON-GOALS

- no hook / runtime interception (skills-only invariant — no hooks).
- no `gh release` — the release skill (REPO-LOCAL) owns version tag + release notes.
- no hardcoded repo slug, no `--repo` flag — every op runs against the cwd repo (parametric-recipe invariant).
- never edits SPEC.md or any skill body.
- no close of a worked issue without an issue-linked PR.
