---
tags: [sql, postgresql, data-types, datetime]
source: [PostgreSQL 18 docs — Date/Time Types]
---
# Date-Time Types (PostgreSQL)

The single most consequential choice in this family is `timestamp` vs `timestamptz`, and it is not about whether you "need timezones" — it's about whether the column represents a moment in time or a wall-clock reading.

## The types
- `date` — day only, no time.
- `timestamp` (without time zone) — date + time, stored exactly as typed. Any timezone offset in the input is silently discarded.
- `timestamptz` (with time zone) — date + time, **always stored internally as UTC**. Display is converted to whatever the session's `TimeZone` parameter is — the original input timezone is never preserved, only the instant.
- `interval` — a duration, stored as three **separate integer fields**: months, days, microseconds — not one collapsed number. This is deliberate: it's the only representation that survives "add 1 month" landing on a different day-count month, or a day that crosses a DST transition.

## The `timestamptz` model, precisely
Storage is always UTC regardless of input offset — `'2024-06-15 12:00:00-04:00'`, `'...16:00:00+00:00'`, and `'...18:00:00+02:00'` all store as the identical UTC instant. What varies is display: the same stored value prints differently depending on the querying session's `TimeZone` setting. This means **`AT TIME ZONE 'X'` converts FROM the session's current zone TO `X`**, not from whatever zone the data was originally entered in — that original zone is gone the moment it's stored, unless you keep it in a separate column.

## When to use which
- **`timestamptz` for anything that happened** — event timestamps, audit trails, "when was this posted/loaded/reversed." The exact instant is what matters, and it stays correct across DST and across readers in different timezones.
- **`timestamp` (or `date`+`time`) for wall-clock schedule** — "this report runs at 9am local," where the *local* time is the meaning, not a fixed instant. Converting this to `timestamptz` would make it drift relative to what it's meant to represent whenever DST shifts.
- Never assume a `timestamp` column preserves a source system's timezone — if that matters, store the zone in its own text column alongside it.
- ISO 8601 input (`'1999-01-08 04:05:06-8'`) is unambiguous; verbose formats (`'January 8, 1999'`) depend on the session's `DateStyle` and are a source of exactly the DD/MM-vs-MM/DD-style parsing bugs that show up when a source system's date format assumption doesn't match Postgres's.

## Related
[[Numeric Types (PostgreSQL)]] · [[A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text]] · [[Dispatch an Ambiguous Format Parse on Shape, Not on Which Attempt Succeeds]]
