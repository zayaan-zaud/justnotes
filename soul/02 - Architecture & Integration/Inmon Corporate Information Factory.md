---
tags: [architecture, inmon]
source: [Kimball ch.1, Adamson ch.2]
---
# Inmon Corporate Information Factory (CIF)

Bill Inmon's **hub-and-spoke** architecture, the main alternative to the [[Kimball DW-BI Architecture]].

## Shape
- Data flows from source systems into a **centralized, normalized (3NF) enterprise data warehouse** — the atomic, integrated "single version of truth" hub.
- **Departmental data marts** (often dimensional, sometimes with summary data) are then built *downstream* as the spokes for user access.

## Contrast with Kimball
| | Inmon CIF | Kimball |
|---|---|---|
| Central store | Normalized 3NF EDW | Dimensional presentation area |
| User access | Via downstream marts | Directly against conformed stars |
| Integration | In the normalized hub | Via the [[Enterprise Data Warehouse Bus Architecture|bus]] + [[Conformed Dimensions]] |
| Atomic data | In the 3NF hub | In the dimensional presentation area |

## Adamson's view
Adamson treats architecture as largely **orthogonal** to dimensional design: whichever camp you follow, the **[[Star Schema|dimensional star]]** appears (as the marts in CIF, or as the presentation area in Kimball). The dimensional design techniques in his book apply to both.

## Hybrid
A common real-world **hybrid** keeps a normalized EDW *and* delivers atomic dimensional data via the bus — see [[Kimball DW-BI Architecture]].

## Related
[[Kimball DW-BI Architecture]] · [[Independent Data Marts]] · [[Conformed Dimensions]]
