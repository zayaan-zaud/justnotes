---
tags: [data-engineering, parsing, data-quality]
source: [claude-conversation, 2026-08]
---
# Dispatch an Ambiguous Format Parse on Shape, Not on Which Attempt Succeeds

When one column mixes two date/text formats (e.g. some rows are ISO `2026-04-01`, others are locale-formatted `01/04/2026`), a `COALESCE(try format A, try format B)`-style fallback chain looks safe but isn't, because the two formats can both parse a given string **successfully** while disagreeing on its meaning.

## Why the fallback silently picks the wrong branch
A value like `01/04/2026` is ambiguous between DD/MM and MM/DD. Under a session/locale defaulting to MM/DD, that string parses without error as "January 4th" — a real, valid date, just the wrong one. Because the first attempt in the fallback chain didn't raise, the fallback branch is never reached at all. The bug produces a plausible, silently-wrong value on exactly the subset of dates where day ≤ 12 (so both readings are valid) — it does not show up as a crash, a NULL, or an out-of-range date, which is what makes it easy to ship unnoticed.

## Fix
Dispatch on the **string's own shape** first (e.g. `^[0-9]{4}-` for ISO), then parse with the format that shape implies — never dispatch by trying formats in sequence and keeping whichever one didn't throw. A cast is "succeeded without erroring" is not the same claim as "parsed correctly."

## When to use
Any column whose values were typed by more than one path into the same field (e.g. a spreadsheet cell that's sometimes a real date type serialized as ISO and sometimes free-typed text in a locale format) — this is common in Excel-sourced data, where a date-typed cell and a text-typed cell in the same column serialize completely differently even though they display the same in the UI.

## Related
[[Classify the Response Body, Never the Status Code]] · [[Date-Time Types (PostgreSQL)]] · [[Round Inferences Toward the Cheap Error]]
