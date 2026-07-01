# Project — Innovation with Purpose (IWP)

**Role:** Apprentice Engineer — Cross-BU Contributor (outside Tax & Treasury)
**Started:** June 2026 (ongoing)
**Status:** Active prototype development — FITGO Customer Journey Analytics (Neo4j)

---

## What Is This Project

Innovation with Purpose is a BU-wide initiative at Fidelity focused on customer analytics and path-based intelligence. The specific initiative is the **FITGO Customer Journey Analytics** project — understanding how customers move through their financial journeys after joining FITGO, which products they adopt, how relationships grow, and what paths are most valuable to the business.

This project sits outside the Tax & Treasury squad's day-to-day sprint work. It operates as a collaborative, cross-team effort with decisions made by consensus rather than assigned top-down.

---

## Business Problems Being Solved

| Problem Area | Description |
|---|---|
| Customer Retention | Identify customers likely to churn before they leave |
| Product Adoption | Track how and when customers adopt new financial products after FITGO |
| Growth Analysis | Understand factors driving growth — recurring investments, asset milestones, relationship depth |
| Customer Segmentation | Slice customers by generation (Gen Z, Millennials), asset brackets, and lines of business (401k, HSA, FITGO, Wealth) |
| Journey Modeling | Represent how customers evolve year-over-year by combining financial activity + interaction data |
| Path Analytics | Uncover the most common and most valuable customer progression sequences — not just idealized funnels |
| Workplace Customer Behavior | Understand how workplace customers behave before and after joining FITGO |

---

## Architecture Decisions (Chronological)

### June 16 — Initial Scoping
- First exposure to the project during a BU-wide session on customer analytics
- Key concepts introduced: customer journey modeling, process mining, segmentation-driven insights
- Customer journeys modeled as timeline-based streams combining:
  - **Financial activity** — portfolio changes, product adoption
  - **Interaction data** — clicks, calls, app usage, branch visits
- Process mining approach discussed: ingest raw event data → graph-based algorithms → interactive visualization for business users
- Segmentation (generation, asset bracket, line of business) identified as the primary lens for personalized insights

### June 18 — Architecture Finalized (Round 1)
- Internal collaboration session to lock in project structure
- **Decision:** Prototyping approach with parallel tracks — allows workstreams to progress independently
- **Data model:** Snowflake schema as the initial approach — clean and queryable without additional transformation layers
- **Visualization:** Python-based
- **Ruled out:** An additional intermediate data modeling layer — would have forced schema-wide column changes and added unnecessary complexity
- Stack settled on: **Snowflake + Python visualization**

### June 26 — Architecture Pivot (Round 2)
- Team reconsidered the Snowflake-only approach after deeper discussion on path analytics requirements
- **Decision:** Proceed with **Neo4j** (graph database) for path-based customer analytics
- Rationale: Graph databases model customer journeys as nodes and edges — far more natural for sequence queries than relational schemas
- Neo4j enables graph traversal queries to find common paths without complex multi-table joins
- Project moved from scoping into active implementation phase

### June 30 — Graph Data Model & Prototype Plan Finalized
- Detailed design session focused on FITGO Customer Journey Analytics
- **Node types confirmed:** `Person`, `Person-Year`, `Line of Business (LOB)`, `Product`, `Account Type`, `Managed Account Type`
- **Design principle:** Separate customer identity (`Person`) from customer state by year (`Person-Year`) — relationships between year nodes represent transitions and enable path traversal
- **Managed account simplification:** Grouping detailed types into broader categories — Fidelity Go, Advisory Programs, SMA (Separately Managed Accounts), FITFOLIO — to reduce model complexity while preserving business insight
- **Prototype approach:** Sample-first — start with 100–500 records, validate relationships, build KPI views, then scale
- **Visualization:** Business-friendly UI consuming Cypher query outputs — simpler, explainable views preferred over complex graph renderings
- **Dashboard structure:** Page 1 = KPI summaries (starting point, ending point, relationship growth, account growth, popular combinations, most common journeys); subsequent pages = deep-dive path analytics

---

## Current Tech Stack

| Layer | Tool / Approach |
|---|---|
| Graph Database | Neo4j |
| Query Language | Cypher |
| Data Model | Nodes: Person, Person-Year, LOB, Product, Account Type, Managed Account Type |
| Relationships | Year-over-year transitions between Person-Year nodes; account/product ownership edges |
| Analytics Focus | FITGO customer path analytics — most common and most valuable progression sequences |
| Visualization | UI layer consuming Neo4j/Cypher outputs — business-friendly KPI summaries + path exploration |

