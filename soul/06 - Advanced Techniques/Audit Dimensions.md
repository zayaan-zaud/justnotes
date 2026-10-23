---
tags: [dimension, etl, data-quality, lineage, advanced]
source: [Kimball ch.2 & 19]
---
# Audit Dimensions

An **audit dimension** attaches **ETL lineage and data-quality metadata** to fact rows as if it were regular context. When a fact row is loaded, ETL tags it with an audit-dimension key describing **how and when** that row was produced.

## Typical attributes
- ETL batch/run identifier and load timestamp,
- source-system/version,
- **data-quality indicators** (e.g., "clean" vs. "contained corrected value"), completeness flags,
- environment (test/prod).

## Value
- Lets analysts **filter or flag** rows by quality/provenance and supports compliance/audit questions ("show only fully clean rows"; "which load produced this?").
- Fed by the **[[Error Event Schema]]** and the cleaning/conforming ETL subsystems.

## Mark superseded, never delete
When a load/run is superseded by a later, wider reload of the same data, the audit-dimension instinct applies to the run-log row itself, not just to fact rows: **mark** it superseded (a pointer to whatever replaced it) rather than deleting it. Deleting it alongside its data rows destroys the lineage evidence and makes a naive reconciliation blind to data loss — see [[A Constraint Is Only as Strong as Its Key]] for the concrete failure this causes.

## Related
[[Error Event Schema]] · [[ETL 34 Subsystems]] · [[Dimension Tables]] · [[A Constraint Is Only as Strong as Its Key]]
