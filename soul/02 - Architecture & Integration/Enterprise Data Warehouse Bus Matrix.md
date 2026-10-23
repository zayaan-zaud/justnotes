---
tags:
  - architecture
  - integration
  - planning
source:
  - Kimball ch.2 & 4
---
# Enterprise Data Warehouse Bus Matrix

The **bus matrix** is the primary planning and communication tool of the [[Enterprise Data Warehouse Bus Architecture]]. It is a simple grid that captures the entire enterprise DW plan on a single page.

## Structure
- **Rows** = [[Business Process (Measurement Event)|business processes]] (order, ship, invoice, inventory, ...), each a candidate fact table.
- **Columns** = **[[Conformed Dimensions]]** (date, product, customer, store, employee, ...).
- **Cells** = an "X" where a dimension applies to a process.

## Why it's powerful
- **Plans the whole warehouse** while allowing **incremental**, process-by-process delivery.
- Reveals which dimensions are **shared** — those must be conformed and built once.
- Doubles as a **communication and prioritization** artifact for business and IT stakeholders.

## Variants
- **Detailed implementation bus matrix** — expands each process row to show its actual grain and facts.
- **Opportunity/stakeholder matrix** — swaps dimension columns for business-group columns to show who cares about each process (used for prioritization).

## Related
[[Enterprise Data Warehouse Bus Architecture]] · [[Conformed Dimensions]] · [[Value Chain]] · [[Four-Step Dimensional Design Process]] · [[Kimball DW-BI Lifecycle]]
