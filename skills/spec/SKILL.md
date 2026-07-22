---
name: spec
description: |
  Sole semantic author of SPEC.md @ repo root — create, amend, fold shapes,
  or backprop bugs (§T status-flip → build, archive → condense, §V renumber →
  reorganize; those carve-outs not authoring paths).
  Triggers when user asks to write spec, start new spec, distill spec from
  code, add invariants, amend a section, record a bug, or fold a GitHub
  issue. Common phrasings: "write the spec for...", "new spec",
  "distill spec from code", "spec this idea", "import existing repo",
  "pull invariants out of code", "this bug keeps biting", "post-mortem on Y",
  "fold-shape", "github issue N".
allowed-tools: ask_user_question, read_file, search_replace, write, grep, run_terminal_command(git *), run_terminal_command(grep *), run_terminal_command(gh *), spawn_subagent, skill
---

# spec — spec mutator

`telegraph` skill applies to all writes here.

## DISPATCH

**Step 0 (precondition):** `git status --porcelain SPEC.md` empty → continue; else bail w/ "SPEC.md has uncommitted changes; commit or stash first" (auto-commit assumes clean baseline; porcelain catches staged + untracked, which `git diff --quiet` misses).

**Step 1 (fold-in shortcut):** any of:
- `$ARGUMENTS` is `fold-shape` / `fold the shape plan` / free-form fold of current shape plan → FOLD-IN from approved session plan (shape skill).
- `$ARGUMENTS` matches `designs/*.md`, file exists → FOLD-IN from legacy design draft.
- `$ARGUMENTS` matches `github issue <N>` / free-form "fold issue N" → FOLD-IN from GitHub issue (see FOLD-IN — github issue).
SPEC.md must exist @ repo root else bail w/ "fold-in needs SPEC.md; init via NEW or DISTILL first".
Else → gate.

Engage `sdd:socratic` gate w/ `$ARGUMENTS` as intent.
Single-question loop until convergence triple matches one mode:

- **NEW** — goal + first-principle-asked + (≥ 1 invariant or ≥ 1 task)
- **DISTILL** — explicit "build from code" intent (gate exits ≤ 1 turn — walks repo, no interrogation)
- **BACKPROP** — symptom + surface + recurrence-class
- **AMEND** — §-target + delta

SPEC.md presence is the only branch (mode is gate byproduct, not user-typed prefix):

1. no SPEC.md @ repo root → gate restricted to {NEW, DISTILL}.
2. SPEC.md exists → gate ranges over {BACKPROP, AMEND, NEW}; NEW rare → require explicit re-init confirmation before overwrite.

Post-convergence → run mode procedure below.
Concrete first-turn input → gate passes ≤ 1 turn (zero-friction); vague → dialogue until convergence.
No skip flag, no prefix back-doors.

## NEW — idea → spec

Input: user idea.

1. Goal (1 line, telegraph) → §G.
2. Constraints stated or implied → §C.
3. External surfaces named → §I.
4. Initial invariants → §V (numbered V<n>).
   Gate probes first-principle (foundational claim); user may decline → converge on derived invariants only.
   Late first-principle → AMEND §V (only late-entry path, not second authoring path).
5. Goal → ordered tasks → §T pipe table, all status `.`, ids T<n>.
6. §B header row only (`id|date|cause|fix`).

→ APPLY.

## DISTILL — code → spec

Walk repo.
Produce §G (infer from README/package.json/main entry), §C (infer from stack), §I (enumerate public APIs/CLIs/configs), §V (derive from tests + assertions), §T (one task per known TODO or missing test), §B (empty).
Flag uncertain items w/ `?` so user can confirm.

→ APPLY.

**Second pass (required Next):** post-APPLY Next always includes `/sdd:check` then `/sdd:spec` confirm `?`-flagged rows (named AMEND batch).
DISTILL is not one-shot truth — brownfield needs check noise + confirmation.

## BACKPROP — bug → §B + §V

Input: gate triple (symptom + surface + recurrence-class).

1. Parse bug.
2. Find root cause (read code).
3. New invariant would catch recurrence? yes → draft `V<next>`.
4. Append §B row `B<next>|<date>|<cause>|<fix>` — fix cell `V<N>` when step 3 drafted, else `-` (per SPEC-FORMAT §B fix grammar).
5. Drafted → append invariant to §V.
6. Fix changes behavior → add/patch §T rows.

→ APPLY.

Rule: every bug → §B entry.
Invariant optional but preferred.

**Resume card (post-commit):** write `.spec/backprop-handoff.json` with `{B, V, T, test_name_hint}` for the new rows (REPO-LOCAL cache, not design truth).
Next item #1 = concrete `/sdd:build §T.<n>` (never bare `--next` only).
Build LOAD consumes + deletes the card on close.

