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

## §B BUGS

id|date|cause|fix
