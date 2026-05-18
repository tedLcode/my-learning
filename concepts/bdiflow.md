# BDI Flow — Notes (2026-05-18)

Session 1 — May 14, 2026

Architecture & System Understanding
- BDI handles payment file processing between Fidelity systems, Kyriba, and banks.
- PostgreSQL roles: metadata (task configs) and audit (history/logbook).
- S3 folder flow: `landing/ → valid/ → quarantine/ → processed/`.
- Axway = external SFTP gateway; NiFi = internal data puller.

Ingest Paths
- PATH 1: Axway (SFTP) → BDI S3
- PATH 2: NiFi pulls from Oracle/FILI/SHARES → BDI S3

Core: `trebdi_execute.py`
- Control-M triggers the app with `--jobname` and `--taskname`.
- Task-driven design: tasks are rows in `BDI_METADATA.BDI_TASK` and not hardcoded.
- Execution starts with `PREDECESSOR_TASK_ID = -1`; while-loop builds execution list adding children as tasks succeed.
- Status codes: FILE NOT FOUND, SUCCESS, PARTIAL SUCCESS, IN PROGRESS, FAILURE.

Task Types (summary)
- FILE_WATCHER, FILE_VALIDATION, FILE_MOVE / FILE_COPY, FILE_TRANSFORM, FILE_TRANSFER (SFTP to Kyriba via Axway), FILE_REMOVE, EMAIL_NOTIFICATION, DATA_INGESTION (NiFi), INCIDENT_CREATION (ServiceNow API).

Session 2 — May 18, 2026

Key Files Reviewed
- `constant.py`: shared constants (SFTP port, SMTP, vault paths).
- `DEV1_env.env`: environment settings for DEV (S3 bucket, DB host/port/name, kyriba_axway_host, nifi_hostname, SNOW_INSTANCE_URL, MODE, notification lists).
- `audit.py`: `add_audit_entry()`, `update_audit_entry()`, `add_sub_audit_entry()`, `check_in_progress()` — used for task run logging and safety checks.
- `notification.py`: `send_notification()` and `send_file_in_mail()` — handles alerts and EMAIL_NOTIFICATION task.

Remaining — Session 3 (to study next)
- `s3_utilities.py`
- `security_from_EKS.py`
- `common_utilities.py`
- `database_adapter.py`