## AMEND — targeted edit

Input: gate §-target + delta.

**Resolve body file** via script when possible: `emit-v-slices --dirty V<n>` resolves check-extras stubs (extras-hook + single-load).
Fallback hand-resolve: SPEC.md stub `→ .spec/check-extras.md §V<n>` → body file `.spec/check-extras.md`; else SPEC.md. §B/§G/§C/§I/§T always SPEC.md.

### Micro-AMEND (trivial path)

Fires when all hold: single § target; delta ≤ one cell / one line; no new §V row; no sweep-§T.
Still show preview.
ask_user_question options lead with `Apply` (recommended); skip fold-first (no new row).
Structural multi-row / new §V / FOLD-IN / BACKPROP / NEW / DISTILL keep full APPLY gate.

### Full AMEND

Read target § from resolved body file.
Show current in steno if target in {§V, §B}; telegraph otherwise.
Ask user what changes when delta incomplete.

→ APPLY.

Never silently rewrite §s user did not name.

## FOLD-IN — shape plan, design draft, or github issue → §V / §T amend

Input (any of):
- approved Grok Plan mode body from `/sdd:shape` (`fold-shape`)
- legacy `designs/<slug>.md`
- GitHub issue `N` via `/sdd:spec github issue N` (or free-form fold of issue N)

No socratic gate — shape/design already enforced Open-Questions-empty (or park) pre-approve; issue fold is operator-named target.
Multi-target: one shape or issue may propose new §V / §T / §I / §B rows in one apply.

1. Read plan, draft, or issue; parse proposed amendments.
2. Draft each in telegraph (target §s + delta text).

→ APPLY.

Rule: fold-in mutates SPEC.md only.
Plan file stays in session; legacy design file stays in working tree (no auto `rm`).
Provenance: slug, "fold-shape", or `github-issue-<N>` in commit msg.

### FOLD-IN — github issue

Ordered:

1. **LOAD** — `gh issue view <N> --json number,title,body,labels` against the cwd repo (no `--repo` slug).
2. **MAP** — problem / body prose → candidate §V / §T rows (telegraph); title → short task goal when one §T fits.
3. **Acceptance** — if body has `## Acceptance` checklist, fold open bullets into §T goals or task notes so `/sdd:build` can prove them; if no `## Acceptance` → surface **ADVISORY** in the preview (not silent-verified; github-workflow invariant) and continue fold without inventing bullets.
4. **Link** — record issue number in commit body (`github-issue-<N>`); do not auto-close the issue from this fold.

Linear solo track after fold: `/sdd:build` → push with close trailer only after ACCEPTANCE-GATE (see `skills/_fragments/ACCEPTANCE-GATE.md` + github skill LINEAR).

## APPLY (all modes, post-delta)

Five steps in order; audits fire on condition, not mode authorship.
All audits run pre-show-user, mechanical pattern-match not LLM-judgment per mechanical-not-LLM-judgment invariant.

**Step 0 — write-time prune** (delta-rewrite stage, ordered ahead of audit table so every audit sees final-form delta):

- delta patches pre-existing §V row → §V-row residue prune per WRITE-TIME PRUNE §.
- delta adds or rewrites §B `cause` cell → one-line trim per WRITE-TIME PRUNE §.
- pruned content → commit-msg body (step 4); step 3 shows post-prune form.

**Step 1 — audit table** (on-fail column names owning § — bail strings + sub-recipe detail live there only):

```
audit | fires when delta is | on fail
sweep-scope | contains sweep-§T row (§V-violation remediation) | bail → SWEEP-§T SCOPE AUDIT
pinned-cite | touches PUBLISHED (a) or SPEC.md narrative (b) | bail → PINNED-CITE AUDIT, matching sub-recipe
next-block  | touches user-typeable SKILL.md | bail → NEXT-BLOCK-SECTION AUDIT
fold-first  | adds §V row to pre-existing §V section, mode not FOLD-IN | ask_user_question gate → FOLD-FIRST AUDIT
```

pinned-cite (a) + next-block rows structurally no-op while step 4 writes SPEC.md only — retained defensive (fire only if future mode widens write set).

Table uses named-invariant + placeholder cite form only (`per <named> invariant`, `§V.<n>`) — `skills/**` in PUBLISHED where pinned §-digit cites banned per sub-recipe (a); body pinned-cite count is 0, stays 0.

**Step 2 — render-split**: §V + §B content rows → steno per steno skill (audience: user reviewing proposal); all else → telegraph (§T/§I pipe forms already legible, §G/§C targets, header-only §B row).

