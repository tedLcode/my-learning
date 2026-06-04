# Task Summary — ORDS ServiceNow INC Data Access Validation

**Role:** Apprentice Engineer — FMT Tax & Treasury, Fidelity
**Duration:** April – May 2026
**Story/Task:** Access for `srvAP178041Snownp` — ServiceNow INC Data Validation

---

## What Was the Task

Validated that ServiceNow (SNOW) incident data is accessible through the enterprise-approved internal data platform (ORDS) for the FMT Tax & Treasury team, without using direct ServiceNow GET APIs which are restricted by enterprise governance.

---

## What I Did

| # | Activity |
|---|----------|
| 1 | Researched enterprise data access governance — understood why direct ServiceNow APIs are not used and how ORDS/TMDL are the approved alternatives |
| 2 | Submitted ACR (Access Control Request) via MyAccess for service account `srvAP178041Snownp` for both UAT and PROD environments |
| 3 | Authored internal technical documentation for pgAdmin-based ORDS connectivity (connection setup, query guide, troubleshooting, common mistakes) |
| 4 | Authored internal technical documentation for Python-based ORDS connectivity using `psycopg2` |
| 5 | Executed end-to-end validation in pgAdmin — connected to ORDS PostgreSQL, discovered incident views, queried `itsm_incident_ticket_v`, verified live INC data |
| 6 | Identified and mapped 87 columns in the incident view relevant to INC data validation |
| 7 | Confirmed near real-time data freshness by querying incidents opened within the last 7 days |

---

## Technologies & Tools Used

- **pgAdmin 4** (v5.4+) — PostgreSQL GUI client
- **PostgreSQL / Amazon RDS Aurora** — ORDS database engine
- **Python / psycopg2** — programmatic DB connectivity
- **ServiceNow** — source system for incident data
- **MyAccess (ACR)** — enterprise access control platform
- **Kerberos / SSO** — enterprise authentication
- **Confluence** — reference documentation research
- **Markdown** — internal documentation authoring

---

## Outcome

- Service account successfully provisioned with read-only access to ORDS in both UAT and PROD
- Confirmed `itsm_incident_ticket_v` view returns live ServiceNow incident data
- Delivered two reusable internal documentation guides (pgAdmin + Python) for the team
- Validated data freshness — ORDS reflects near real-time ServiceNow data
- Story closed and handed over to Saravana with full validation evidence

---

## Skills Demonstrated

- Enterprise data governance awareness
- PostgreSQL querying and database navigation
- Internal technical documentation writing
- Access management process (ACR/MyAccess)
- End-to-end ownership of a task from research → access → validation → documentation

---

## Resume One-Liner

> *Validated ServiceNow incident data access via enterprise PostgreSQL data store (ORDS/Amazon RDS), authored internal configuration guides for pgAdmin and Python connectivity, and completed end-to-end data validation for the FMT Tax & Treasury team at Fidelity.*
