---
tags: [data-engineering, pipeline-design, data-quality, ci-cd]
source: [claude-conversation, 2026-08-31; industry pattern popularized by Netflix, DataWorks Summit 2017]
---
# Write-Audit-Publish (WAP) Pattern

A three-stage discipline for landing data safely: write it to an isolated staging location first, run validation checks against that staged copy, and only then publish (a fast, usually metadata-only swap/rename/partition-move) into the location downstream consumers actually read. It exists because once new data is materialized into a production table, any consumer running between "commit" and "someone notices something's wrong" has already read the bad version — validating *after* publish is too late by design.

## The three stages
1. **Write** — land the new data into a staging table, partition, or branch that nothing downstream reads yet.
2. **Audit** — run data-quality checks (row counts, null rates, referential checks, distribution checks) against the staged data, not the live table.
3. **Publish** — if every check passes, move the staged data into the production location. On table formats that support it (Iceberg, Delta, or a schema-swap in a plain warehouse), this step is a fast atomic pointer/metadata change rather than a data rewrite, so it doesn't reintroduce the exposure window it's trying to close.

## When to use
Any load where a downstream consumer (a dashboard, an ML feature store, another team's pipeline) could read data before a human or an automated check has had a chance to catch a bad batch — especially loads with any of: a source that can silently send corrupt/incomplete data, no output validation as it happens, or an incident history of "a consumer raced a partial write."

## Gotchas
- WAP only protects the publish boundary — it does nothing for whatever produced bad data upstream; pair it with real data-quality checks in the audit stage, not just a row-count sanity check (see [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]]).
- The publish step's atomicity is only as good as what backs it — a plain `INSERT INTO prod SELECT * FROM staging` is not atomic and reintroduces the exposure window; a rename/swap or a table-format snapshot commit is what actually delivers the guarantee.
- Doesn't replace idempotent-load design — a failed audit still needs a defined "what happens to this write" answer (retry, quarantine, alert), the same way [[Idempotent-Skip as the Only Recovery Mechanism]] needs a defined re-run story.

## Related
[[A Constraint Is Only as Strong as Its Key]] · [[Idempotent-Skip as the Only Recovery Mechanism]] · [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Slim CI - Diff Against a Known-Good State, Not a Full Rebuild]] · [[The Five Pillars of Data Observability]]
