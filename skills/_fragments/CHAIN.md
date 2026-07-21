# Green-path chain (canonical)

Default-on for solo operators per recipe-step-no-dispatch invariant.
Operator may pass `--no-chain` on build or check to disable for that run.

## Edges (same turn, high confidence only)

| From | Condition | Same-turn continue |
|------|-----------|-------------------|
| build pass | §T flipped `.`→`x`, auto-commit landed | `/sdd:check` cascade over just-closed §T |
| check clean | ≥ 1 pending `.` §T | `/sdd:build --next` (or concrete `§T.n`) |
| build FAIL | class (b)/(c) | mid-loop `/sdd:spec` BACKPROP (already required) |
| check dirty | any surface violation | never auto-remedy; Next only |

## Rules

- Chain is not an orchestrator: at most one hop after the primary recipe completes.
- `--all` build: chain check once after the last closed row in the chain, not after every row.
- `--no-chain` wins over default-on.
- Backprop handoff after build FAIL still mid-loop; after BACKPROP APPLY, prefer concrete `/sdd:build §T.n` (resume card).