**Step 3 — show-user**: render diff preview; await user OK.

**Step 4 — write + commit**: on OK → write delta to its resolved body file(s) (telegraph) + auto-commit path-scoped `git commit -m <subject> [-m <body>] -- <body-file(s)>` (write-ownership invariant — scopes to the owned file set, pre-staged files never leak).
Body file(s) = SPEC.md every mode + target, except a stub-redirected §V AMEND → `.spec/check-extras.md` per AMEND § resolution + extras-hook invariant (the SPEC.md stub row stays untouched, so check-extras.md is the sole path-scope; mixed delta touching both an inline §V/other § and a stub-redirected §V → path list = the union). `-m` flags ! precede `--` — message tokens after `--` parse as pathspecs, commit fails; no commit prompt (uniform every mode).
Msg per mode:

```
NEW      → init SPEC.md (V<1>..V<n>, T<1>..T<m>)
DISTILL  → init SPEC.md from code
BACKPROP → backprop §B.<n>(+) + §V.<N>(+): <one-line cause>   (trimmed forensics → msg body, from step 0)
AMEND    → amend §<S>.<n>(+): <one-line>                       (pruned history → msg body, from step 0)
FOLD-IN  → fold-in §V.<n>(+) and §T.<n>(+): <slug|fold-shape>  (omit absent §s)
```

**Re-entry**: any stage rewriting delta after step 0 — concretely fold-first's fold-into reroute (new §V row → existing-row amend) — re-enters APPLY @ step 0; rewritten delta newly satisfies §V-row prune and prior audits saw a delta that no longer exists.

APPLY ends @ commit. `## POST-APPLY` fires after, unchanged.

## SWEEP-§T SCOPE AUDIT

Every sweep-§T row (remediating §V-class violation) in delta ! task line declares scope as grep pattern or vocab table per sweep-§T-scope invariant; named-procedure or named-site list not accepted.
No pattern → bail w/ `sweep §T row scope ! grep pattern per sweep-§T scope rule`; user supplies pattern, retry.

## PINNED-CITE AUDIT

**Sub-recipe (a) — PUBLISHED-scope ban**: grep `§[VTB]\.[0-9]+` in delta touching PUBLISHED scope (per scope-set invariant).
Match → bail `pinned §-cite not allowed in PUBLISHED — use placeholder form (§V.<n>) or inline rule embedding` until rewrite.
No PUBLISHED delta → no-op.

**Sub-recipe (b) — SPEC.md-narrative §V resolution**: grep `§V\.[0-9]+` in delta touching SPEC.md narrative (§G/§C/§I/§V/§T/§B body).
Pre-filter backtick-wrapped tokens `grep -v -E '`[^`]*§V\.[0-9]+[^`]*`'` (invert scan, grep -v -E per tooling-preference invariant) — historical-quote form per verbatim invariant exempt.
Each surviving match resolves against current SPEC.md §V row set (parse `^V[0-9]+:` openers).
Unresolved → bail `stale §V.<n> cite in delta — row absent (likely folded); backtick-wrap historical or substitute live row` until rewrite.
No narrative delta → no-op.

(a) defends against PUBLISHED-touching deltas via spec-cmd flow — `/sdd:spec` normally writes SPEC.md only so typically no-op. (b) closes post-fold authoring gap — fold-time sweep (condense prong-1) substitutes existing cites @ fold-commit; new bare cites to folded id authored post-fold bypass until next `/sdd:check`.
Pattern-match catches what LLM prose-review missed (see §B history).

## NEXT-BLOCK-SECTION AUDIT

Audits touched user-typeable `<plugin>/skills/<n>/SKILL.md` per skills-only architecture invariant.
User-typeable = frontmatter lacks `disable-model-invocation: true` and `user-invocable: false` (skill dir surfaces as `/<plugin>:<n>` natively unless opted out).

Each touched file in post-amend tree:
1. Grep `^(disable-model-invocation|user-invocable):\s*` over frontmatter block.
2. Opt-out match → no-op for this file (auto-fire or programmatic-only, no slash-cmd surface).
3. Else grep `## OUTPUT — "Next" block` heading in post-amend file.
   Match → no-op; else bail `<skill> SKILL.md lacks Next-block section per /<plugin>:<n> response-shape contract` until author adds §.

Defends against new user-typeable skill bodies (or cross-plugin migrations) omitting Next-block contract sister skills carry — V20-class runtime rule governs response shape, not authoring-time presence (see §B history).
Structurally no-op while APPLY step 4 writes SPEC.md only (mirrors pinned-cite (a) posture).

## FOLD-FIRST AUDIT

Per fold-first authoring invariant (§V.<n>).
Mechanical decision-gate.

