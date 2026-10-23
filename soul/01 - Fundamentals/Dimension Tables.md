---
tags: [dimension, fundamentals]
source: [Kimball ch.1-2, Adamson ch.1 & 3 & 6]
---
# Dimension Tables

**Dimension tables** hold the textual, descriptive **context** that gives meaning to the numbers in a [[Fact Tables|fact table]]. They answer *who, what, where, when, why, how*. Dimensions are the entry points for filtering, grouping, labeling, and drilling.

## Anatomy
- A single-column **[[Dimension Surrogate Keys|surrogate primary key]]**.
- Many **attributes** (often 50–100+): verbose, descriptive, discrete text values used as row/column headers and filters.
- Usually the **[[Natural, Durable, and Supernatural Keys|natural key]]** from the source system, kept as an attribute.

## Principles
- **Wide and denormalized.** Flatten hierarchies into the dimension rather than [[Snowflaked Dimensions|snowflaking]]. Redundancy is acceptable in exchange for simplicity and speed.
- **Verbose, decoded attributes.** Replace cryptic codes with full descriptive text; use [[Flags and Indicators as Textual Attributes|meaningful words instead of Y/N flags]]. Attribute quality *is* BI quality — reports are only as good as their dimension labels.
- **Fewer rows, many columns** — the opposite shape of a fact table.
- **No nulls in keys**; use surrogate "Unknown"/"Not Applicable" rows instead.

## What dimensions enable
- **Browsing** — exploring the distinct values of an attribute.
- **Grouping/labeling** — "sales by *district* by *product brand*."
- **[[Dimension Hierarchies|Drilling down]]** — adding row headers to reveal more detail.

## Change over time
Attributes change; how you handle that is governed by [[Slowly Changing Dimensions]] (Types 0–7).

## Related
[[Dimension Surrogate Keys]] · [[Slowly Changing Dimensions]] · [[Conformed Dimensions]] · [[Dimension Hierarchies]] · [[Role-Playing Dimensions]] · [[Junk Dimensions]]
