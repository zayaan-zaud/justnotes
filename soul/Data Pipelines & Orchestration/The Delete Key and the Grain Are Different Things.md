---
tags: [etl, idempotency, incremental-load, keys, dbt]
source: [claude-conversation, 2026-09-08 — rin claim-paid register re-grain]
---
# The Delete Key and the Grain Are Different Things

In a delete-then-insert incremental load, the key used to **clear old rows** and the key that defines the table's **grain** serve different purposes, and forcing them to be the same column list causes a specific failure: rows the new batch no longer contains are never deleted, because nothing in the batch matches their key. The delete key often needs to be **deliberately coarser** than the grain.

## The mechanism
Delete-then-insert clears stored rows whose key matches the *incoming batch's* keys, then inserts the batch. So a stored row is only removed if the batch still carries its key.

- Grain `(entity, document, attribute)`, delete key the same → the source reassigns `attribute` on an existing record. The new batch carries the new value, the delete clears only that, and the **old row survives forever alongside the new one**, double-counting the measure.
- Delete key `(entity, document)` → the batch clears *everything* stored for that entity/document and reinserts the pull's full set. The reassigned row is removed because its **parent** is in the batch, not because its own key is. The grain can still be the finer triple.

The instinct to make them identical is what produces orphans. The two questions are different: *what makes a row unique* versus *what scope does a batch authoritatively replace*.

## Choosing the delete key
- Take the **stable parent** of the grain — the part the source will not revise (a document, a voucher, an entity+period).
- It must cover every row the batch intends to replace, so a batch that legitimately drops a row still evicts it.
- The finer grain remains enforced separately, by a unique index or test, so uniqueness is not lost by coarsening the delete.
- In dbt this is exactly the split between `unique_key` (delete scope, need not be row-unique for `delete+insert`) and the model's own unique index.

## When it bites
- Any source that **corrects an attribute in place** — reassigned ownership, a relabelled category, a corrected reference.
- Any source that can **drop** a row it previously reported.
- Both are invisible to row-count checks: the table only grows, and the extra rows look like legitimate history.

## Related
[[A Constraint Is Only as Strong as Its Key]] · [[Duplicates Within One Load Are Real, Across Loads They Are Revisions]] · [[A Dedupe Key That Encodes an Assumption Deletes Data When the Assumption Is False]] · [[Idempotent-Skip as the Only Recovery Mechanism]] · [[Natural, Durable, and Supernatural Keys]] · [[Grain]]
