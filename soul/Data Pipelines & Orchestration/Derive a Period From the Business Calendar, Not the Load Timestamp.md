---
tags: [etl, data-quality, scheduling, snapshots]
source: [claude-conversation, 2026-09-08 — rin monthly schedule model]
---
# Derive a Period From the Business Calendar, Not the Load Timestamp

A load timestamp records **when data arrived**, not **which period it describes**. The two coincide often enough — a month's file usually lands near its month end — to look like a usable proxy, and then diverge exactly when a file is late, backfilled, or re-ingested. Any logic that infers a reporting period from a load stamp is one late delivery away from computing the wrong period.

## How it fails
Deriving "the next period to build" as *the earliest load stamp after the last built period*:

- monthly files normally land on their own month end, so the rule appears correct
- one month's file is delivered late — loaded on the 10th of the following month
- the rule now picks that load stamp, treats a **prior** period's data as the next period, and builds a month labelled with a date that is neither: a period stamped with a load date, containing the previous month's figures

It fails quietly. The output has plausible row counts and real amounts; only the labelled period and the small number of "new" records give it away.

## The rule
- Compute the period **arithmetically** from the last period on record — next month end after the previous month end.
- Then **check availability separately**: does the source for that period actually exist, keyed the same way every downstream filter keys it? If not, build nothing.
- Keep those two steps distinct. Fusing them ("the next period is whatever arrived next") is precisely the conflation that fails.

## Related
[[Duplicates Within One Load Are Real, Across Loads They Are Revisions]] · [[A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text]] · [[Catchup Replays Missed Schedules, Not Failed Runs]] · [[Idempotent-Skip as the Only Recovery Mechanism]] · [[Round Inferences Toward the Cheap Error]]
