# Task Summary — DDL Updates for MQ Producer/Consumer Activity Audit

**Role:** Apprentice Engineer — FMT Tax & Treasury, Fidelity
**Duration:** June 2026
**Story/Task:** TRETAX-11923 — DDL Updates for MQ Activity Audit Tables

---

## What Was the Task

The Treasury BDI platform needed its MQ audit infrastructure updated to support both producer and consumer applications separately. Previously, a single generic audit task entry existed for MQ activity. The requirement was to rename existing DDL objects to be producer-specific and create metadata entries for both producer and consumer, enabling independent audit logging with proper foreign key references.

---

## What I Did

| # | Activity |
|---|----------|
| 1 | Explored the MQ Producer Java application codebase to understand the end-to-end message flow (S3 → MQ → DB audit) |
| 2 | Mapped the full foreign key chain: `bdi_mq_activity_audit` → `bdi_load_audit` → `bdi_task` → `bdi_task_type` |
| 3 | Connected to DEV PostgreSQL via pgAdmin and queried existing metadata to identify current state and duplicates |
| 4 | Renamed DDL objects (sequence, table, column, PK/FK constraints) from generic `MQ_ACTIVITY_AUDIT` to producer-specific `MQ_PRODUCER_ACTIVITY_AUDIT` |
| 5 | Created an ALTER migration script with `IF EXISTS` guards for safe execution across DEV/UAT environments |
| 6 | Rewrote the DML seed script (`INS_BDI_MQ_ACTIVITY_AUDIT.sql`) to be production-ready — inserts task type + producer task + consumer task using `RETURNING` pattern |
| 7 | Validated no other scripts in the repo were affected by the rename via full-text search |
| 8 | Pushed changes to feature branch for review |

---

## Technologies & Tools Used

- **PostgreSQL** — target database for DDL/DML changes (schemas: `bdi_audit`, `bdi_metadata`)
- **pgAdmin 4** — database exploration, query validation, FK chain verification
- **Git** — version control, feature branching, commit and push
- **VS Code** — SQL script editing and workspace navigation
- **Jenkins (Groovy)** — deployment pipeline awareness (scripts deployed via Jenkins jobs)

---

## Outcome

- Producer audit table, sequence, and constraints renamed to clearly identify producer-specific objects
- New consumer task metadata entry created alongside renamed producer entry
- Production-ready DML script prepared for clean PROD deployment
- Separate ALTER migration script created for existing DEV/UAT environments
- All changes validated to have zero impact on existing repo scripts or running applications

---

## Skills Demonstrated

- Database schema analysis and foreign key relationship mapping across multiple schemas
- Writing safe, idempotent DDL migration scripts (ALTER with IF EXISTS)
- Production-ready DML scripting with proper use of PostgreSQL `RETURNING` clause
- Impact analysis across a multi-file SQL deployment repository
- End-to-end ownership of a DDL change from requirement gathering through implementation and deployment prep

---

## Resume One-Liner

> *Designed and implemented PostgreSQL DDL/DML changes to split a generic MQ audit infrastructure into producer- and consumer-specific metadata entries, including safe ALTER migration scripts and production-ready seed logic, for the Treasury BDI platform at Fidelity.*
