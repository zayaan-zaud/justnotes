---
tags: [dimension, advanced, behavior]
source: [Kimball ch.2 & 8]
---
# Aggregated Facts as Dimension Attributes

Users often want to **constrain or group by an aggregated measure** — "show me **high-value** customers", "sort by **lifetime spend** band". Rather than compute this at query time, store the **precomputed aggregate as an attribute** of the dimension (e.g., a "customer lifetime value band" on the customer dimension).

## Notes
- Makes common segmentation instantaneous and consistent.
- The attribute is **as-of** a point in time and must be **refreshed** by ETL; treat its change behavior as an [[Slowly Changing Dimensions|SCD]] decision (often Type 1 or [[Dynamic Value Banding|banded]]).
- Related Kimball behavior techniques: **behavior tag time series**, **behavior study groups** (capturing cohorts as a set of durable keys).

## Related
[[Dynamic Value Banding]] · [[Slowly Changing Dimensions]] · [[Numeric Values - Attribute or Fact]] · [[Behavior Study Groups]]
