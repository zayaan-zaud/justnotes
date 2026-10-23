---
tags: [sql, postgresql, data-types, text, sorting]
source: [claude-conversation, 2026-08]
---
# A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text

When a date/timestamp value is stored as **text** rather than a real date/timestamp type, ordering is lexicographic string comparison, not chronological comparison — and those two usually agree, with one specific exception that inverts the rule people rely on.

## The rule and its inversion
The intuition "a timestamp sorts above a bare date" holds for any *earlier* date. It inverts on the **same calendar day**, because a bare date string is a literal string *prefix* of that day's timestamp string, and a prefix sorts **below** any string that extends it (a longer string with the same leading characters is "greater" byte-for-byte):

```sql
SELECT '2026-08-06' >= '2026-08-06 11:08:08.636254+05:00';  -- FALSE
SELECT '2026-08-07' >= '2026-08-06 11:08:08.636254+05:00';  -- TRUE
```

## Why this bites incremental loads
A common incremental-model pattern filters `WHERE stamp_col >= (SELECT MAX(stamp_col) FROM {{ this }})`. If one load stamps a bare date and a later, same-day load stamps a full timestamp (e.g. a revision/reprocessing pass that records the actual pull instant), the bare-date row's own comparison against the newer MAX silently evaluates false — that day's ordinary row is excluded from the incremental window and never reaches the model, with no error. Only that one calendar day is affected; the next day's date string sorts strictly above the stale timestamp and the model resumes normally.

## When to use
Any time a text column mixes bare dates and timestamps as its sort/watermark key (a `_pull_date`/`_loaded_at`-style column), and especially right after adding a second write path (a revision pass, a backfill, a re-processing job) that stamps a finer-grained value than the original path did. Check for this specific same-day gap before assuming an incremental model caught everything.

## Fix
Either promote the column to a real `timestamp`/`timestamptz` type (chronological comparison, no prefix trap), or ensure every writer to that text column stamps at the same, most granular resolution — never let some rows carry a coarser format than others.

## Related
[[Date-Time Types (PostgreSQL)]] · [[Character Types (PostgreSQL) - text vs varchar vs char]] · [[A Constraint Is Only as Strong as Its Key]] · [[Derive a Period From the Business Calendar, Not the Load Timestamp]]
