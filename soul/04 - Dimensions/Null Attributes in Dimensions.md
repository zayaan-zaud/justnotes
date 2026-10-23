---
tags: [dimension, data-quality, best-practice]
source: [Kimball ch.2-3]
---
# Nulls in Dimensions and Fact Foreign Keys

## Dimension attributes
Avoid null **attribute values**; nulls confuse users and behave inconsistently across databases and BI tools (grouping, filtering). Substitute a descriptive constant like **"Unknown"** or **"Not Applicable"** so reports label the condition clearly.

## Fact-table foreign keys
**Never** allow a null in a fact-table **foreign key** — a null FK breaks the join and silently drops rows. Instead point to a **special dimension row** (surrogate key such as −1) meaning "Unknown / Not Applicable / Not Yet". This is also how [[Accumulating Snapshot Fact Tables|open milestone dates]] are handled.

## Fact measures
Nulls in **fact measures** are acceptable — SQL aggregations correctly ignore them (unlike zero, which would distort averages). Don't substitute zero for a genuinely missing measurement.

## Related
[[Dimension Surrogate Keys]] · [[Flags and Indicators as Textual Attributes]] · [[Additive, Semi-Additive, and Non-Additive Facts]]
