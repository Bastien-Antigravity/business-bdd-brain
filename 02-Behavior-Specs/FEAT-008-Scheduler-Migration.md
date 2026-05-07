---
type: legacy
status: active
---

# 🌌 Feature: Ontime-Scheduler Migration Validation (FEAT-008)

**Objective**: Validate the functional parity of the Go-based `ontime-scheduler` with the legacy Python service, ensuring reliable job scheduling and execution.

## 📝 Background
The `ontime-scheduler` has been migrated to Go. It must support:
- Legacy dashboard interactions.
- Cron, Interval, and Date triggers.
- Python script execution via base64 upload or file path.
- Persistent job storage in SQLite/PostgreSQL.

## 🧪 Scenario 1: Job Lifecycle via Legacy API
**Given** the `ontime-scheduler` service is running in standalone mode
**When** a client sends a POST request to `/create_job` with a valid Cron payload and a Python script
**Then** the service should return a 201 Created status
**And** the script should be saved in the `scheduled/` directory
**And** the job should appear in the database with status `PENDING`

## 🧪 Scenario 2: Manual Execution (Trigger Bolt)
**Given** a job with ID `test-manual` is already scheduled
**When** a client sends a POST request to `/exec_job/test-manual`
**Then** the service should execute the associated task immediately
**And** a new entry should appear in the `job_logs` table with `log_type = 'INFO'`

## 🧪 Scenario 3: Execution Parity (Python Bridge)
**Given** a job is configured with a Python script containing a function `target_func`
**When** the scheduler triggers the job
**Then** the engine should invoke `python3 -c "..."` to run the function
**And** the output should be captured and logged in the database

## 🧪 Scenario 4: Dashboard Resilience
**Given** the dashboard is open at `http://localhost:8080`
**When** the user clicks the "Pause" button for a job
**Then** the frontend should send a POST to `/pause_job/{id}`
**And** the job should no longer be scheduled in the Go cron engine
