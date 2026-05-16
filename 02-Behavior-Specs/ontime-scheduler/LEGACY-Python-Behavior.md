---
type: legacy
status: active
tags:
- \'#service/business-bdd-brain\'
- '#state/active'
- null
- '#type/legacy'
microservice: business-bdd-brain
---

# Legacy BDD Spec: Ontime-Scheduler (Python)
**Status**: ARCHIVED / REFERENCE
**Context**: Original Python implementation using FastAPI and APScheduler.

## Feature: Dynamic Python Job Execution
As a developer, I want to upload raw Python code to be scheduled and executed dynamically.

### Scenario: Creating a scripted job
**Given** a Python script string containing a function `exec_job`
**When** a POST request is made to `/create_job`
**Then** the scheduler writes the script to `scheduled/<job_id>.py`
**And** the scheduler dynamically imports the module
**And** the job is added to the APScheduler engine

## Feature: Comprehensive Job Control (CRUD+)
The scheduler provides full lifecycle management via a REST API.

### Behaviors:
- **Pause/Resume**: Temporarily stop a job without removing it from the store.
- **Modify**: Update the trigger expression, arguments, or the script itself on the fly.
- **Manual Execution**: Trigger a job immediately via `/exec_job/{jobId}`.
- **Detailed Inspection**: `/infos_job/{jobId}` returns run history, script source, and next run time.

## Feature: Resilience and Monitoring
### Behaviors:
- **Persistence**: All jobs are stored in PostgreSQL using `SQLAlchemyJobStore`.
- **Misfire Handling**: Uses a `misfire_grace_time` of 10 seconds.
- **Precision Loops**: Custom logic (`job_loops.py`) detects if a job starts with a delay and logs a WARNING or stops the job if the delay is too high.
- **Error Alerts**: An internal task (`alert_jobs_errored`) scans the database every 300 seconds for failed executions and reports them.

## Feature: Multi-Channel Interface
### Behaviors:
- **Web UI**: Provides a `main.html` dashboard for human-readable monitoring.
- **Telecommand**: Support for remote commands (via internal `Telecommand` module).
- **Discord Integration**: Capability to log events directly to Discord via `DiscoLogger`.

## Feature: Trigger Flexibility
Supports multiple trigger formats:
- **Cron**: Standard crontab strings (e.g., `* * * * *`) or JSON-defined intervals.
- **Interval**: Seconds, minutes, hours, or days.
- **Date**: One-time execution at a specific timestamp.
