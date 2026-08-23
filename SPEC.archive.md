# SPEC.archive — archived rows

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

## §B BUGS

id|date|cause|fix