---

## Neo4j Graph Data Model

```
(Person)-[:HAS_YEAR]->(Person-Year)
(Person-Year)-[:NEXT_YEAR]->(Person-Year)   ← year-over-year transitions = journey paths
(Person-Year)-[:HAS_LOB]->(Line of Business)
(Person-Year)-[:OWNS]->(Account Type)
(Account Type)-[:IS_TYPE]->(Managed Account Type)
(Person-Year)-[:HOLDS]->(Product)
```

**Managed Account Type groupings:**
- Fidelity Go
- Advisory Programs
- SMA (Separately Managed Accounts)
- FITFOLIO

**Key business question answered by graph traversal:**
> After joining FITGO, what account types do customers most commonly add, and which paths lead to the strongest relationship growth?

---

## Prototype Development Sequence

| Step | Activity |
|---|---|
| 1 | Use sample datasets (100–500 records) before scaling |
| 2 | Load datasets into Neo4j |
| 3 | Validate customer journey relationships |
| 4 | Build initial KPI views |
| 5 | Develop path analytics visualizations |
| 6 | Compare insights before loading larger datasets |

---

## Key Concepts Learned Through This Project

**Customer Journey Modeling**
- Customer evolution represented as year-over-year node transitions — `Person-Year` nodes connected by `:NEXT_YEAR` relationships
- Interaction patterns actively influence product adoption and retention — financial activity and interaction data must be modeled together
- Segmentation (generation, asset bracket, line of business) is the lens for personalized insight, not just a reporting filter

**Process Mining**
- Differs from manual analysis: raw event data → graph-based algorithms → automatic pattern discovery
- Captures actual customer paths (including unexpected ones) rather than idealized funnels defined upfront
- Output is interactive visualization — business users can explore journey paths without writing queries

**Graph Databases (Neo4j) & Cypher**
- Natural fit for path analytics — customers, products, and account types as nodes; transitions and ownership as edges
- Cypher query language expresses graph patterns naturally (ASCII-art syntax) — more readable than equivalent SQL joins
- Relationships are first-class objects in Neo4j — traversal queries find "most common path" in a way relational databases cannot efficiently replicate
- `Person-Year` node design is the graph equivalent of SCD Type 2 — historical state as explicit nodes rather than effective-date rows

**KPI and Dashboard Design for Business Users**
- Business users prioritize: starting point, ending point, relationship growth, account growth, popular paths — not raw graph structure
- Layered page structure: KPI summary first, deep-dive path analytics on subsequent pages
- Simpler, explainable visualizations preferred — the graph backend handles complexity, the UI should feel accessible

**Architecture Decision-Making**
- Simplicity at the data model level compounds downstream — extra layers multiply maintenance cost
- Sample-first prototyping (100–500 records) validates relationships before scaling — same discipline as testing with dummy files before production
- Technology choice should follow the query pattern: relational for aggregations, graph for traversals

---

## My Contributions

| Date | Contribution |
|---|---|
| June 16 | Attended BU-wide session; built conceptual understanding of customer journey modeling and process mining; practiced framing business problems in analytics terms |
| June 18 | Active participant in architecture discussion; contributed to the decision to reject the extra modeling layer; aligned on Snowflake + Python as the initial approach |
| June 26 | Participated in architecture pivot discussion; aligned on Neo4j as the implementation path; began orienting toward graph data modeling concepts |
| June 30 | Attended FITGO design session; reviewed and absorbed the full Neo4j node/relationship schema; understood prototype development sequence and Cypher as the next technical step |

---

## Open Questions

- What does the sample dataset (100–500 records) look like — is it synthetic/anonymized or real customer data, and what format does it come in before Neo4j loading?
- What are the specific Cypher queries the team plans to use for path traversal and KPI aggregation?
- What are my specific implementation responsibilities for the prototype (data loading, KPI design, visualization, or query writing)?
- What data sources feed into the graph model — where does FITGO enrollment, account ownership, and interaction history live at Fidelity?

---

## Next Steps

- Get Neo4j environment ready
- Review sample datasets and understand their structure
- Learn Cypher queries used by the team for journey path retrieval
- Support prototype development: data loading → relationship validation → KPI views → path analytics visualizations
- Continue contributing to FITGO customer path analytics use cases
