# Project — Innovation with Purpose (IWP)

**Role:** Apprentice Engineer — Cross-BU Contributor (outside Tax & Treasury)
**Started:** June 2026 (ongoing)
**Status:** Moving into implementation phase (Neo4j prototype)

---

## What Is This Project

Innovation with Purpose is a BU-wide initiative at Fidelity focused on customer analytics and path-based intelligence. The goal is to understand how customers move through their financial journeys — which products they adopt, when they churn, how interactions drive behavior — and surface those insights in a way that business users can actively explore.

This project sits outside the Tax & Treasury squad's day-to-day sprint work. It operates as a collaborative, cross-team effort with decisions made by consensus rather than assigned top-down.

---

## Business Problems Being Solved

| Problem Area | Description |
|---|---|
| Customer Retention | Identify customers likely to churn before they leave |
| Product Adoption | Track how and when customers adopt new financial products |
| Growth Analysis | Understand factors driving growth — recurring investments, asset milestones |
| Customer Segmentation | Slice customers by generation (Gen Z, Millennials), asset brackets, and lines of business (401k, HSA, FITGO, Wealth) |
| Journey Modeling | Represent how customers evolve over time by combining financial activity + interaction data |
| Path Analytics | Uncover the actual sequences customers take, including unexpected paths, not just idealized funnels |

---

## Architecture Decisions (Chronological)

### June 16 — Initial Scoping
- First exposure to the project during a BU-wide session on customer analytics
- Key concepts introduced: customer journey modeling, process mining, segmentation-driven insights
- Customer journeys modeled as timeline-based streams combining:
  - **Financial activity** — portfolio changes, product adoption
  - **Interaction data** — clicks, calls, app usage, branch visits
- Process mining approach discussed: ingest raw event data → graph-based algorithms → interactive visualization for business users
- Segmentation (generation, asset bracket, line of business) identified as the primary lens for personalized insights — not just a reporting filter

### June 18 — Architecture Finalized (Round 1)
- Internal collaboration session to lock in project structure
- **Decision:** Prototyping approach with parallel tracks — allows workstreams to progress independently
- **Data model:** Snowflake schema as the core (normalized dimension tables + fact) — clean and queryable without additional transformation layers
- **Visualization:** Python-based
- **Ruled out:** An additional intermediate data modeling layer — would have forced schema-wide column changes and added unnecessary complexity
- Stack settled on: **Snowflake + Python visualization**

### June 26 — Architecture Pivot (Round 2)
- Team reconsidered the Snowflake-only approach after deeper discussion on path analytics requirements
- **Decision:** Proceed with **Neo4j** (graph database) for path-based customer analytics
- Rationale: Graph databases model customer journeys as nodes and edges (customers, products, events, transitions) — far more natural for sequence queries than relational/snowflake schemas
- Neo4j enables graph traversal queries to find common paths (e.g., which journey leads to churn) without complex multi-table joins
- Project moved from scoping into active implementation phase

---

## Current Tech Stack

| Layer | Tool / Approach |
|---|---|
| Graph Database | Neo4j |
| Data Modeling | Graph nodes (customers, events, products) + edges (transitions, interactions) |
| Analytics Focus | Path-based customer journey tracking, sequence pattern analysis |
| Visualization | Python (to be implemented) |

---

## Key Concepts Learned Through This Project

**Customer Journey Modeling**
- Customer evolution represented as timelines merging financial activity + interaction data
- Interaction patterns actively influence product adoption and retention — the two data streams must be modeled together, not separately
- Segmentation (generation, asset bracket, line of business) is the lens for personalized insight, not just a reporting filter

**Process Mining**
- Differs from manual analysis: raw event data → graph-based algorithms → automatic pattern discovery
- Output is an interactive visualization business users can explore without writing queries
- Captures actual customer paths (including unexpected ones) rather than idealized funnels defined upfront

**Graph Databases (Neo4j)**
- Natural fit for path analytics — customers, events, and products as nodes; transitions as edges
- Enables sequence queries (e.g., "what path do churning customers most commonly take?") via graph traversal
- Outperforms relational databases for relationship-heavy, sequence-based queries at scale

**Architecture Decision-Making in Analytics Projects**
- Simplicity at the data model level compounds downstream — extra layers multiply maintenance cost
- Parallel tracks in a prototype allow uncertain workstreams to progress without blocking known ones
- Technology choice should follow the query pattern: relational for aggregations, graph for traversals

---

## My Contributions

| Date | Contribution |
|---|---|
| June 16 | Attended BU-wide session; built conceptual understanding of customer journey modeling and process mining; practiced framing business problems in analytics terms |
| June 18 | Active participant in architecture discussion; contributed to the decision to reject the extra modeling layer; aligned on Snowflake + Python as the initial approach |
| June 26 | Participated in architecture pivot discussion; aligned on Neo4j as the implementation path; began orienting toward graph data modeling concepts |

---

## Open Questions

- What does the raw event data look like for customer journey modeling at Fidelity — interaction logs, clickstream data, or something else? Where does it live?
- What data is already available in Snowflake / accessible for this project before prototyping begins?
- What are the node types, relationship types, and source data feeds for the initial Neo4j data model?
- What are my specific implementation responsibilities for the Neo4j prototype?

---

## Next Steps

- Begin contributing to the Neo4j-based prototype
- Understand the data model for path analytics (node/edge schema, source data)
- Align with team on implementation approach and individual responsibilities
- Connect business scenarios (churn, adoption, segmentation) to actual data sources and Neo4j queries
