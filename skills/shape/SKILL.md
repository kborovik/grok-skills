---
name: shape
description: |
  SDD structural shape: propose-then-critique via Grok Plan mode → approved
  plan folds into SPEC.md via `/sdd:spec fold-shape`. Invoke via `/sdd:shape
  <topic>` or natural language "shape the X for SDD". Exhausts structural Open
  Questions (distinct from socratic "enough"). Not the bundled Grok `/design`
  skill (write-review-revise design doc + PR plan).
allowed-tools: ask_user_question, read_file, grep, enter_plan_mode, exit_plan_mode
---

# shape — propose-then-critique via Grok Plan mode

Skill body SPEC-ADJACENT → telegraph.
Plan body user-reviewed pre-fold → steno: spell out arrows and inequalities as words, keep only `|` `§` raw (per steno SYMBOLS).

## POSITION IN FUNNEL

`/sdd:shape` is the structural front door.
Caller wants to commit a shape before `/sdd:spec` fold-in.
Layer unclear → ≤ 2 questions, then propose.
Bundled Grok `/design` is a different skill — never treat bare "design …" as this skill; "shape …" / `/sdd:shape` is the SDD surface.

## ARTIFACT — Plan mode, not designs/

Primary working artifact = Grok session **plan file** (`plan.md` under the session directory).

1. Call `enter_plan_mode` at start (user must approve plan mode).
2. Write / patch the shape proposal **only** into the plan file (plan mode allows that path; other writes fail).
3. Iterate Open Questions until empty.
4. Call `exit_plan_mode` to present the plan for approval.
5. On approve → Next leads with `/sdd:spec fold-shape` (same-session fold of approved plan content).
6. No default write to `designs/<slug>.md`.
   Optional durable export only if operator asks (`--export designs/<slug>.md` after approval, outside plan mode).

Legacy `designs/*.md` still folds via `/sdd:spec designs/<slug>.md` for old drafts.

## LOOP

1. `enter_plan_mode` (bail if declined → offer escape to `/sdd:spec` direct amend).
2. Read `SPEC.md` @ root (citation context only — never written this run); absent → degrade per § below.
3. Topic vague or empty → ≤ 2 questions to localize, then propose.
4. Propose shape (named structures, types, key decisions) in 1 pass → write plan file.
5. Surface `## Open Questions` list at bottom of plan.
6. Wait → user critique / answers (or plan-mode line comments).
7. Patch plan in place; resolved Qs → `## Design decisions` w/ rationale.
8. Repeat 6–7 until `## Open Questions` empty.
9. `exit_plan_mode` → user approves plan.

Never self-resolve Open Qs.
Never exit_plan_mode with open Qs unless escape parks them under `## Unresolved`.
Never mutate SPEC.md this run.

## DISTINCTION FROM SOCRATIC

- `socratic` — converges on "enough": 1 question/turn, sharpen intent.
- `shape` — converges on "exhausted": propose shape, exhaust open Qs.

Not merged. socratic = bug or small-feature framing. shape = structural choice.

## OUTPUT TEMPLATE (plan body)

steno body; § citations OK when `SPEC.md` present.

```
# <title>

## Problem

[symptoms + §B/§V cites; no SPEC.md → "shaping without SPEC anchor"]

## Proposal

[named structures, types, shape]

## [topic-specific sections]

## Effect on in-flight SPEC items

[§T/§V deltas described here, never applied to SPEC.md. omit if SPEC.md absent]

## Design decisions

[each resolved Q: **Decision:** ... **Why:** ...]

## Success criterion

[observable invariants]

## Out of scope

[deferred]

## Unresolved

[only if escape used]
```

## CODE READS

Reactive only — no preemptive scans.
Banned: grep repo before first proposal "for context".
OK: user cites `file:line` / symbol → read target.
Cap ≤ 2 reads/turn.

## SPEC.md DEGRADATION

`SPEC.md` absent → flag once: "shaping without SPEC anchor; §V/§B/§T citations omitted".
Continue; omit Effect section.

## LONG-SESSION ESCAPE

Single Open Q ≥ 3 turns unresolved → ask_user_question (decision-gate):

- header `Open-Q escape`
- `Park Q and converge` / `Keep going`

Park → `## Unresolved` in plan.

## FOLD-IN HAND-OFF

Post-approve Next:

```
## Next

1. /sdd:spec fold-shape — fold the approved plan into SPEC.md
2. /sdd:shape <topic> — re-run for a revised plan
3. /sdd:spec <intent> — amend SPEC directly without shape pass
```

`/sdd:spec fold-shape` (or free-form "fold the shape plan") → FOLD-IN mode: agent uses the just-approved plan content from this session (re-read session plan.md when path known; else in-context plan body).
Mutates SPEC.md only.

## ESCAPE HATCH

"just file it" / "skip shape" / "I already know" → stop; hand intent to `/sdd:spec`.

## BOUNDARY

Never mutate `SPEC.md` this skill.
Not root-cause debugging — backprop via `/sdd:spec <bug intent>`.

## MECHANIZE

Load `${GROK_PLUGIN_ROOT}/skills/_fragments/MECHANIZE.md`.
Run probe.
Emit Next item per fragment.

## OUTPUT — "Next" block

Per `skills/_fragments/NEXT.md`.
Mid-loop → Open-Q resolution items.
Post-approve → fold-shape leads.