Each proposed new §V row in delta:

1. Closest existing §V row by topic — heuristic: shared scope tokens (e.g. `PUBLISHED`, `GITHUB-FACING`, `SPEC-ADJACENT`), shared procedure ref (e.g. `/sdd:spec`, `/sdd:check`), shared verb pattern (e.g. `audit`, `auto-fire`, `gate`).
   None identifiable → step 3 w/ "no fold candidate" note.
2. ask_user_question per decision-gate invariant (§V.<n>):
   - **question**: `New §V row proposed: <delta one-line>. Closest existing row §V.<m>: <m-summary>. Fold into existing or split as new row?`
   - **header**: `Fold-first`
   - **options** (3, mutually exclusive, label = action):
     - `Fold into §V.<m>` → reroute delta as §V.<m> amend (inline addition to existing row).
     - `New row (cite §B recurrence-class)` → proceed; requires §B.<k> cite in delta justifying split (audit greps `§B\.[0-9]+` post-selection).
     - `New row (orthogonal concept)` → proceed; user-typed orthogonal-concept declaration recorded in commit msg post-selection.
3. Fold-into → re-render delta as §V.<m> amend, re-enter APPLY @ step 0 per Re-entry rule (re-prune + re-audit — not jump to show-user); new-row branches → record justification, proceed to show-user.

Defends against premature-split class — small audit or enforcement-meta additions creating new §V row when inline amend sufficed. "mirrors §V.<n>" alone insufficient justification per fold-first authoring invariant.

## WRITE-TIME PRUNE

Per freshness-contract invariant (SPEC.md is clean current design; history in commit log + archive, not inlined).
Auto-rewrites delta → clean current state; pruned history → auto-commit msg body (recoverable via code + `git log`).
Show-user diff displays post-prune row — prune reviewed, not blind.

**§V-row delta prune** (delta patches pre-existing §V row): strip inlined-history residue.
Pattern set (single source per freshness-contract invariant — shared w/ /sdd:check history-residue audit + token-budget condense body-trim prong):

- amendment-counter `(∆)` markers → drop (clean current state carries no edit tally).
- dated-retirement `retired YYYY-MM-DD` clause in live row → drop (wholesale-retired row is reorganize archival job, not amend residue).
- supersession-narration (`pre-amend …`, `prior … retired/dropped/superseded`) → drop.
- `Closes §B.<x>` standalone narration → fold to `(closes §B.<x>)` suffix.

Pre-filters (exempt, not pruned): backtick-wrapped tokens per verbatim-preservation invariant (code-context pattern-defs + quoted historical refs — §V row whose subject is a retirement rule not self-flag); cite-modifier `§V.<n>(∆)` (∆-on-citation marks amended cross-ref, differs ∆-on-retired-value).
Stripped content → commit-msg body per APPLY step 4.

**§B cause trim** (delta adds/rewrites §B `cause` cell): auto-trim → one-line bug-class description; multi-line forensics (repro transcript, root-cause walk, sha lineage) → commit-msg body per APPLY step 4.
Preview shows trimmed form, not raw forensics.

§T body not pruned here — /sdd:build flips status cell only per status-flip invariant so §T rows authored one-line @ creation (NEW + BACKPROP drafts); pre-existing §T residue owned by token-budget condense body-trim prong + /sdd:check oversized-cell advisory backstop.

## POST-APPLY

Default: surface `/sdd:check` as Next item #1 (cascade over just-applied delta).
Exceptions:
- **BACKPROP** → item #1 = concrete `/sdd:build §T.<n>` (resume card); item #2 = `/sdd:check`.
- **DISTILL** → item #1 = `/sdd:check`; item #2 = `/sdd:spec` confirm `?`-flagged rows.
- Green-path: not default-chained from spec (operator or explicit Next).

Not silent commit-then-done.

## OUTPUT RULES

Defer to `${GROK_PLUGIN_ROOT}/SPEC-FORMAT.md` — row shape, section catalog, citation forms, header conventions.

## MECHANIZE

Load `${GROK_PLUGIN_ROOT}/skills/_fragments/MECHANIZE.md`.
Run probe.
Emit Next item per fragment.

## OUTPUT — "Next" block

Per `skills/_fragments/NEXT.md`.
Show-user → apply + revise lead.
Post-commit → POST-APPLY leads (BACKPROP concrete build; DISTILL check + confirm-?; else check then build).

## NON-GOALS

- Writes serialize on main thread; reads delegable to sub-agents.
- No dashboards.
  Cache files (`.spec/backprop-handoff.json`, check memo) are not design truth.
- No auto-build after non-BACKPROP spec (BACKPROP Next names concrete §T).
