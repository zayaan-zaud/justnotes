---
tags: [etl, deduplication, idempotency, data-quality, snapshots]
source: [claude-conversation, 2026-09-08 — rin claim-paid register re-grain]
---
# Duplicates Within One Load Are Real, Across Loads They Are Revisions

When an append-only landing table holds several rows that look alike, the load stamp separates the two possible causes cleanly: **several rows inside a single load are the source genuinely reporting several things**, while **the same row appearing across different loads is the source correcting itself**. Deduplicating without that distinction either destroys real detail or leaves stale corrections in place.

## The two shapes

| | rows per load stamp | across load stamps | correct handling |
|---|---|---|---|
| **Genuine multiplicity** | many, together in every load | stable | keep them all |
| **Revision** | one | attribute changes | keep the newest |

A source that re-exports its current state every pull will keep re-reporting a genuine split in each pull; it has no reason to. A correction, by contrast, only ever exists as a difference *between* pulls.

## Applying it
Restrict to each entity's own **latest load stamp**, then keep every row within it. That handles both cases with one rule: a revision resolves because the superseded value simply isn't in the latest load, and a genuine split survives intact because all its rows are.

The naive alternative — dedupe on some attribute across the whole table — has to guess which shape it is looking at, and guesses wrong in whichever direction the chosen attribute happens to favour.

## Gotchas
- **The load stamp has to be trustworthy and comparable.** If it is stored as text and mixes bare dates with timestamps, ordering breaks in the specific way described in [[A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text]].
- **A load stamp is when data was FETCHED, not the period it describes.** Don't reuse it as a business period — see [[Derive a Period From the Business Calendar, Not the Load Timestamp]].
- This rule diagnoses; it does not fix orphaning. Removing a superseded row from an incrementally-loaded table is a separate mechanism — see [[The Delete Key and the Grain Are Different Things]].
- Same-value groups confined to one load stamp are the signature of legitimate behaviour, and are worth checking *before* concluding a table has a duplicate problem at all.

## Related
[[A Dedupe Key That Encodes an Assumption Deletes Data When the Assumption Is False]] · [[The Delete Key and the Grain Are Different Things]] · [[Idempotent-Skip as the Only Recovery Mechanism]] · [[A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text]] · [[Slowly Changing Dimensions]]
