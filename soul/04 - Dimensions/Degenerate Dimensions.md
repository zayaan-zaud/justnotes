---
tags: [dimension, fact-table]
source: [Kimball ch.2-3, Adamson ch.6 & 11]
---
# Degenerate Dimensions

A **degenerate dimension (DD)** is a dimension key that lives **in the fact table** with **no associated dimension table** — because it has no attributes of its own worth storing separately. Written as `DD` in Kimball diagrams.

## Typical examples
Operational transaction identifiers: **order number, invoice number, ticket/receipt number, tracking number**. These are useful for grouping line items of the same transaction and for drill-through to the source, but they carry no descriptive context, so building a dimension table for them would be pointless.

## Uses
- **Group** all fact rows belonging to one transaction (e.g., all lines on an order).
- Support **audit / drill-back** to the operational system.
- Serve as the natural grouping key in [[Header-Line Fact Tables]].

## Note
If several such identifiers accumulate and you *do* have a few attributes, consider consolidating them into a [[Junk Dimensions|junk dimension]] instead.

## Related
[[Header-Line Fact Tables]] · [[Junk Dimensions]] · [[Transaction Fact Tables]] · [[Fact Tables]] · [[LATERAL Joins]]
