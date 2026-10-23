---
tags: [fact-table, advanced]
source: [Kimball ch.2 & 6]
---
# Multiple Units of Measure Facts

Different actors along a [[Value Chain]] view the same quantity in different units (a manufacturer thinks in pallets, the retailer in eaches/units). Rather than storing many pre-converted copies of every fact:

- Store the facts **once** in a single base unit of measure, and
- Store the **conversion factors** as additional facts in the same row.

Users (or the BI layer) multiply the base fact by the appropriate factor to view any unit, keeping the table compact and internally consistent.

## Related
[[Multiple Currency Facts]] · [[Conformed Facts]] · [[Value Chain]]
