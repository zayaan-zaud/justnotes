---
tags: [integration, query-technique, conformed]
source: [Kimball ch.2 & 7, Adamson ch.5]
---
# Drilling Across

**Drilling across** means combining measurements from **two or more fact tables** into a single result, aligned on their **[[Conformed Dimensions|conformed dimensions]]**. It's how the [[Enterprise Data Warehouse Bus Architecture|bus architecture]] pays off analytically.

## The technique (crucial detail)
Do **not** join fact tables directly to each other — that produces wrong answers (fan-out / double counting from many-to-many relationships through shared dimensions).

Instead use **multipass / phased aggregation**:
1. Query each fact table **separately**, grouping by the **common conformed dimension attributes** and aggregating its facts.
2. Perform a **full outer join** of the separate result sets **on the shared dimension labels** (a "sort-merge" on conformed keys/labels).
3. Combine/compare the aligned facts (e.g., compute variance = actual − forecast).

Adamson calls this the "drill-across" or "multiple-pass" approach; Kimball calls it **multipass SQL to avoid fact-to-fact joins**.

## Requirements
- The dimensions used to align must be **conformed** (identical or subset-conformed).
- Facts being compared should be **[[Conformed Facts|conformed]]** (or at least understood as distinct).

## Related
[[Conformed Dimensions]] · [[Conformed Facts]] · [[Multipass SQL & Fact-to-Fact Joins]] · [[Consolidated Fact Tables]] · [[Enterprise Data Warehouse Bus Architecture]]
