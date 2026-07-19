---
name: release
description: |
  REPO-LOCAL gh release flow for this plugin repo. Bumps
  `.grok-plugin/plugin.json` version, path-scoped commits, tags
  `v<version>`, creates a GitHub release with generated notes.
  Triggers: "/release", "cut a release", "ship version", "gh release",
  "bump version and tag", "publish a release".
allowed-tools: read_file, search_replace, run_terminal_command(git *), run_terminal_command(gh *), ask_user_question
---

# release — gh release flow (REPO-LOCAL)

User-invocable REPO-LOCAL skill under `.grok/skills/` — not shipped PUBLISHED.
Owns version tag + release notes (github skill defers `gh release` here).
Body LLM-facing → telegraph.
Scope = REPO-LOCAL per the scope-set invariant.

## LOAD

1. Parse `$ARGUMENTS` for target version (semver `X.Y.Z`).
   Strip leading `v` if present.
   Missing or invalid → ask once for the version string (decision-gate); empty answer → bail.
2. Read `.grok-plugin/plugin.json` current `.version`.
   Plugin-internal file owns the version field (parametric-recipe invariant — no hardcoded repo slug, no `--repo` flag).
   File absent → bail "`.grok-plugin/plugin.json` missing".
3. `git status --porcelain` clean for release-owned paths → continue.
   Dirty tree on `.grok-plugin/plugin.json` or other staged noise → bail "working tree dirty; commit or stash first".

## GATE

ask_user_question before any mutation (decision-gate invariant).
Header `Release`.
Question surfaces current `.version` → target `v<version>` + the cwd repo.
Labels (mutually exclusive):

- `Bump, tag, push, release` — full flow
- `Bump + tag only` — local commit + tag; no push, no `gh release`
- `Cancel` — stop, no writes

Cancel → stop.
Selection drives same-turn behavior.

## BUMP

1. Rewrite `.grok-plugin/plugin.json` `.version` field → `<version>` (patch in place).
2. Path-scoped auto-commit:
   `git commit -m "release: v<version>" -- .grok-plugin/plugin.json`
   Write-ownership invariant: `-m` flags ! precede `--`; bare `git add` + bare `git commit` banned (pre-staged files never leak).
   No commit prompt.

## TAG

`git tag v<version>` on the bump commit.
Tag already exists → bail "tag `v<version>` already exists".

## PUBLISH

Only when gate selected `Bump, tag, push, release`:

1. `git push origin HEAD`
2. `git push origin v<version>`
3. `gh release create v<version> --generate-notes`
   Runs against the cwd repo — no `--repo` slug (parametric-recipe invariant).
   Notes auto-generated from commits since prior tag; github-facing body shape is gh's, not hand-steno.

Gate selected `Bump + tag only` → stop after TAG; report that push + release remain operator-side.

## OUTPUT — "Next" block

Heading `## Next`; 1–5 atomic items (one sentence each, no `Reply` prefix); positional dispatch (`run <int>` or `run /<plugin>:<cmd> [args]`).
Optional `## Hint` (≤ 3 lines) precedes when item selection needs hidden state.
Recipe ends @ commit/tag/release — slash-cmd dispatch is operator turn only (recipe-step-no-dispatch invariant).

Post-success (full publish):

```
## Next

1. /sdd:check — cascade scan after the version bump commit
2. verify the GitHub release page for `v<version>`
3. /release <next> — cut a follow-up release when ready
```

Post-success (bump + tag only):

```
## Next

1. git push origin HEAD && git push origin v<version> — publish the tag
2. gh release create v<version> --generate-notes — open the GitHub release
3. /sdd:check — cascade scan after the version bump commit
```

Cancel / bail → single item to re-invoke with a version, or stop.

## NON-GOALS

- not PUBLISHED — lives under `.grok/skills/`; never ships to consumers
- no hardcoded `owner/repo` slug, no `--repo` flag — every git/gh op runs against the cwd repo
- no SPEC.md edits — version lives in `.grok-plugin/plugin.json` only
- no issue/PR workflow — github skill owns those; this skill owns `gh release` only
- no marketplace manifest rewrite beyond the version field this recipe patches
