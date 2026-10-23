---
tags: [etl, incremental-load, dbt, idempotency, data-quality]
source: [claude-conversation, 2026-10-05 — rin late-filled ledger days]
---
# An Incremental Watermark Must Track When Data Arrived, Not the Period It Describes

An incremental model that filters `WHERE stamp >= (SELECT MAX(stamp) FROM {{ this }})` assumes new data always carries a newer stamp than anything already loaded. That holds only if the stamp measures **arrival**. If the stamp names the **period** the data describes (a report day, a window end), any batch that arrives late — after the model has already moved past its period — is never selected, and nothing errors.

## How it fails
- Daily loads are stamped with the report day. Day 7 fails or is delayed and lands on day 13.
- Meanwhile the model has run on days 8–12 and its `MAX(stamp)` is day 12.
- Day 7's rows (stamp = day 7) are `< MAX`, so every later run skips them. Permanently.
- Everything stays green: the load succeeded, the model succeeded, the unique index and grain test only describe rows that are present. The symptom shows up far downstream as a reconciliation that will not tie (here, ~2M untied for a month, because the ledger side was missing 13 days).

It is the same family as [[A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text]] (a watermark comparison silently excluding rows), but no type fix cures it: the column is simply the wrong clock.

## The fix: watermark on a completion time
- Keep a load registry written **after** the insert, with a completion timestamp per source file/load.
- Select raw rows whose load completed after the newest completion already staged, and carry that timestamp into the model (`_loaded_at`).
- Stamp at completion, not at the start of a batch: a long batch that finishes after a model run which already passed its start time would be missed all over again. Writing the registry row after the insert also makes a load invisible until its rows exist, so a crash between the two statements cannot lose it.
- Re-reading is harmless because delete+insert is idempotent ([[The Delete Key and the Grain Are Different Things]]).

## The guard that comes with it
A late load can carry an **older** period stamp than a row already in the model for the same key. Ordering the dedupe by that stamp would then let the older row replace the newer one. Refuse that explicitly: drop incoming rows when the target already holds the same key with a greater stamp ([[Duplicates Within One Load Are Real, Across Loads They Are Revisions]]).

## Ways *not* to fix it
- **Look-back window** (`>= max - N days`): fails silently once a fill is older than N. The gap that surfaced it was 14 days; any N is a guess.
- **Full refresh after each late fill**: correct, but manual, and you learn about the gap afterwards.

## Detect it, don't just prevent it
Add a completeness test that asks about **absence** directly: every key in the landing table must exist in the staged model (`raw EXCEPT staged` returns nothing). Compare key sets, not which pull won, so a superseded revision losing the dedupe is not a failure. Prove the test can fail by deleting a day from staging and watching it go red ([[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]]). Make it error severity if nothing else alerts you; a warning that nobody reads is the same silence ([[Write-Audit-Publish (WAP) Pattern]]).

## Gotchas
- A rebuild from the landing table drops rows the old incremental table kept after the landing copies were compacted away; expect a few stale revision rows to disappear.
- Adding the watermark column to an existing incremental model needs a one-time full refresh, and a baked deploy image must be rebuilt first or a scheduled run reverts the model.
- Rows with no registry record (legacy or manually loaded files) get a NULL watermark: a full refresh includes them, an incremental run never selects them.

## Related
[[A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text]] · [[Derive a Period From the Business Calendar, Not the Load Timestamp]] · [[The Delete Key and the Grain Are Different Things]] · [[Duplicates Within One Load Are Real, Across Loads They Are Revisions]] · [[A Constraint Is Only as Strong as Its Key]] · [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Write-Audit-Publish (WAP) Pattern]] · [[Idempotent-Skip as the Only Recovery Mechanism]]
