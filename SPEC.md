# SPEC — sdd plugin

## §G GOAL

LLM writes code faster than humans read → standards + logic drift unchecked; counter: one telegraph SPEC.md authoritative over code; plugin skills keep code shape + component contracts aligned over time.

## §C CONSTRAINTS

- installable Grok skills plugin; root-source plugin `sdd` (`.grok-plugin/plugin.json`, optional `.grok-plugin/marketplace.json` source `./`)
- skills-only: every surface = `skills/<name>/SKILL.md`; no commands/ tree, no hooks
- `scripts/check-mechanical.py` single-file, stdlib-only python3
- no orchestrator, no swarm: main Grok agent executes; sub-agents read-only
- no state beyond SPEC.md + git + REPO-LOCAL `.spec/` cache

## §I INTERFACES

external surface — what operator + consuming repo see.

- shape: `/sdd:shape <topic>` → Grok Plan mode propose-critique → approved plan; fold via `/sdd:spec fold-shape` (not bundled `/design`; no default `designs/` write)
- spec: `/sdd:spec <intent>` → socratic gate → SPEC.md delta preview → apply + auto-commit; fold-shape + micro-AMEND paths
- build: `/sdd:build [§T.n|--next|--all|--no-chain]` → plan → edit → verify → flip §T `.`→`x` + commit; green-path chain default-on → check
- check: `/sdd:check [--full|--no-chain]` → thin recipe + script; read-only REPORT + Next; clean chain → build --next
- explain: `/sdd:explain [§-cite|--next]` → prose expansion w/ cited siblings, zero writes
- condense: `/sdd:condense` → six-prong token sweep, single atomic commit
- reorganize: `/sdd:reorganize [--taxonomy-only]` → §V cluster + renumber + cite sweep, single atomic commit
- script: `python3 ${GROK_PLUGIN_ROOT}/scripts/check-mechanical.py <mode>` → pipe-table `id|verdict|evidence`; modes: audit, write-memo, fix-sembr, emit-v-slices, emit-superseded, emit-fold-seeds, emit-v-weights, emit-row-ids, emit-overview, emit-token-estimate, emit-residue, emit-check-agent-prompt, --self-test
- fragments: `skills/_fragments/*` shared recipe text (MECHANIZE, NEXT, CHAIN, CHECK-RECIPE, …) — not slash surfaces
- format: `SPEC-FORMAT.md` → row shape + section catalog contract; loaded by spec, check, condense, reorganize

## §V INVARIANTS

numbered, testable, named; each ! hold. ids clustered by topic; gaps = cluster spans + closure history.

