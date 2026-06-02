# Lambda -> Control-M Trigger Jira Story

## Summary
- Sprint task: build a Python AWS Lambda function that triggers Control-M jobs from S3 file arrival events.
- Main flow: S3 -> SQS -> Lambda -> Control-M API.
- Key scope: routing logic, validation, time window checks, logging, retries, and environment handling.

## Timeline

### 2026-05-22
- Received the Jira story and reviewed the architecture with Saravana, Durgesh, and Hari.
- Understood the story scope and reusable POC pieces.

### 2026-05-25
- Implemented conditional routing logic, the 6:00 AM to 6:30 PM ET time window guard, and retry handling.
- Fixed MT942 pattern matching and PAIN file misrouting.

### 2026-05-26
- Expanded routing coverage to all 27 Control-M jobs.
- Improved logging for CloudWatch and kept retry handling in place.

### 2026-05-27
- Started pytest-based test design for the Lambda handler and routing logic.

### 2026-05-29
- Finalized routing and time validation.
- Identified follow-up work for DEV/UAT/PROD configuration and Sonar stage in the deployment pipeline.

### 2026-06-01
- Rewrote the full routing logic from the CSV ground truth.
- Fixed filename and path handling, ACK/NACK prefix matching, missing job mappings, and weekend skipping.
- Updated the test suite and validated sequential batch behavior.

### 2026-06-02
- Updated the CloudFormation template to externalize Lambda runtime configuration.
- Wired DEV parameters through `dev_params.yml` and reviewed Jenkins branch and checkout flow.
- Confirmed the template-side work is ready for Jenkins build execution.

## Current Status
- Routing logic complete and validated.
- Template and parameter wiring complete.
- Remaining work: Jenkins execution and pipeline validation.

## Next Checks
- Run the Jenkins pipeline.
- Validate deployment packaging and execution in CI.
- Confirm Sonar stage behavior if the pipeline requires it.
