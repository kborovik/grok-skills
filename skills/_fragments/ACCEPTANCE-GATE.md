# ACCEPTANCE-GATE — issue close gate (github-workflow)

Shared by `/sdd:build` verify and `github` close paths (github-workflow invariant).
Fires when issue-linked work is about to close issue `N` via any of:

- commit or PR body will carry `Closes #N` / `Fixes #N` / `Resolves #N`
- explicit `gh issue close N` after issue-linked work
- linear solo push that would close via a close trailer (no PR)

Ordered; stop on bail:

1. **DETECT** — collect issue numbers from planned close trailer, PR body, commit message, §T/commit `#N` cite, or explicit close target.
   No issue id → gate no-op (skip).
2. **LOAD** — `gh issue view <N> --json number,title,body` against the cwd repo (no `--repo` slug; parametric-recipe invariant).
3. **PARSE** — extract bullets under `## Acceptance` (`- [ ]` / `- [x]` / `* [ ]` / `* [x]`).
   No `## Acceptance` section → **ADVISORY** (not silent-verified): emit advisory that Acceptance is absent; do not claim verified close; operator may still close after the advisory is surfaced.
4. **EVIDENCE** — for each open (`[ ]`) bullet: require evidence (test name, code path, or CLI probe) that proves the bullet.
   Checked (`[x]`) bullets need no new evidence.
5. **VERDICT**
   - any open bullet missing evidence → **BLOCK**: do not emit close trailer; do not `gh issue close`; do not merge a PR whose body would auto-close; emit FAIL table `bullet | missing evidence`.
   - every open bullet proven → **ALLOW**: emit close trailer / allow close or merge; post Acceptance-evidence comment (step 6); optionally flip open checks to `[x]` on the issue body.
6. **COMMENT** (ALLOW only) — `gh issue comment <N> --body <steno>` with bullet → evidence map (github-facing-register → steno).

BLOCK is a verify FAIL for build (status stays `.`, no close trailer in the commit).
Never silent-pass when Acceptance bullets exist and any stay unproven.
