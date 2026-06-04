# Task Summary — BDI Lambda Trigger: S3-to-Control-M Job Router

**Role:** Apprentice Engineer — FMT Tax & Treasury, Fidelity
**Duration:** May – June 2026
**Story/Task:** TRETAX-11250 — Develop the application code needed for Lambda trigger and the Lambda trigger solution in Dev

---

## What Was the Task

The Treasury BDI (Bulk Data Ingest) platform receives financial files from multiple banks and data vendors (PAIN001/002, BAI, Bloomberg, FILI, OracleAP, etc.) into an S3 bucket. Each file type needs to trigger a specific Control-M job to process it downstream.

The task was to design, build, unit test, and pipeline-integrate an AWS Lambda function that:
1. Listens to an SQS queue buffering S3 file-arrival events
2. Identifies the file type from the S3 key
3. Routes it to the correct Control-M job via the Control-M AAPI
4. Only processes files during business hours (Mon–Fri, 06:00–18:30 ET)

---

## What I Did

| # | Activity |
|---|----------|
| 1 | Analysed the authoritative BDI file watcher CSV (`data-1780299996098.csv`) to extract all file patterns, S3 prefixes, and target Control-M job names |
| 2 | Designed the routing logic covering 20+ file types across PAIN001 (9 banks), PAIN002 (8 banks + HSBC special), PAIN008, BAI/MT940/MT942 (5 jobs), ACK, NACK, Bloomberg (INTRATE/FXRATE/FEDFUNDS), FILI, OracleAP, Shares, and CCTS |
| 3 | Built `lambda_function.py` in Python 3.12 with SQS batch processing, per-record error isolation (partial batch failure), JWT + bearer token Control-M auth, and a 3-attempt retry mechanism |
| 4 | Used `SM_PATH` environment variable to retrieve Control-M credentials (client ID, secret, username, password) from AWS Secrets Manager at runtime |
| 5 | Applied time-window and weekday guard logic using `zoneinfo` (America/New_York) so files outside business hours are skipped without failure |
| 6 | Identified and fixed a multi-environment bug for CCTS files: the file prefix differs per environment (FBSIT=Dev, FBSIG=SIT, FBSIC=UAT, FBSI=PRD) — made the match generic using `startswith("FBSI") and ".FBCAD49Q.INPFILE" in filename` |
| 7 | Wrote `conftest.py` to inject all required environment variables before pytest collects modules (prevents `KeyError` at import time during local test runs) |
| 8 | Wrote a full pytest unit test suite (`test_lambda_function.py`) — 44 tests covering all routing paths, time-window edge cases, retry logic, partial batch failure, malformed events, and URL-encoded filenames |
| 9 | Created CFT template (`Lambdacft.yml`) with 4 new Parameters (`SmPath`, `CtmApiUrl`, `CtmServer`, `CtmJobFolder`) and the corresponding Lambda Environment block |
| 10 | Created `dev_params.yml` with Dev environment values for all 4 parameters |
| 11 | Reviewed and confirmed Abhijith's two-repo Jenkins pipeline (`lambdagroovydep 1.yml`): BDI scripts repo checked out first for build/test/sonar, cloud repo checked out to `source/` for CFT packaging and deployment |
| 12 | Identified Sonar coverage gap: `sonar.coverage.inclusions` in BDI scripts repo's `sonar.properties` was missing `lambda/**` — flagged to Abhijith to add before pipeline run |
| 13 | Created 50+ dummy S3 test files (organised under `s3_test_files/` by type subfolder) for manual integration testing against the real S3 bucket, cross-referenced against the BDI watcher CSV |
| 14 | Flagged 3 unimplemented routing paths from CSV (822 files, TWIST files, MDW files) as out-of-scope gaps — raised to Saravana/Abhijith for decision |

---

## Technologies & Tools Used

- **Python 3.12** — Lambda function development
- **AWS Lambda** — serverless compute, triggered by SQS
- **Amazon SQS** — buffers S3 event notifications to Lambda
- **Amazon S3** — file landing bucket (`ctg-dev-treasurybdi-bulkdp`)
- **AWS Secrets Manager** — stores Control-M API credentials, accessed via `boto3`
- **boto3 / botocore** — AWS SDK for Secrets Manager client
- **urllib3** — HTTP client for Control-M AAPI calls (SSL verification disabled for internal API)
- **zoneinfo** — timezone-aware business-hours enforcement (America/New_York)
- **Control-M AAPI** — REST API to order/trigger Control-M jobs
- **pytest** — unit testing framework (44 tests, all passing)
- **unittest.mock** — mocking boto3, urllib3, datetime for isolated tests
- **AWS CloudFormation (CFT)** — infrastructure-as-code for Lambda deployment
- **Jenkins / Groovy pipeline** — CI/CD pipeline for build, test, sonar, package, deploy
- **SonarQube** — static analysis and code coverage gate
- **Git** — version control across two repos (BDI scripts repo + cloud/infra repo)

---

## Outcome

- Lambda function fully implemented, handling all 20+ file-type routing paths per the BDI watcher CSV
- 44 unit tests written and passing (exit code 0) — all routing paths, edge cases, and error scenarios covered
- CFT and dev_params ready for Dev deployment
- Pipeline structure confirmed correct with Abhijith; Sonar coverage gap identified and flagged
- CCTS routing made generic to work across all 4 environments without code changes per deployment
- Dummy S3 test files created for all routing paths, ready for manual integration testing
- Story TRETAX-11250 closed with all 3 subtasks complete:
  - **TRETAX subtask 1** — Lambda script creation
  - **TRETAX subtask 2** — Unit testing
  - **TRETAX subtask 3** — Add Sonar changes to pipeline

---

## Skills Demonstrated

- AWS serverless architecture (Lambda + SQS + S3 + Secrets Manager)
- Python application development following production standards (no hardcoded defaults, environment-variable-driven config)
- Test-driven development — comprehensive pytest suite with mocking
- Infrastructure-as-code authoring (CloudFormation CFT + deployment parameters)
- CI/CD pipeline understanding (Jenkins two-repo Groovy pipeline)
- Reading and interpreting authoritative source-of-truth data (BDI file watcher CSV) to drive implementation
- Cross-environment code generalisation (CCTS prefix fix across Dev/SIT/UAT/PRD)
- Proactive gap identification (Sonar coverage, unimplemented 822/TWIST/MDW routes, S3 event filter for Bloomberg)
- End-to-end ownership: requirement analysis → design → implementation → testing → infra → pipeline → documentation

---

## Resume One-Liner

> *Built and delivered a production-ready AWS Lambda (Python 3.12) that routes 20+ bank file types from S3 through SQS to Control-M jobs, with 44 unit tests, CloudFormation deployment, and Jenkins CI/CD pipeline integration — for the FMT Tax & Treasury BDI platform at Fidelity.*
