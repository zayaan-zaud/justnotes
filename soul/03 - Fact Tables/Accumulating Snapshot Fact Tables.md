---
tags: [fact-table, grain, snapshot, pipeline]
source: [Kimball ch.2 & 5-6, Adamson ch.11]
---
# Accumulating Snapshot Fact Tables

**Grain = one row per instance of a pipeline / workflow process with a well-defined beginning and end** (one row per order, per claim, per mortgage application). The row is **revisited and updated** as the item advances through its milestones.

## Distinctive features
- **Multiple [[Role-Playing Dimensions|date foreign keys]]** — one per milestone in the pipeline (order date, shipment date, delivery date, payment date...). These are role-playing views of the [[Calendar Date Dimension]]. Undefined future milestones point to a special "not yet / unknown" date row.
- **Rows are UPDATED in place** as milestones complete — unusual for fact tables, which are usually insert-only.
- **Lag / duration facts** measure elapsed time between milestones (see [[Lag and Duration Facts]]).

## When to use
- Processes with a **defined series of steps** where you want to track progress and measure time/efficiency **between** steps (order fulfillment, claims processing, hiring).

## The three grains together
[[Transaction Fact Tables|Transaction]], [[Periodic Snapshot Fact Tables|periodic snapshot]], and accumulating snapshot are **complementary** — many pipeline processes justify all three.

## Related
[[Fact Tables]] · [[Lag and Duration Facts]] · [[Role-Playing Dimensions]] · [[Calendar Date Dimension]] · [[Transaction Fact Tables]] · [[Periodic Snapshot Fact Tables]] · [[BRIN Index]]