V1: spec-adjacent-register — SPEC.md, `skills/**/SKILL.md`, SPEC-FORMAT.md, spec-referencing prose ! telegraph per telegraph skill; /sdd:explain decodes on demand.
V2: github-facing-register — README, issues, PRs, commit-msg bodies ! steno per steno skill; commit subjects = per-skill fixed templates, verbatim.
V3: verbatim-preservation — → `.spec/check-extras.md §V3`
V4: symbol-set — → `.spec/check-extras.md §V4`
V10: sole-source-of-truth — SPEC.md @ repo root is sole live spec; no docs/ tree, no sidecars; SPEC.archive.md sibling carries immutable archived rows only.
V11: shape-semantics-split — SPEC-FORMAT.md binds row shape + section catalog + citation grammar; §V rows bind semantics + enforcement; neither restates the other.
V12: monotonic-numbering — V/T/B ids strictly increasing in section order; gaps OK, reuse banned; sole renumber path = /sdd:reorganize (map append + cite sweep, same commit).
V13: cite-resolution — every cite ! resolve: `cites` tokens → live/archived V/T/B row or live §I kind, `fix` tokens → §V row, free-text `§<S>.<n>` → §<S> row; renumber sweeps citers same commit.
V14: pinned-cite-ban — PUBLISHED bodies ! placeholder (`§V.<n>`) or named-invariant form, never pinned §-digit cites; SPEC.md-narrative + REPO-LOCAL pinned cites ! resolve live.
V15: renumber-chain-walk — `.spec/spec-renumber-map.json` append-only; historical id resolves newest-first to live id or `archive` sentinel (→ SPEC.archive.md §V.retired block, never live row).
V16: archive-semantics — archived §T/§B + retired §V rows migrate verbatim to SPEC.archive.md w/ per-section markers per SPEC-FORMAT; archived rows stay cite-resolvable, never edited.
V20: write-ownership — → `.spec/check-extras.md §V20`
V21: write-serialize — SPEC.md + code writes serialize main-thread; reads delegable to read-only sub-agents.
V22: recipe-step-no-dispatch — slash-cmd dispatch = operator turn only; recipes end @ commit + Next block; exclusions: (1) /sdd:build verify-fail → mid-loop spec BACKPROP; (2) green-path chain default-on per `skills/_fragments/CHAIN.md` — build pass → check cascade same turn; check clean + pending `.` §T → build --next same turn; `--no-chain` disables; dirty check never auto-remedy.
V23: decision-gate — → `.spec/check-extras.md §V23`
V24: response-shape — → `.spec/check-extras.md §V24`
V25: socratic-gate — /sdd:spec mode {NEW, DISTILL, BACKPROP, AMEND, FOLD-IN} = gate byproduct of free-form `$ARGUMENTS`; no mode prefixes, no skip flags; concrete intent converges ≤ 1 turn.
V26: first-principle-probe — NEW mode fires foundational-claim question exactly once, declinable; `first-principle-asked` recorded regardless of answer.
V27: backprop-protocol — → `.spec/check-extras.md §V27`
V28: freshness-contract — → `.spec/check-extras.md §V28`
V29: fold-first — new §V row vs amend of closest existing row ! operator gate; split justification = §B recurrence cite or declared orthogonal concept; "mirrors existing row" alone insufficient.
V30: sweep-scope — sweep-class §T row ! declare scope as grep pattern or vocab table; named-procedure + named-site lists rejected.
V31: shape-lifecycle — /sdd:shape uses Grok Plan mode (enter_plan_mode → plan.md → exit_plan_mode); primary artifact = session plan, not `designs/`; fold-in via `/sdd:spec fold-shape` mutates SPEC.md only; legacy `designs/*.md` still folds; optional `--export designs/<slug>.md` only on operator ask; not bundled `/design`.
V40: mechanical-realization — → `.spec/check-extras.md §V40`
V41: parametric-recipe — → `.spec/check-extras.md §V41`
V42: scope-set — → `.spec/check-extras.md §V42`
V43: drift-verdict-vocab — → `.spec/check-extras.md §V43`
V44: memo — → `.spec/check-extras.md §V44`
V45: scope-feed — → `.spec/check-extras.md §V45`
V46: batch — → `.spec/check-extras.md §V46`
V47: check-dispatch — /sdd:check accepts bare (memo-driven) or `--full` (drop memo, re-classify all) only; other args bail.
V48: token-budget — → `.spec/check-extras.md §V48`
V49: extras-hook — → `.spec/check-extras.md §V49`
V60: skills-only — every surface = `skills/<name>/SKILL.md` dispatched natively as `/<plugin>:<name>`; no commands/ tree, no hooks, no orchestrator.
V61: sub-skill-flags — → `.spec/check-extras.md §V61`
V62: tooling-preference — → `.spec/check-extras.md §V62`
V63: plugin-shape — → `.spec/check-extras.md §V63`
V64: single-load — → `.spec/check-extras.md §V64`
V65: monitor-protocol — → `.spec/check-extras.md §V65`
V66: mechanize-scan — → `.spec/check-extras.md §V66`
V67: human-clarity — → `.spec/check-extras.md §V67`
V68: table-use — → `.spec/check-extras.md §V68`
V69: github-workflow — → `.spec/check-extras.md §V69`
V70: sembr — repo `.md` prose source lines ! semantic line breaks (sembr.org): one sentence per line, clause-boundary break OK; source-format only — rendered output unchanged; scope: README.md, AGENTS.md, `designs/*.md`, `skills/**/SKILL.md`, `skills/_fragments/**`; exempt: pipe-row files (SPEC.md, SPEC.archive.md, `.spec/check-extras.md`), fenced blocks, `|`-tables, frontmatter; GitHub issue/PR/comment bodies out of scope (GFM renders single newline as hard break); register-orthogonal — sibling to table-use.
V71: consumer-core-profile — core loop = shape? → spec → build → check → explain; maintenance = condense/reorganize; plugin-meta (UPSTREAM-FR fragment, monitor issue path for plugin repo) loads only when cwd is plugin `.repository` or operator targets plugin; consumer non-plugin repos skip UPSTREAM-FR body weight.
V72: shared-fragments — canonical cross-skill recipe text lives in `skills/_fragments/` (MECHANIZE, NEXT, PROGRESS, PATH-SCOPED-COMMIT, CHAIN, CHECK-RECIPE, CHECK-AGENT-PROMPT, UPSTREAM-FR); user-invocable skills point, never copy; `_fragments` is not a slash surface.
V73: backprop-resume-card — BACKPROP APPLY writes `.spec/backprop-handoff.json` `{B,V,T,test_name_hint}`; Next leads with concrete `/sdd:build §T.n`; build LOAD consumes + deletes on close; card is resume pointer, not design truth.
V74: micro-amend — single-§ single-line AMEND with no new §V row uses shortened APPLY gate (preview + Apply-led ask_user_question; skip fold-first); structural modes keep full gate.
V75: auto-fire-engage-log — auto-fire sub-skills (telegraph, steno, monitor, github) emit one telegraph `engaged sdd:<name>` line when they fire so the operator sees the governor.
V76: thin-check — check SKILL.md is phase + script orchestration only; long recipe detail in `_fragments/CHECK-RECIPE.md`; sub-agent prompt from `emit-check-agent-prompt` / CHECK-AGENT-PROMPT fragment.

