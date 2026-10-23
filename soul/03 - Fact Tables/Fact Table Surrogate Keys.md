---
tags: [fact-table, keys, advanced]
source: [Kimball ch.2 & 19]
---
# Fact Table Surrogate Keys

Unlike a [[Dimension Surrogate Keys|dimension surrogate key]], a **fact table surrogate key** is a single meaningless sequential key assigned to **each fact row**. Fact tables usually identify rows by their combination of dimension FKs, so this is optional — but it earns its keep in several ETL situations.

## Benefits
- **Single-row identification** — lets ETL update or delete one specific fact row (e.g., to back out an error) without a composite predicate.
- **Resumable loads** — if a bulk load fails midway, the highest surrogate key marks where to resume, avoiding duplicate or missing rows.
- **Support for updates** — helpful with [[Accumulating Snapshot Fact Tables]] and corrections.
- Can act as a **parent key** linking a fact row to related detail (e.g., audit or drill-through rows).

## Related
[[Dimension Surrogate Keys]] · [[Accumulating Snapshot Fact Tables]] · [[ETL 34 Subsystems]]
