---
tags: [dimension, etl, scd, advanced]
source: [Kimball ch.2 & 19]
---
# Late Arriving Dimensions

A **late arriving dimension** occurs when a **fact** arrives for a dimension member the warehouse **hasn't seen yet** (or before an updated version of the member arrives). ETL can't just drop the fact or leave a null [[Null Attributes in Dimensions|FK]].

## Handling
- Insert a **placeholder dimension row** for the unknown member (with the natural/durable key and "Unknown" attributes), assign it a surrogate key, and use it for the fact now.
- When the real dimension data later arrives, **update the placeholder** (Type 1 on those attributes) or, for **[[SCD Type 2 - Add New Row|Type 2]]**, insert history rows and back-fill the correct effective dating.
- The mirror problem — a fact arriving late for an **existing Type 2 member** — is **[[Late Arriving Facts]]**: look up the surrogate key **in effect at the event date**.

## Related
[[Late Arriving Facts]] · [[SCD Type 2 - Add New Row]] · [[Natural, Durable, and Supernatural Keys]] · [[ETL 34 Subsystems]]