## §T TASKS

id|status|task|cites
T1|x|add CI: script --self-test + audit dirty-verdict gate on push/PR|V40,V62
T2|x|bump plugin version 1.1.0 + sync manifest description|-
T3|x|create REPO-LOCAL release skill (gh release flow)|V24,V41,V42
T4|x|add script emit-overview mode; check LOAD uses it|V64,V40
T5|x|add PROGRESS checklist to multi-phase recipes|V24,V62
T6|x|script batch-advisory row; check consumes it|V46,V40
T7|x|init skills/monitor auto-fire deviation capture|V65,V61
T8|x|script: admit MATCH clean verdict on §I rows|V43,V40
T9|x|script write-memo --from-audit; check drops hand-merge|V44,V40
T10|x|script write-memo dirty → exit 1; self-test exit codes|V44
T11|x|sweep frontmatter Bash grants: drop zero-use; pin script python3|V62
T12|x|open upstream FR: env-var expansion in allowed-tools|V62
T13|x|monitor: assert gh --repo == manifest .repository pre-write|V65,B11
T14|x|sweep skill auto-commits to path-scoped git commit -- <paths>|V20,B12
T15|x|sweep body rg → builtin Grep / grep -v -E invert|V62,V3
T16|x|copy canonical MECHANIZE block into user-invocable skills|V66,V30
T17|x|monitor: add mechanization-candidate dispatch path|V65,V66
T18|x|pin git commit -m before -- in path-scoped recipes|V20,V30
T19|x|script: MECHANIZE block byte-identity audit + self-test|V66,V40
T20|x|script: ban slash-dispatch of auto-fire sub-skills|V24,V40,B14
T21|x|reframe backprop frontmatter as caller-engaged sub-skill|V24,V61,B14
T22|x|sweep allowed-tools: drop zero-body-use Skill grants|V62
T23|x|read-only skills: add disallowed-tools Edit, Write|V20,V62
T24|x|drop remaining zero-body-use grants (backprop/design/explain)|V62
T25|x|drop residual Glob/Skill zero-use grants after T24|V62,B17
T26|x|script: zero-body-use grant audit + self-test|V62,V40,B17
T27|x|script emit-token-estimate mode for budget baseline|V40,V48
T28|x|rename surface compact → condense across plugin|V60
T29|x|sweep 25k → 20k token-budget threshold in docs|V48
T30|x|socratic CONVERGENCE: AskUserQuestion gate not prose or|V23,B18
T31|x|sweep human-facing surfaces: spell out → ≥ ≤ & ~|V4,V67,B19
T32|x|script: naked-symbol audit on human-facing surfaces|V4,V67,V40,B19
T33|x|fix manifest description token-cut figure 30% → 40%|B20
T34|x|sweep steno + design bodies per symbol-set + human-clarity|V4,V67,B21
T35|x|author root clarity carrier (CLAUDE.md era) + marker block|V67,B21
T36|x|script: clarity-carrier presence + marker-block audit|V67,V40,B21
T37|x|sweep README banned idiom/metaphor → literal phrasing|V67,B22
T38|x|script: banned-idiom audit on human-facing surfaces|V67,V40,B22
T39|x|sweep prose pipe-tables → bullet lists (table-use)|V68,V3
T40|x|init skills/github passive gh-CLI workflow governor|V69,V61,V2,V41
T41|x|spec AMEND/APPLY: write §V body file not always SPEC.md|V49,V20,B23
T42|x|github skill: drop git-worktree steps per workflow amend|V69
T43|x|script: emit scope v-path-dirty advisory for §V path tokens|V45,V40
T44|x|move spec-owned files .claude/ → .spec/|V15,V41,V42,V44,V49
T45|x|re-anchor steno + clarity carrier on simple technical language|V67,V2
T46|x|sweep prose → semantic line breaks (sembr)|V70
T47|x|script: sembr multi-sentence-line advisory + self-test|V70,V40
T48|x|script: fix-sembr mode rewrite multi-sentence lines|V70,V40
T49|x|build: auto-send Grok Build feedback for upstream-FR §T rows|V62
T50|x|script emit-residue mode (section|id|pattern|line; share HR_*/pre-filter/oversized w/ audit_history_residue; self-test emit=audit); condense prong 4 consumes table (empty → skip)|V28,V40,V48
T51|x|MECHANIZE de-dupe: fragment pointer + script pointer audit (retire byte-identity)|V66,V72,V40
T52|x|thin check recipe + emit-check-agent-prompt + CHECK-RECIPE fragment|V76,V64,V40
T53|x|emit-v-slices resolve check-extras stubs|V49,V64,V40
T54|x|backprop resume card + concrete Next §T id|V73,V27,V24
T55|x|green-path chain default-on + --no-chain|V22,V24
T56|x|consumer-core-profile + UPSTREAM-FR fragment gate|V71,V62
T57|x|rename design → shape; Plan mode artifact; fold-shape|V31,V72
T58|x|micro-AMEND path for trivial amends|V74,V23
T59|x|medium: DISTILL 2nd pass, explain-first remedies, engage log, socratic concrete escape, reorganize advisory, shared fragments|V25,V75,V72,V76

