---
name: telegraph
description: |
  Telegraph encoding — LLM-facing compression for SPEC.md and spec-adjacent
  writes. Loaded by /sdd:spec, /sdd:build, /sdd:check
  when any write to SPEC.md or spec-adjacent telegraph surface is required.
  Not user-invoked — engaged from caller recipes (spec, build, check).
user-invocable: false
---

# telegraph — LLM-facing telegraph encoding

## ENGAGE LOG

When engaged for a SPEC-adjacent write, emit one telegraph line once per run: `engaged sdd:telegraph`.

Audience: LLM re-reading SPEC.md into context.
Humans operate via /sdd:* cmds; /sdd:explain decodes telegraph → prose.
Compression = telegraphic grammar (content words kept, function words dropped, as in telegrams), not symbols — heavy math operators cost 2–4 tokens vs 1-token word.
Telegraph vs steno: telegraph drops grammar, runs in fragments; steno keeps grammar intact for GitHub reviewers.

Applies: SPEC.md writes, spec-referencing prose, backprop entries.
Not: code, error strings, commit messages, PR descriptions, anything a human reads on GitHub → use `sdd:steno`.

## GRAMMAR

- Drop articles (a, an, the), filler (just, really, basically, simply, actually), aux verbs where fragment works (is, are, was, were, being), pleasantries, hedging (might, perhaps, could be worth).
- Fragments fine.
- Canonical verbs/nouns per `## VERBS` / `## NOUNS`.
- Pipe tables compact: header row + data rows, bare `|cell|cell|` — no alignment padding, no separator row.
  Escape literal `|` as `\|`.

## SYMBOLS

Keep set — low-token, parse-clear; prefer over the word:

```
→   leads to / becomes / on <x>
≥   at least
≤   at most
!   must / required
?   may / optional / unknown
§   section reference
|   pipe-table delimiter (no semantic meaning)
```

No other symbols.
Math operators outside keep set (for-all, exists, element-of, not-equal, and, or, …) cost 2–4 tokens each — write the ASCII word.
For "or" write `or` — never bare `|`.

Exclusions (apply to SYMBOLS, VERBS, NOUNS): backticks, verbatim trigger phrases, domain-load-bearing named ops (`backprop`, `telegraph-encode`, `socratic`, `steno`).

## VERBS

Use canonical form; never synonyms in `avoid` column.

**Write / edit ops**

|canonical|meaning|avoid|
|`add`|place new entity inside existing container|introduce, insert|
|`init`|bring new container into existence (file, §)|create, generate, scaffold|
|`drop`|remove entity|remove, delete, erase|
|`swap`|exchange one for another, in place|replace, substitute, switch|
|`fix`|repair broken behavior|implement (when fixing), correct|
|`patch`|targeted change to existing entity|mutate, modify, change, alter, update|
|`sweep`|apply same edit every matching sites|refactor (when mechanical)|
|`sync`|reconcile two surfaces back to agreement|update (when reconciling)|
|`append`|add at end of ordered seq (log, table, list)|push, attach|
|`strip`|drop substructure (prefix, whitespace, tokens)|trim, clean|
|`extend`|enlarge existing structure (additive change)|expand, augment, grow|
|`tighten`|sharpen wording, scope unchanged|refine, improve, polish|

**Read / check ops**

|canonical|meaning|avoid|
|`read`|load file/section into context|inspect, view, examine, look at|
|`cite`|reference §V/§I/§T row|reference, mention, point to|
|`grep`|scan text for pattern, return matches|search, scan (when pattern-match)|
|`diff`|compare two surfaces, return delta|compare (when surface-vs-surface)|
|`audit`|systematic rule-check, return pass/fail|review, verify (when ruleful)|
|`parse`|structured read of formal input|interpret, decode|
|`trace`|follow chain (cause→effect, caller→callee)|track, follow|

**Runtime ops**

|canonical|meaning|avoid|
|`run`|execute cmd / skill / task|execute, invoke, trigger (when running), tackle (when applied to task)|
|`retry`|re-run after failure|redo, reattempt|
|`bail`|exit early w/ nothing done|abort, halt, quit, give up|
|`fire`|dispatch event / hook|trigger (when dispatching), send|
|`emit`|produce output (stdout, file, channel)|output, print, write (when emitting)|
|`render`|format for display (table, prose, diagram)|format, display, show, generate|
|`ask`|prompt user for input / decision|prompt, query, request|

**State ops**

|canonical|meaning|avoid|
|`set`|assign value to a field|assign, configure|
|`mark`|transition state field to known value (`mark T<n> x`)|tag (when stateful), flag|
|`flag`|raise attention / categorize as violation|warn, alert, note|
|`open`|create new tracked entity w/ initial status|start, begin, file (when opening)|
|`close`|transition entity to terminal state|finish, complete, resolve|

Domain-load-bearing verbs override (`backprop` as bug-protocol noun, `telegraph-encode` as named encoding op).
Canonical form distorts domain semantic → keep domain verb.

## NOUNS

|canonical|meaning|avoid|
|`exclusion`|permitted divergence from default rule; named exception|carve-out, exemption, escape hatch|
|`require` (modal `!`)|obligation; mandate|earn (when subject not animate)|
|`record` or `ledger`|persisted bug-class store|memory (when used metaphorically for §B)|

Extend as new pairs surface — idioms/metaphors caught by sweep tasks land as noun rows here.

## PRESERVE VERBATIM

Never compress: code/backticked snippets, paths, URLs, identifiers (function/variable/env names), numbers, versions, error strings, SQL/regex/JSON/YAML, quoted strings.

## SHAPES

**Invariant**:

```
V<n>: <subject> <relation> <condition>
V<n>: every req → auth check before handler
V<n>: token expiry ≤ current_time → reject
```

**Bug row** (pipe table under §B):

```
id|date|cause|fix
B<n>|2026-04-20|token `<` not `≤`|V<n>
```

**Task row** (pipe table under §T):

```
id|status|task|cites
T<n>|x|add auth mw|V<n>,I.api
```

Status: `x` done, `.` todo.

**Interface**:

```
<kind>: <name> → <shape>
api: POST /x → 200 {id:string}
cmd: `foo bar <arg>` → stdout JSON
env: FOO_KEY ! set
```

## ADDRESSING

`§<S>.<n>` = section.item ref (e.g. `§V.<n>` = invariants §, item n).
Cmd args, commits, PRs cite by § → zero ambiguity.

## ONE FILE RULE

Big project → more §s, not more files. grep ceremony kills agent speed.
Token-budget overflow → /sdd:condense (folds, trims, archives), never split.
Thresholds live w/ condense skill + audit script, not here.

## EXAMPLES

Bad: "The system should ensure that every incoming request is properly authenticated before being forwarded to its corresponding handler function."
Good: `V<n>: every req → auth check before handler`

Bad: "We discovered that the token expiration check in the middleware was using a strict less-than comparison, so tokens were rejected at the exact moment of expiry."
Good: `B<n>: token `<` not `≤` → reject @ expiry boundary.`

Bad: "The POST endpoint at /x accepts a JSON body and returns a 200 response with an object containing the created id."
Good: `api: POST /x → 200 {id}`

## BOUNDARIES

Normal English when: user asks for prose explanation; external-review docs (RFC, pitch); diff comments in code.
Commit messages: subject = per-skill fixed template (preserve verbatim); body = steno per steno skill (humans read git log).

## WHEN UNSURE

Cutting a word loses a fact → keep it.
Compression, not amputation.
