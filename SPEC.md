# SPEC — sdd plugin

## §G GOAL

LLM writes code faster than humans read → standards + logic drift unchecked; counter: one telegraph SPEC.md authoritative over code; plugin skills keep code shape + component contracts aligned over time.

## §C CONSTRAINTS

- installable Grok skills plugin; root-source plugin `sdd` (`.grok-plugin/plugin.json`, optional `.grok-plugin/marketplace.json` source `./`)
- skills-only: every surface = `skills/<name>/SKILL.md`; no commands/ tree, no hooks
- `scripts/check-mechanical.py` single-file, stdlib-only python3
- no orchestrator, no swarm: main Grok agent executes; sub-agents read-only; exclusion: github post-spec-commit `/sdd:build` child on fold-produced §T ids write-capable; bundled `review` sub-agent scratch writes only (github-workflow invariant)
- no state beyond SPEC.md + git + REPO-LOCAL `.spec/` cache

## §I INTERFACES

external surface — what operator + consuming repo see.

- shape: `/sdd:shape <topic>` → Grok Plan mode propose-critique → approved plan → hand title/body (Problem + Proposal + Design decisions + Effect + Out of scope + Unresolved when present)/Acceptance/class to github ISSUE + stop; later fold via `/sdd:spec github issue N` or same-session `/sdd:spec fold-shape` (preserves issue N linkage; not bundled `/design`; no default `designs/` write)
- spec: `/sdd:spec <intent>` → socratic gate → SPEC.md delta preview → apply + auto-commit; fold-shape + micro-AMEND paths; `github issue N` / fold-shape+issue N → BRANCH (clean) then write then commit then draft PR (`Related: #<issue>`; no Closes); post-spec chain builds fold-produced §T ids (new + Acceptance-touched existing `.`) then review then READY; non-issue path: no BRANCH, no PR
- build: `/sdd:build [§T.n|§T.a,§T.b,…|--next|--all|--no-chain]` → plan → edit → verify → flip §T `.`→`x` + commit; green-path one hop per operator turn → check; issue-linked → PUSH per task, READY once post-loop (no check hop; Next merge phrasing); post-spec child (`POST-SPEC-CHILD=1`): fold-produced §T ids, implies `--no-chain`, PUSH only; task-scoped acceptance @ build; full acceptance @ MERGE only; MERGE probes checks + reviewDecision + mergeable
- check: `/sdd:check [--full|--no-chain]` → thin recipe + script; read-only REPORT + Next; clean chain → build --next
- explain: `/sdd:explain [§-cite|--next]` → prose expansion w/ cited siblings, zero writes
- condense: `/sdd:condense` → six-prong token sweep, single atomic commit
- reorganize: `/sdd:reorganize [--taxonomy-only]` → §V cluster + renumber + cite sweep (updates SPEC.md stubs and `.spec/check-extras.md` row prefixes), single atomic commit
- script: `python3 ${GROK_PLUGIN_ROOT}/scripts/check-mechanical.py <mode>` → pipe-table `id|verdict|evidence`; modes: audit, write-memo, fix-sembr, emit-v-slices, emit-superseded, emit-fold-seeds, emit-v-weights, emit-row-ids, emit-overview, emit-token-estimate, emit-residue, emit-archive-window, emit-condense-propose, emit-check-agent-prompt, --self-test
- fragments: `skills/_fragments/*` shared recipe text (MECHANIZE, NEXT, CHAIN, CHECK-RECIPE, POST-SPEC-CHAIN, …) — not slash surfaces
- format: `SPEC-FORMAT.md` → row shape + section catalog contract; loaded by spec, check, condense, reorganize

## §V INVARIANTS

numbered, testable, named; each ! hold. ids clustered by topic; gaps = cluster spans + closure history.

