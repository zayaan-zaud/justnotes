---
tags: [fact-table, advanced, time, state]
source: [Kimball ch.2 & 8]
---
# Timespan Tracking in Fact Tables

Adding **effective** and **expiration** timestamps (and a "current row" flag) to fact rows so you can reconstruct the **state of the business at any point in time** — analogous to [[SCD Type 2 - Add New Row|Type 2]] tracking but applied to facts. Useful when fact rows themselves are superseded/updated and you must query "as of" a moment.

## Related
[[Accumulating Snapshot Fact Tables]] · [[SCD Type 2 - Add New Row]] · [[Late Arriving Facts]]