## §B BUGS

id|date|cause|fix
B1|2026-06-11|sub-skill flags inverted: disable-model-invocation hid auto-fire from Skill tool|V61
B2|2026-06-11|marketplace root source ./ lstrip-emptied → plugin dropped from PUBLISHED scope|V63
B3|2026-06-11|§I id derivation hardcoded dev-repo slash-bullets → zero ids in consumers|V41
B4|2026-06-11|backprop promised one commit; spec APPLY + build committed separately|V27
B5|2026-06-11|compression claim drift: measured ~30% vs legacy quarter/4x in README|-
B6|2026-06-11|check LOAD whole-file Read + emit-v-slices double-loaded SPEC.md|V64
B7|2026-06-11|batch narrow-scope override used LLM eyeballed file count as proxy|V46
B8|2026-06-11|MATCH clean §I verdict missing from memo vocab → silent HOLD remap|V43
B9|2026-06-11|dirty write-memo demanded full table then exit 0 → unusable as CI gate|V44
B10|2026-06-11|allowed-tools pin failed: CLAUDE_PLUGIN_ROOT no-expand → broad Bash grants|V62
B11|2026-06-11|monitor gh-write hit anthropics/claude-code not plugin .repository|V65
B12|2026-06-11|check .gitignore guard swept into next bare git commit of SPEC.md|V20
B13|2026-06-12|path-scoped commit put -m after -- → pathspec abort|V20
B14|2026-06-13|Next-block named /sdd:backprop as user dispatch; real route /sdd:spec|V24
B15|2026-06-13|allowed-tools cast as access-restriction; real denial = disallowed-tools|V62
B16|2026-06-13|grant sweep scoped to 3 skills; left Skill/Glob zero-use elsewhere|V62
B17|2026-06-13|T24 grant sweep under-covered Glob; no mechanical V62 audit yet|V62
B18|2026-06-17|socratic escape used prose or-keep-going; V23 needs AskUserQuestion|V23
B19|2026-06-18|README + manifest predate V4/V67; naked → and ~N% never swept|V67
B20|2026-06-18|manifest token-cut figure stale 30% vs measured ~40%|-
B21|2026-06-19|steno/design + missing clarity carrier predate V4/V67 sync|V67
B22|2026-06-19|README banned idiom; audit scanned symbols not idiom set|V67
B23|2026-06-22|spec AMEND assumed SPEC.md body; condense stubs redirect to check-extras|V49
B24|2026-07-21|MECHANIZE byte-identity forced multi-skill copy-paste; DRIFT class on any edit drift|V66,V72
B25|2026-07-21|/sdd:design slash-only + designs/ file fought Plan mode + bundled /design name collision|V31