V1: spec-adjacent-register — → `.spec/check-extras.md §V1`
V2: github-facing-register — → `.spec/check-extras.md §V2`
V3: verbatim-preservation — → `.spec/check-extras.md §V3`
V4: symbol-set — → `.spec/check-extras.md §V4`
V10: sole-source-of-truth — → `.spec/check-extras.md §V10`
V11: shape-semantics-split — → `.spec/check-extras.md §V11`
V12: monotonic-numbering — → `.spec/check-extras.md §V12`
V13: cite-resolution — → `.spec/check-extras.md §V13`
V14: pinned-cite-ban — → `.spec/check-extras.md §V14`
V15: renumber-chain-walk — → `.spec/check-extras.md §V15`
V16: archive-semantics — → `.spec/check-extras.md §V16`
V20: write-ownership — → `.spec/check-extras.md §V20`
V21: write-serialize — → `.spec/check-extras.md §V21`
V22: recipe-step-no-dispatch — → `.spec/check-extras.md §V22`
V23: decision-gate — → `.spec/check-extras.md §V23`
V24: response-shape — → `.spec/check-extras.md §V24`
V25: socratic-gate — → `.spec/check-extras.md §V25`
V26: first-principle-probe — → `.spec/check-extras.md §V26`
V27: backprop-protocol — → `.spec/check-extras.md §V27`
V28: freshness-contract — → `.spec/check-extras.md §V28`
V29: fold-first — → `.spec/check-extras.md §V29`
V30: sweep-scope — → `.spec/check-extras.md §V30`
V31: shape-lifecycle — → `.spec/check-extras.md §V31`
V40: mechanical-realization — → `.spec/check-extras.md §V40`
V41: parametric-recipe — → `.spec/check-extras.md §V41`
V42: scope-set — → `.spec/check-extras.md §V42`
V43: drift-verdict-vocab — → `.spec/check-extras.md §V43`
V44: memo — → `.spec/check-extras.md §V44`
V45: scope-feed — → `.spec/check-extras.md §V45`
V46: batch — → `.spec/check-extras.md §V46`
V47: check-dispatch — → `.spec/check-extras.md §V47`
V48: token-budget — → `.spec/check-extras.md §V48`
V49: extras-hook — → `.spec/check-extras.md §V49`
V60: skills-only — → `.spec/check-extras.md §V60`
V61: sub-skill-flags — → `.spec/check-extras.md §V61`
V62: tooling-preference — → `.spec/check-extras.md §V62`
V63: plugin-shape — → `.spec/check-extras.md §V63`
V64: single-load — → `.spec/check-extras.md §V64`
V65: monitor-protocol — → `.spec/check-extras.md §V65`
V66: mechanize-scan — → `.spec/check-extras.md §V66`
V67: human-clarity — → `.spec/check-extras.md §V67`
V68: table-use — → `.spec/check-extras.md §V68`
V69: github-workflow — → `.spec/check-extras.md §V69`
V70: sembr — → `.spec/check-extras.md §V70`
V71: consumer-core-profile — → `.spec/check-extras.md §V71`
V72: shared-fragments — → `.spec/check-extras.md §V72`
V73: backprop-resume-card — → `.spec/check-extras.md §V73`
V74: micro-amend — → `.spec/check-extras.md §V74`
V75: auto-fire-engage-log — → `.spec/check-extras.md §V75`
V76: thin-check — → `.spec/check-extras.md §V76`

## §T TASKS
## archived: §T.1..§T.34 → SPEC.archive.md (34 rows)

