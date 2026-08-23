# ACCEPTANCE-GATE — issue close gate (github-workflow)

Shared by `/sdd:build` verify and `github` close paths (github-workflow invariant).
Fires on issue linkage (open PR, `github-issue-N` cite, `gh issue develop` branch), not planned close trailer.
Also fires on explicit `gh issue close N` after issue-linked work.

Ordered; stop on bail:

1. **DETECT** — collect issue numbers from issue linkage (open PR, `github-issue-N` cite, `gh issue develop` branch) or explicit close target.
   Planned close trailer is not the detector.
   No issue id → gate no-op (skip).
2. **LOAD** — `gh issue view <N> --json number,title,body` against the cwd repo (no `--repo` slug; parametric-recipe invariant).
3. **PARSE** — extract bullets under `## Acceptance` (`- [ ]` / `- [x]` / `* [ ]` / `* [x]`).
   No `## Acceptance` section → **ADVISORY** (not silent-verified): emit advisory that Acceptance is absent; do not claim verified close; operator may still close after the advisory is surfaced.
4. **EVIDENCE** — collect evidence (test name, code path, or CLI probe) proving open (`[ ]`) bullets.
   Task build: check evidence for open bullets addressed by the active task; unclaimed open bullets do not block intermediate tasks.
   PR ready / MERGE / close: require evidence for every remaining open bullet.
   Checked (`[x]`) bullets need no new evidence.
5. **VERDICT**
   - missing evidence on task-claimed bullet (build) or any open bullet (PR ready / MERGE / close) → **BLOCK**: do not emit close trailer; do not `gh issue close`; do not merge a PR whose body would auto-close; emit FAIL table `bullet | missing evidence`.
     Build: verify FAIL, status stays `.`.
   - task evidence verified (build) or every open bullet proven (PR ready / MERGE / close) → **ALLOW**:
     - build: evidence sufficient (no close trailer); post Acceptance-evidence comment (step 6)
     - MERGE: add close trailer then squash; post comment if not already posted
     Optionally flip open checks to `[x]` on the issue body.
6. **COMMENT** (ALLOW only) — `gh issue comment <N> --body <steno>` with bullet → evidence map (github-facing-register → steno).

BLOCK is a verify FAIL for build (status stays `.`, no close trailer in the commit).
Close trailer MERGE-only.
Never silent-pass when Acceptance bullets exist and any stay unproven.
