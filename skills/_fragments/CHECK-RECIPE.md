# CHECK-RECIPE — full check detail (load on demand)

Thin `skills/check/SKILL.md` owns phase order + script calls.
Load this fragment when classifying §V behavior, writing the REPORT, or debugging memo/scope.
Mechanical row catalog and memo schema also documented in script docstring + SPEC-FORMAT adjacent notes.

## MEMO schema v3

`.spec/check-state.json`:

```json
{
  "schema_version": 3,
  "last_clean_sha": "<git HEAD @ last clean run>",
  "v_row_shas": { "V<n>": "<sha256 of §V row body>" },
  "last_run_at": "<ISO-8601 timestamp>",
  "last_v_classifications": { "V<n>": "HOLD|HOLD-SINCE-CLEAN|SCOPE-EMPTY|VIOLATE-CAPTURED|LATENT" },
  "oversized_cell_ack": "<sha256 over sorted oversized cell-id set>"
}
```

Script owns both ends (memo invariant): audit emits invalidation advisories; write-memo writes only on clean.

## SCOPE — memo-driven default

Both scope dimensions script-emitted; LLM never hand-rolls `git diff`.

1. **§V dirty** = `memo|ADVISORY|v_row_shas drift` + `scope|ADVISORY|v-path-dirty`.
   Neither → `HOLD-SINCE-CLEAN`, skip.
2. **§T** re-verify scoped to `tasks|ADVISORY|flipped-since-clean`.
3. **§I + cite-DAG** full-sweep every run.

First-run, invalidated memo, or `--full` → classify all §V rows.
§V bodies: `emit-v-slices` (resolves check-extras stubs).

## CHECK invariants (behavioral)

Script never classifies behavior.
For each dirty `V<n>`:

1. Translate invariant → verifiable claim about code.
2. Recipe scope (invariant may reduce scope per scope-set invariant; default full repo).
   Touch set = script touched-set intersect scope; first-run / `--full` → scope itself.
3. Touch set empty → `SCOPE-EMPTY`, skip.
4. Clean since `last_clean_sha` + scope untouched → `HOLD-SINCE-CLEAN`, skip.
5. Else grep/read; verdict in {HOLD, VIOLATE, VIOLATE-CAPTURED, UNVERIFIABLE, SCOPE-EMPTY, HOLD-SINCE-CLEAN, LATENT}.
   Surfaced {VIOLATE, VIOLATE-CAPTURED, UNVERIFIABLE} → REPORT body.
   Silence {HOLD, HOLD-SINCE-CLEAN, SCOPE-EMPTY, LATENT} → summary `suppressed` only.
6. Record file:line evidence.

### Batch protocol

1. Batch count = audit `batch|ADVISORY|recommended: <n> agents` (`n` = 1 → main thread).
2. Partition = contiguous V spans.
3. Prompt = `emit-check-agent-prompt` (or `skills/_fragments/CHECK-AGENT-PROMPT.md`); fill `{...}` only.
4. Aggregate pipe-tables → REPORT.
5. Agent error → re-run that range serially.

## CHECK §-cite

Mechanical cite-DAG owned by script.
LLM adjudicates only `cite|ambiguous|…` → {spec-cite, phase-label, ambiguous}.

## CHECK interfaces

MATCH / DRIFT / MISSING / EXTRA.
List-shape fields → set-diff (spec set vs code symbols).

## CHECK tasks

1. `x` predates clean sha → HOLD-SINCE-CLEAN.
2. `x` flipped since memo → verify work present; absent → STALE.
3. `.` → note pending.

## REPORT shape

H2 blocks: `## invariant drift`, `## cite drift`, `## interface drift`, `## task drift`, optional `## checkpoint`, optional `## advisory`, `## summary`.
Silence-class excluded from body.
Clean run → checkpoint line before summary.
Dirty → omit checkpoint.

## WRITE-MEMO

```
python3 ${GROK_PLUGIN_ROOT}/scripts/check-mechanical.py emit-row-ids
# fill behavioral verdicts
python3 ${GROK_PLUGIN_ROOT}/scripts/check-mechanical.py write-memo --from-audit < filled
```

Exit 0 clean / 1 dirty / 2 invalid vocab.

## REMEDY → Next

- VIOLATE / DRIFT → `/sdd:spec <desc citing §V.n>` (BACKPROP).
- UNVERIFIABLE or multi-cite VIOLATE → lead with `/sdd:explain §V.n` before backprop/build.
- VIOLATE-CAPTURED → no action.
- history VIOLATE → `/sdd:spec amend` or `/sdd:condense` body-trim.
- format VIOLATE → `/sdd:spec amend` or `/sdd:condense`.
- MISSING → `/sdd:build` or `/sdd:spec` add task.
- STALE → `/sdd:spec amend` uncheck.
- EXTRA → `/sdd:spec amend interfaces`.
- UNRESOLVED / TYPE-MISMATCH → `/sdd:spec amend` repair cite.
- reorganize advisory → `/sdd:reorganize`.
- token advisory → `/sdd:condense`.

Never auto-remedy on dirty report.