id|status|task|cites
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
T60|x|script: discover_sembr_files include skills/_fragments/** + self-test; fix multi-sentence fragment lines|V70,B26
T61|x|condense+reorganize: replace PROGRESS/Next body copies with _fragments pointers|V72,B27
T62|x|telegraph+steno frontmatter: socratic-desc caller-engagement form (drop user-says triggers)|V61,B28
T63|x|script: ARCHIVE_CLOSED_T=50 constant; condense prong 3 consumes it; self-test|V48,B29
T64|x|condense+reorganize PROGRESS: name todo_write (mirror check; clear grant VIOLATE)|V62,B30
T65|x|acceptance-gate: build+github load ## Acceptance; block unproven close; post evidence comment|V69,B31
T66|x|document linear issue track + `/sdd:spec github issue N` fold route; no-Acceptance advisory|V69
T67|x|script: drop agents-md presence + marker-block audit; retire self-tests|V67,V40
T68|x|script emit-archive-window mode (action|tid_lo|tid_hi|count|marker; archive/keep/skip; ARCHIVE_CLOSED_T single source; keep newest N closed live, archive older id-asc); condense prong 3 consumes table only (skip → no archive); self-test|V48,V40,V16
T69|x|script: emit-row-ids --from-audit pre-fills HOLD-SINCE-CLEAN (clean §V/§T) + MATCH (§I) + blank verdict (dirty §V + flipped §T); write-memo --from-audit accepts table; check WRITE-MEMO recipe consumes (no hand-join advisory ids); self-test|V44,V40
T70|x|script emit-condense-propose mode (fold-seeds|superseded|archive-window|residue|v-weights tables; columns unchanged); condense PROPOSE consumes combined emit; self-test|V40,V66,V48
T71|x|shape post-approve: GitHub issue + class label + stop (skill + README)|V31,V69,I.shape,V2
T72|x|github skill: require PR per issue; after code complete follow bundled review skill, apply findings, then gh pr create|V69
T73|x|sweep remaining LINEAR-no-PR wording to PR-per-issue + review-apply; scope grep `LINEAR|solo linear|no PR required`|V69
T74|x|spec skill: patch FOLD-IN github issue — after OK, github BRANCH then SPEC.md commit on that branch then `gh pr create --draft` (spec commit first on PR; no close trailer @ create; no review-at-create); After-fold Next `/sdd:build`|V69,V22,I.spec
T75|x|github skill: PR create `--draft` no review no Closes; add PUSH (`git push` issue-linked branch w/ open PR); READY = review-apply + push + `gh pr ready`; MERGE = acceptance-gate then add Closes then squash|V69,V22
T76|x|build skill: issue-linked pass → github PUSH then load-and-run review-apply + push + `gh pr ready`; Next merge when approved|V69,V22,I.build
T77|x|README Issue-linked PR: branch then spec commit then draft PR; build then review-apply then `gh pr ready`; Closes only @ merge after acceptance-gate|V69,V2
T78|x|github skill: after spec APPLY commit + draft PR, run `/sdd:build --all` write-capable sub-agent then bundled `review` sub-agent; no operator wait|V69,V21,V22
T79|x|spec skill: FOLD-IN github issue POST-APPLY auto-chain run build then review (drop never-auto-build on that path)|V69,V22,I.spec
T80|x|build skill: post-spec-commit child write-capable; drop READY on that child (parent runs review next)|V21,V69
T81|x|README Issue-linked PR: spec commit + draft PR then auto-build sub-agent then auto-review sub-agent|V69,V2
T82|x|spec+github: post-spec-commit chain runs once; owner = github PR three-step list; spec After OK stops at draft PR|V69,B32,B33,I.spec
T83|x|V21+§C: post-spec review sub-agent scratch-write exclusion; spawn omits capability_mode read-only|V21,B34
T84|x|build skill: `POST-SPEC-CHILD=1` discriminator in LOAD; token set → PUSH only, drop READY|V69,V21,B35
T85|x|add `backprop-handoff.json` to `.spec/.gitignore` during initial setup|V73
T86|x|github skill MERGE: squash commit subject ! hold `#<issue>` via `gh pr merge --squash --subject "<title> (#<issue>)" --body "Closes #<issue>"`; GitHub default `(#PR)` insufficient|V69,B36
T87|x|script: github MERGE needles `--subject` + `#<issue>` (issue not merely PR); self-test|V69,V40,B36
T88|x|README Issue-linked PR: squash commit message holds `#<issue>`|V69,V2,B36
T89|x|github skill: BRANCH+PR require corresponding GitHub issue; missing → no git branch, no `gh pr create`|V69
T90|x|spec skill: non-github-issue APPLY: no github BRANCH, no github PR|V69,I.spec
T91|x|README Issue-linked PR: no corresponding issue → no git branch, no GitHub PR|V69,V2
T92|x|script: github no-issue-no-PR needles (corresponding GitHub issue + no BRANCH/no PR); self-test|V69,V40
T93|x|build+ACCEPTANCE-GATE+github: fire verify on issue linkage not planned trailer; ALLOW @ build = evidence only; close trailer MERGE-only|V69,B37
T94|x|script emit-v-weights: stub rows not heavy; self-test all-stub fixture → empty heavy set|V48,V40,B38
T95|x|condense prong 6: consume stub-skip from table (no re-extract)|V48,B38
T96|x|script classify_grants reverse (missing grant VIOLATE) + github allowed-tools add spawn_subagent, read_file, search_replace, write|V62,B39
T97|x|github skill: READY remainder wording — no cross-section step index|V69
T98|x|spec skill: split multi-sentence lines 93, 190, 193|V70
T99|x|patch acceptance-gate + build: task-scoped evidence check vs merge-time full gate|V69
T100|x|patch monitor + MECHANIZE: route consumer candidate via spec skill|V65,V66,V24
T101|x|patch build + github: post-spec child failure reports to parent without mid-loop prompt|V21,V69
T102|x|patch shape + spec: fold-shape retains created issue number for branch + PR linkage|V31,V69
T103|x|patch reorganize: renumber sweep updates .spec/check-extras.md headings and prefixes|V49,V12
T104|x|github CLOSE: `git switch <default-base>` before `git branch -D`; script needle asserts order|V69,V40,B40
T105|x|build: issue-linked READY fires once post-loop (single task or last `--all` row), not per task; per-task PUSH only|V69,V22,B42
T106|x|build+github: `POST-SPEC-CHILD=1` implies `--no-chain`; child builds fold-produced §T ids only (not whole backlog)|V69,V21,V22,B48,B49
T107|x|amend V47: check arg set admits `--no-chain` (+ `--full --no-chain`); script constant single-sources arg set|V47,V40,B43
T108|x|CHAIN+README: one hop per operator turn; hopped recipe never hops again|V22,V24,B44
T109|x|Next contract: merge dispatch route for issue-linked pause (trigger phrasing or thin surface)|V24,V69,B41
T110|x|spec: precondition covers resolved body file; branch before delta write on github fold|V20,V69,B50,B51
T111|x|condense: prong 6 consumes post-fold weights; atomicity non-goal reworded (confirmed set, one commit)|V48,V40,B45,B46
T112|x|shape: hand issue creation to github; drop gh grant; resolve plan-file write grant|V72,V62,V31,B53,B54
T113|x|script: widen missing-grant `write` pattern + shape fixture self-test|V62,V40,B54
T114|x|ACCEPTANCE-GATE: one evidence comment per issue-linked run (post @ READY not per task)|V69,B52
T115|x|github: `<issue>` vs `<pr>` placeholders throughout (never dual `<n>`)|V69,B55
T116|x|monitor: drop stale five-member roster line (cite sub-skill-flags invariant)|V61,B47
T117|x|build+explain: emit modes replace whole-file SPEC.md read, or explain exemption in V64|V64,B56
T118|x|reorganize CONFIRM: subset restarts the gate pass (no mid-pass re-prompt)|V23,B57
T119|x|amend V69+§I.build+ACCEPTANCE-GATE: full accept MERGE-only; drop PR-ready full-gate claim (align T99)|V69,B58
T120|x|amend V25: drop FOLD-IN from socratic mode set; name dispatch shortcut|V25,B59
T121|x|check description: never invokes remedies except green-path chain|V22,V76,B60
T122|x|spec POST-APPLY: fold-shape+issue N same chain ownership + Next as FOLD-IN github issue|V31,V69,V22,B61
T123|x|github+build child-fail: parent Next may `/sdd:spec` BACKPROP when class b/c (not build-only)|V21,V27,V69
T124|x|CHAIN+build+NEXT: issue-linked READY skips check hop; Next merge phrasing leads, `/sdd:check` listed not hopped|V22,V24,V69,B63
T125|x|README Issue-linked PR: drop post-spec `--all`; fold-produced §T ids only|V69,V2,B64
T126|x|shape ISSUE body: Effect + Out of scope + Unresolved when present + Acceptance|V31,V69,I.shape,B65
T127|x|github READY remainder: post-spec apply bug+suggestion, list nits no wait; operator-run may wait nits|V69,B66
T128|x|spec+github: fold-produced ids = new §T + existing `.` rows receiving Acceptance notes this fold|V69,B67
T129|x|github READY: re-run verify after review-apply before `gh pr ready`; fail → not ready|V69,B68
T130|x|github MERGE: probe `gh pr checks` + `reviewDecision,mergeable`; empty checks skip; required reviews missing or CHANGES_REQUESTED or unmergeable → BLOCK|V69,B69
T131|x|github CLOSE: `gh pr close --delete-branch` then switch default-base then local -D|V69,B70
T132|x|github+build child-fail: `gh pr comment` steno (§T, class, Next) on draft PR|V21,V69,B71
T133|x|github PR body: `Related: #<issue>` line; no Acceptance copy; no secondary Closes @ create|V69
T134|x|script: github MERGE check-probe + CLOSE `--delete-branch` + PR `Related: #<issue>` needles; self-test|V69,V40
T135|x|script: github READY remainder no-wait + fold-produced Acceptance-notes + re-run verify + `gh pr comment` needles; self-test|V69,V40,B66,B67,B68,B71
T136|x|init skills/_fragments/POST-SPEC-CHAIN.md; spec After OK + POST-APPLY + NON-GOALS, github WHEN/PR, build LOAD/POST-LOOP/CHAIN/OUTPUT point not restated three-step chain; scope grep `fold-produced|POST-SPEC-CHILD|parent runs review`|V72,V69,V21,I.spec,I.build
T137|.|pointer-only MECHANIZE + PROGRESS + PATH-SCOPED-COMMIT (drop paraphrase after pointer; spec/condense/reorganize load PATH-SCOPED-COMMIT); scope grep `Run probe. Emit Next item per fragment|todo_write one task per phase|-m. flags`|V72,V66,V20
T138|.|drop restated fragment/invariant bodies (github ACCEPTANCE-GATE load-only; monitor PROTOCOL/DISPATCHED share WRITE; WRITE-TIME PRUNE + condense prong 4 cite script HR_* names; NON-GOALS unique-boundary only; spec APPLY drop structurally-no-op + Defends-against tails); scope grep `## ACCEPTANCE-GATE|structurally no-op|Defends against|amendment-counter|no hook / runtime`|V72,V28,V40,V69

## §B BUGS
## archived: §B.0..§B.0 → SPEC.archive.md (0 rows)

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
B26|2026-07-21|discover_sembr_files omits skills/_fragments/**; multi-sentence fragment lines unaudited|V70
B27|2026-07-21|condense+reorganize copy PROGRESS/NEXT body instead of _fragments pointer|V72
B28|2026-07-21|telegraph+steno frontmatter advertise user-says triggers; collide w/ caller dispatch|V61
B29|2026-07-21|closed-§T archive threshold 50 hardcode in condense; not script constant per V48|V48
B30|2026-07-21|T61 PROGRESS pointer left todo_write grant without body literal on condense+reorganize|V62
B31|2026-07-21|Closes #N / issue close w/o Acceptance audit → silent-pass|V69
B32|2026-08-22|post-spec-commit chain copied into APPLY + POST-APPLY → double build+review|V69
B33|2026-08-22|spec-side post-spec chain omitted READY remainder|V69
B34|2026-08-22|V21 read-only default blocked review sub-agent scratch writes|V21
B35|2026-08-22|post-spec build child had no discriminator; took operator-run READY path|V69
B36|2026-08-23|squash-merge default subject PR title `(#PR)`; `Closes #<issue>` on PR body not squash commit → git log cannot recover closed issue|V69
B37|2026-08-23|acceptance-gate trigger keyed on close trailer after close trailer banned on build commits|V69
B38|2026-08-23|emit-v-weights ranks already-stubbed §V rows as heavy|V48
B39|2026-08-23|grant-use audit extras-only; recipes omit required grants|V62
B40|2026-08-23|github CLOSE `git branch -D` while still on issue-linked branch; git refuses delete current branch|V69
B41|2026-08-23|Next "merge when approved" has no user-invocable dispatch; github auto-fire only|V24,V69
B42|2026-08-23|build issue-linked READY inside per-task loop; `--all` re-runs review+pr-ready each row|V69,V22
B43|2026-08-23|V47 check-dispatch bare/`--full` only; §I+skill+README accept `--no-chain`|V47
B44|2026-08-23|CHAIN "at most one hop" vs two default edges; hop depth per turn vs per recipe unclear|V22,V24
B45|2026-08-23|condense NON-GOALS all-or-none firing set; CONFIRM offers force-skip + subset|-
B46|2026-08-23|condense prong 6 consumes pre-fold v-weights while prong 1 claims fold-first reshape|V48
B47|2026-08-23|monitor "5th member" roster omits github; V61 lists six auto-fire skills|V61
B48|2026-08-23|post-spec child `/sdd:build --all` closes whole backlog into one issue PR|V69
B49|2026-08-23|POST-SPEC-CHILD inherits green-path chain; child may hop check then build --next|V21,V22,V69
B50|2026-08-23|spec Step 0 porcelain checks SPEC.md only; stub AMEND body file uncommitted work leaks|V20,V49
B51|2026-08-23|spec github fold: branch vs delta-write order underspecified; dirty checkout risk|V69,V20
B52|2026-08-23|ACCEPTANCE-GATE COMMENT posts every ALLOW; build per-task gate → N comments per `--all`|V69
B53|2026-08-23|shape POST-APPROVE duplicates github ISSUE create steps; dual ownership drift class|V31,V72,V69
B54|2026-08-23|shape prescribes plan-file write/patch; allowed-tools omits write; grant audit pattern misses|V62
B55|2026-08-23|github body `<n>` means issue id and PR id in different sections|V69
B56|2026-08-23|build+explain whole-file SPEC.md Read; single-load prefers script emit where covered|V64
B57|2026-08-23|reorganize CONFIRM subset re-emits CONFIRM inside no-mid-flow-reprompt gate|V23
B58|2026-08-23|V69+§I+ACCEPTANCE-GATE claim full accept @ PR ready; T99+recipes = MERGE-only full gate|V69
B59|2026-08-23|V25 lists FOLD-IN as socratic mode; FOLD-IN is dispatch shortcut bypassing gate|V25
B60|2026-08-23|check description "never invokes" remedies; body+V22 green-path chain invokes build|V22,V76
B61|2026-08-23|spec POST-APPLY names FOLD-IN github issue only; fold-shape+issue N same issue-linked path under-specified|V31,V69
B62|2026-08-23|post-spec child fail → parent Next build-only; no BACKPROP offer for class b/c|V21,V27,V69
B63|2026-08-23|issue-linked READY hops `/sdd:check`; check Next drops merge phrasing|V22,V24,V69
B64|2026-08-23|README Issue-linked PR still says post-spec `/sdd:build --all`|V69
B65|2026-08-23|shape ISSUE body drops Effect + Out of scope + Unresolved; fold from issue sees reduced plan|V31
B66|2026-08-23|READY remainder unless-operator-declines vs post-spec no-wait|V69
B67|2026-08-23|fold-produced ids omit existing `.` §T rows that received Acceptance notes; MERGE blocks after ready|V69
B68|2026-08-23|READY review-apply then `gh pr ready` with no re-verify|V69
B69|2026-08-23|MERGE runs `gh pr merge` with no probe of checks or `reviewDecision` or mergeable|V69
B70|2026-08-23|CLOSE deletes local branch only; remote branch remains|V69
B71|2026-08-23|post-spec child fail reports to parent session; draft PR has no GitHub comment|V21,V69
