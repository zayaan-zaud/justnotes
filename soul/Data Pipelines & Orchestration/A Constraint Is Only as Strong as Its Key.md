---
tags: [data-engineering, data-quality, pipeline-design, idempotency]
source: [claude-conversation, 2026-08-25]
---
# A Constraint Is Only as Strong as Its Key

A uniqueness constraint enforces whatever the key *declares*, not what identity actually is. If the key omits an attribute that is genuinely part of a thing's identity, the constraint faithfully enforces the wrong identity and stays green forever — so the defect never surfaces as a violation, it surfaces as silent data loss.

## Two shapes of the same bug

**Key too narrow for a stored grain.** Rows that are genuinely distinct collide under the declared key, and a dedupe keeps one and discards the rest. The unique index succeeds; the row count simply sits lower than it should and never moves, which reads as "no new activity" rather than as a fault.

**Key too narrow for a unit of work.** A load/run key that identifies a fetched window by only one of its bounds (`<report>_<date_to>`) gives a narrow fetch and a wide fetch the *same* identity. Any process that treats same-key rows as versions of one another — a compaction, an upsert, a "supersede the old load" step — then deletes the wide one as though it were a stale copy of the narrow one. Under the declared key it genuinely *was* the same thing, so nothing is violated.

## Why the tests usually don't help either
A grain test typically asserts the same key the model declares, so it *encodes* the bug rather than detecting it. Worse, a test over a **superset** of the key is a strictly weaker assertion than the key itself and can never detect a duplicate grain — it passes on data that already violates the intended grain.

A run-log reconciliation (`sum(rows_loaded)` vs live count) can also be structurally blind: if the superseding step deletes the log row *together with* its data rows, both sides drop in lockstep and the totals still tie.

## When to use
Reach for this whenever designing a dedupe key, upsert key, compaction key, or idempotency/run-log key. The diagnostic question is: **"what two genuinely different things could produce this same key string?"** A key naming a *range* must encode both bounds; a key naming a *snapshot* must resolve finely enough that two consecutive pulls cannot share it.

Corollary worth stating separately: a constraint speaks only about rows that are **present**. Completeness — an entire month, partition, or window that vanished — is a different question and needs a check that asks about absence directly (e.g. assert no gap in the calendar months between a table's own min and max). See [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] for grading what a given check can actually prove.

Prefer fixes that make the collision **impossible by construction** (widen the key to carry full identity) over fixes that make it merely **detectable afterwards** (add monitoring, add a test). Detection is the fallback when the key change is too risky to make immediately.

## The "too narrow for a unit of work" case has a second, independent fix
Even with the key correctly widened going forward, a compaction/supersede step that **deletes** the row it's replacing is still fragile: deleting the old log row *together with* its data rows is exactly what makes a `sum(rows_loaded)`-vs-live-count reconciliation blind to the bug in the first place (both sides drop in lockstep). The [[Audit Dimensions|audit-dimension]] answer is to **mark** the superseded row (a `superseded_by` pointer to whatever replaced it) rather than delete it — the evidence survives, a lockstep-blind reconciliation stops being possible even if the key is later found to be wrong again, and "is this still current" becomes a filter (`WHERE superseded_by IS NULL`) instead of "does this row still exist." Keep the two fixes separate in your head: widening the key prevents the wrong thing from being deleted; marking-not-deleting means that even a residual bug in the key can't destroy evidence.

## Related
[[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Empirical Grain Discovery (Candidate-Key Cardinality Probing)]] · [[Idempotent-Skip as the Only Recovery Mechanism]] · [[Grain]] · [[Round Inferences Toward the Cheap Error]] · [[Audit Dimensions]] · [[Write-Audit-Publish (WAP) Pattern]] · [[The Five Pillars of Data Observability]] · [[Expand-Contract for Backward-Compatible Schema Migrations]] · [[The Delete Key and the Grain Are Different Things]] · [[A Dedupe Key That Encodes an Assumption Deletes Data When the Assumption Is False]]
