---
tags: [dimension, date, fundamentals]
source: [Kimball ch.2-3, Adamson ch.6]
---
# Calendar Date Dimension

The **date dimension** is the one dimension present in virtually **every** dimensional model, and it is nearly always the first one built. Despite dates being computable, you build an explicit table because it holds attributes SQL can't derive.

## Why a table, not a SQL date function
It stores rich, **verbose calendar attributes** that can't be computed easily: day-of-week name, day/week/month/quarter numbers, month name, fiscal periods, holiday flags, weekday/weekend flags, season, selling periods, "last day of month" flags. These power filtering, grouping, and readable report labels.

## Design points
- **Grain** = one row per day (a separate time-of-day dimension handles finer resolution; don't put minutes in the date dimension).
- Use a **meaningful but still surrogate** key (often `yyyymmdd` integer is used, but Kimball recommends a plain sequential surrogate; either way a real key, not a raw date).
- Include special rows for **"date unknown / not yet"** (for [[Accumulating Snapshot Fact Tables|open milestones]]).
- Frequently a **[[Role-Playing Dimensions|role-playing dimension]]** (order/ship/due dates).
- Multiple calendars (fiscal, manufacturing) are added as extra attributes or an [[Outrigger Dimensions|outrigger]].

## Related
[[Role-Playing Dimensions]] · [[Dimension Tables]] · [[Accumulating Snapshot Fact Tables]] · [[Dimension Hierarchies]]
