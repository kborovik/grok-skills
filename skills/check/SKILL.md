---
name: check
description: |
  Read-only drift detector. Diffs SPEC.md vs current code, reports violations
  grouped by severity. Writes nothing — suggests remedies via spec or build
  skills, never invokes them. Triggers when user asks to check drift, audit
  spec, or verify invariants. Phrasings: "check drift", "audit the spec",
  "check invariants", "spec vs code", "is the spec still accurate?",
  "did the code drift?".
allowed-tools: read_file, run_terminal_command(python3 */check-mechanical.py *), spawn_subagent, todo_write
disallowed-tools: search_replace, write
---

# check — drift report

Pure diagnostic.
Reports violations; writes nothing to SPEC or code; user decides remedy.
Mechanical audits → `scripts/check-mechanical.py` (mechanical-realization invariant).
Behavioral judgment stays LLM.
Detail: load `${GROK_PLUGIN_ROOT}/skills/_fragments/CHECK-RECIPE.md` when classifying or reporting.
Read-only → sub-agent delegation safe.

## PROGRESS

Multi-phase: LOAD, audit, §V classify, §I + cite-DAG, §T, REPORT + WRITE-MEMO.
Per `skills/_fragments/PROGRESS.md` — todo_write one task per phase; status `in_progress` then `completed`.

## LOAD

1. `python3 ${GROK_PLUGIN_ROOT}/scripts/check-mechanical.py emit-overview`
   Non-zero / no SPEC.md → "no spec, nothing to check."
   Stop.
2. Args (check-dispatch invariant): bare = memo-driven default; `--full` = drop memo, re-classify all; other → bail.
   `--no-chain` disables green-path chain after clean report.
3. `python3 ${GROK_PLUGIN_ROOT}/scripts/check-mechanical.py audit [--full]`
   Consume scope-feed + batch advisory rows machine-side (never surface in advisory prose).
4. §V bodies: `emit-v-slices [--dirty V…]` — resolves check-extras stubs; never whole-file SPEC.md Read (single-load invariant).

## CLASSIFY

### §V

Follow CHECK-RECIPE "CHECK invariants".
Batch count = audit `batch|ADVISORY|recommended: <n> agents`.
Sub-agent prompt = `python3 ${GROK_PLUGIN_ROOT}/scripts/check-mechanical.py emit-check-agent-prompt` (fill `{...}` only; never paraphrase).
n = 1 → main-thread.
Agent fail → re-run span serially.

### §I

MATCH / DRIFT / MISSING / EXTRA.
List-shape fields → set-diff.

### §T

HOLD-SINCE-CLEAN / STALE / pending `.` per CHECK-RECIPE.

### Cite

Script owns UNRESOLVED / TYPE-MISMATCH.
LLM adjudicates only `cite|ambiguous|…`.

## REPORT + WRITE-MEMO

Telegraph H2 blocks: invariant / cite / interface / task drift; optional checkpoint; optional advisory; summary.
Silence-class → summary `suppressed` only.
Clean → write-memo via emit-row-ids skeleton + `write-memo --from-audit` (memo invariant).
Dirty → no memo advance.

Advisory sources: token, sembr, memo-invalidation, history-oversized, **reorganize** (sparse §V numbering / cluster gap smell when script or LLM notes renumber debt).

## CHAIN (default-on)

Per `skills/_fragments/CHAIN.md`.
Clean report + ≥ 1 pending `.` §T + not `--no-chain` → same-turn `/sdd:build --next` (or concrete §T).
Dirty → never auto-remedy.

## REMEDY → Next

Map per CHECK-RECIPE REMEDY.
UNVERIFIABLE or multi-cite VIOLATE → lead with `/sdd:explain §V.n` before `/sdd:spec` / `/sdd:build`.
Never invoke fixes from this skill.

## MECHANIZE

Load `${GROK_PLUGIN_ROOT}/skills/_fragments/MECHANIZE.md`.
Run probe.
Emit Next item per fragment.

## OUTPUT — "Next" block

Per `skills/_fragments/NEXT.md`.
Dirty example: explain-first when unclear, then spec/build remedies.
Clean + pending + chain off → `/sdd:build --next`.
Terminal clean → `/sdd:spec` seed.

## NON-GOALS

- Zero writes to SPEC or code (memo via script only).
- No scores; binary holds-or-drifts.
