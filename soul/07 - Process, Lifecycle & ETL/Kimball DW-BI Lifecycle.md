---
tags: [process, lifecycle, kimball, moc]
source: [Kimball ch.17-18 & 20]
---
# Kimball DW/BI Lifecycle

The **Kimball Lifecycle** (a.k.a. "Business Dimensional Lifecycle") is the end-to-end roadmap for planning, designing, building, and deploying a DW/BI system. It organizes work into three concurrent tracks that fan out from requirements and converge at deployment.

## Backbone
1. **Program/Project Planning** and **Program/Project Management** — scope, justification, staffing.
2. **Business Requirements Definition** — the central driver feeding all three tracks.

## The three parallel tracks
- **Technology track:** technical architecture design → product selection & installation.
- **Data track:** **[[Dimensional Modeling]]** → physical design → **[[ETL 34 Subsystems|ETL design & development]]**.
- **BI Application track:** BI application design → BI application development.

The tracks converge at **Deployment**, followed by **Maintenance** and **Growth** (iterate back to requirements — the lifecycle is a loop, not a line).

## Guiding themes
- **Focus on the business** and its requirements above all.
- Deliver in **incremental, business-process-sized** chunks on the **[[Enterprise Data Warehouse Bus Architecture|bus]]**.
- Build **atomic** data first; add [[Aggregate Fact Tables|aggregates]] and cubes for performance.

## Related
[[Dimensional Modeling Process and Tasks]] · [[ETL 34 Subsystems]] · [[Enterprise Data Warehouse Bus Matrix]] · [[Collaborative Dimensional Modeling Workshops]]
