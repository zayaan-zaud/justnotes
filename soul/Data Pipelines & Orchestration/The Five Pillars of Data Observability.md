---
tags: [data-engineering, observability, monitoring, data-quality]
source: [claude-conversation, 2026-08-31; industry framework, Monte Carlo/Metaplane et al.]
---
# The Five Pillars of Data Observability

A checklist for what "we'd know if this pipeline were producing bad data" actually requires watching — most pipeline monitoring only covers the first rung of this (did the job run), which is exactly the gap [[Orchestrator Status Is Not Ground Truth]] describes at the job level; this generalizes the same concern to the data itself.

## The five pillars
1. **Freshness** — did new data arrive on the schedule it's supposed to? (a table that hasn't updated in 36 hours when it updates hourly)
2. **Volume** — is the row/byte count in the range it's supposed to be? (silent duplication, a silently-empty source, a partial load)
3. **Schema** — did the structure change out from under downstream consumers? (a renamed/dropped/retyped column)
4. **Distribution** — do the values look statistically normal? (a currency column suddenly all zero, a rate that jumped from 2% to 40%)
5. **Lineage** — where did this data come from and what touched it, so a problem found downstream can be traced to its actual origin instead of guessed at?

## When to use
Deciding what a pipeline's monitoring should actually check, beyond "did the orchestrator report success." A job that runs green on schedule tells you nothing about volume, schema, or distribution — those need their own explicit checks (schema tests, a reconciliation query, a schema-diff step), not inference from job status.

## Gotchas
- These pillars catch *symptoms*, not causes — a distribution alert says "this looks wrong," not "here is the bug." Pair with [[A Constraint Is Only as Strong as Its Key]]-style thinking to find the actual mechanism once a pillar fires.
- A check that only re-asserts the same weak invariant a bug already satisfies is vacuous — see [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] before trusting a pillar's green light as proof of correctness rather than absence of one *known* failure mode.
- Freshness and volume are the cheapest to implement and the most commonly deployed; schema and distribution catch the failure modes that silently corrupt correct-looking pipelines, so under-investing in those two is the common gap.

## Related
[[Orchestrator Status Is Not Ground Truth]] · [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[A Constraint Is Only as Strong as Its Key]] · [[Write-Audit-Publish (WAP) Pattern]]
