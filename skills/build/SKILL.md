---
name: build
description: |
  Plan-then-execute impl vs SPEC.md. Triggers when user asks to build,
  implement, or execute spec or specific §T task. Phrasings: "build §T.<n>",
  "build --next", "implement next task", "run the build", "does the
  implementation run?", "is §T.<n> done?".
allowed-tools: ask_user_question, read_file, search_replace, write, run_terminal_command, skill, todo_write
---

# build — implement spec

Single-thread native plan→execute.
You are the main Grok agent.
No swarm.

## LOAD

1. Read `SPEC.md`.
   Missing → tell user run spec skill first; bail.
2. Parse `$ARGUMENTS`:
   - `§T.n` → that task only
   - `--next` or empty → lowest-numbered `.` row
   - `--all` → every `.` row in §T order — plan once, then chain {edit → verify → commit} per row
   - `--no-chain` → disable green-path check hop after pass
3. If `.spec/backprop-handoff.json` exists → treat as resume card (see FAIL → BACKPROP); prefer its `T` id when args empty/`--next`.

## PLAN

Emit plan inline, per chosen task(s) — never enter plan mode (wait-state breaks `--all`):

1. Cite every applicable §V invariant.
2. Cite every §I interface touched.
3. List files to init / patch.
4. List tests to add or patch (one per invariant touched).
5. Name verification cmd (test, build, lint).

Emit plan inline → EXECUTE.

## PROGRESS

`--all` = multi-phase per `skills/_fragments/PROGRESS.md`.
todo_write one task per chosen `.` §T row.
FAIL → BACKPROP (status stays `.`) → task stays `in_progress`.

## UPSTREAM-FR (plugin-meta only)

When §T goal matches `upstream FR` (case-insensitive) **and** cwd is plugin dev repo (origin resolves to manifest `.repository`) → load `${GROK_PLUGIN_ROOT}/skills/_fragments/UPSTREAM-FR.md` and run it.
Consumer repos: skip this section entirely (consumer-core-profile invariant).

## EXECUTE

Per task in order:

0. UPSTREAM-FR trigger + plugin repo → run fragment first; else skip.
1. Edit code per plan.
   Stage explicit paths for step-3 probe; commit path-scoped (see `skills/_fragments/PATH-SCOPED-COMMIT.md`).
2. Run verification cmd.
3. Staged diff touches PUBLISHED → probe `.spec/check-extras.md` judgment recipes when present.
4. **Pass** → flip §T.n `.`→`x`; path-scoped commit `T<n>: <goal line>` + §V cites on listed paths + SPEC.md.
   Clear `.spec/backprop-handoff.json` if present.
5. **Fail** → FAIL → BACKPROP.
   No blind retry.
   Status stays `.`.

## FAIL → BACKPROP

1. Read failure output.
2. Classify: (a) code bug, (b) spec wrong, (c) unspec edge.
   Low confidence → ask_user_question: Code bug / Spec wrong / Unspec edge.
3. (a) → fix code, retry.
   No spec change.
4. (b)/(c) → run spec skill with cause as free-form intent (BACKPROP).
   Spec commits §B (+ §V, §T).
   Resume: failing test first, then fix; commit cites new §B/§V.

Mid-loop spec dispatch = sole mandatory exclusion from operator-only dispatch (plus green-path chain).

## CHAIN (default-on)

Per `skills/_fragments/CHAIN.md`.
After successful pass (single task or last of `--all`), unless `--no-chain`: same-turn `/sdd:check` cascade over just-closed §T.

## WRITE POLICY

Build flips §T status cells only.
Every other SPEC.md edit → spec skill.

## MECHANIZE

Load `${GROK_PLUGIN_ROOT}/skills/_fragments/MECHANIZE.md`.
Run probe.
Emit Next item per fragment.

## OUTPUT — "Next" block

Per `skills/_fragments/NEXT.md`.
Pass (chain off) → check leads, then build --next.
Backlog clear → `/sdd:spec` seed.

## NON-GOALS

- No progress dashboards — `grep §T SPEC.md` is the dashboard.
- No speculative work beyond chosen task scope.
