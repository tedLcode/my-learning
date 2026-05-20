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

---

### Session 3 — May 20, 2026

`security_from_EKS.py`
- No passwords are hardcoded; a vault agent mounts secrets as files into the container at `/vaultdata/` when running on EKS. `SECRETS_PATH_CONTAINER = "vaultdata"` in `constant.py`.
- Functions:
	- `get_secrets_raw("dbcreds")` reads `vaultdata/dbcreds/_raw` and returns `{"username": ..., "password": ...}`.
	- `get_secrets_value("kyriba_sftp_key", "private_key")` reads a single file and returns RSA key text.
	- `get_secrets_file(full_path)` reads any file by full path.
- Used by DB connection (dbcreds), SFTP transfer (RSA key), NiFi login (nificred), ServiceNow login (snowcreds).

`database_adapter.py` + `postgresql_database_adapter.py`
- `database_adapter.py` is a wrapper; real logic in `postgresql_database_adapter.py` using `psycopg2`.
- `get_connection()` builds connection using vault `dbcreds` and env vars (`db_hostname`, `db_port`, `db_name`).
- `get_data(sql)` executes SELECTs and returns list of dicts with uppercase keys to match metadata columns.
- `save(sql)` runs INSERT/UPDATE and commits; audit and metadata flows call these.

`s3_utilities.py`
- Uses `boto3` with retry logic (3 attempts with 1s, 2s, 4s delays).
- Key functions:
	- `list_s3_objects(bucket, prefix, pattern)` — lists direct children matching pattern; used by FILE_WATCHER, FILE_VALIDATION, FILE_TRANSFER, INCIDENT_CREATION, EMAIL_NOTIFICATION.
	- `read_s3_object_bytes(bucket, key)` — downloads raw bytes; used for SFTP uploads and email attachments.
	- `copy_s3_file(src, tgt)` — copies object; used when copy_ind=Y.
	- `move_s3_file(src, tgt)` — copy then delete original; used for moves to quarantine/processed.
	- `delete_s3_file(bucket, key)` — deletes object; used for cleanup.
	- `get_s3_object_info(bucket, key)` — returns metadata/size without full download; used for validation.
- Pattern matching uses `fnmatch` and only direct children of the prefix are returned.

`common_utilities.py`
- `generate_task_status()` — tracks `pass_flag` and `fail_flag` per file; final task status: no pass → FAILURE, both flags → PARTIAL SUCCESS, only pass → SUCCESS.
- `populate_metadata_with_values()` — replaces `$VAR` placeholders and `{yyyy}/{mm}/{dd}` date placeholders in metadata with env values and today's date.
- `metadata_to_dataclass(cls, data)` — converts uppercase DB row dicts to typed dataclass objects used by task executors.
- `generate_file_name(original_name, pattern)` — supports placeholders `{filename}`, `{ext}`, `{yyyy}`, `{mm}`, `{dd}`, `{timestamp}`, `{suffix}`; `{suffix}` comes from `file_type_suffix` env var (DEV uses `T`).

---

### How All 3 Sessions Connect — The Complete Chain

Control-M triggers `trebdi_execute.py`
				│
				├─ `security_from_EKS`  → reads vault files (passwords, keys)
				├─ `database_adapter`   → connects to PostgreSQL using vault + env file
				├─ `metadata.py`        → fetches task rows from DB
				├─ `common_utilities`   → replaces $VAR placeholders, converts rows to dataclasses
				│
				└─ WHILE LOOP per task:
								├─ `audit.add_audit_entry()`     → `database_adapter.save()` → PostgreSQL
								├─ `s3_utilities.list/copy/move` → `boto3` → AWS S3
								├─ `security.get_secrets_value`  → RSA key for SFTP (FILE_TRANSFER)
								├─ `common_utilities` pass/fail flags tracked per file
								├─ `audit.add_sub_audit_entry()` → `database_adapter.save()` → PostgreSQL
								├─ `audit.update_audit_entry()`  → `database_adapter.save()` → PostgreSQL
								└─ if FAILURE/PARTIAL → `notification.send_notification()` → SMTP email

---

### Remaining — Session 4 (Final)
- No new files.
- Draw the full flow from memory and prepare KT questions for Hari.


