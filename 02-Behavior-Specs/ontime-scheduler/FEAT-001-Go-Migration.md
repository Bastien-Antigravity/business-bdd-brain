---
microservice: 08-Base-Scripts
type: note
status: active
tags:
- '#service/08-Base-Scripts'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---
# BDD Spec: Ontime-Scheduler (Go Edition)
**Status**: DRAFT
**Domain**: Infrastructure / Automation

## Feature: Reliable Job Scheduling
As a fleet administrator, I want a hardened Go-based scheduler to execute tasks on time with high reliability and persistence.

### Scenario: Creating a One-Time Triggered Job
**Given** the scheduler is running and connected to PostgreSQL
**When** I send a POST request to `/jobs` with:
  | Name | Trigger | Target | Payload |
  |------|---------|--------|---------|
  | "Cleanup" | "Date" | "http://log-server/purge" | {"days": 7} |
**Then** the job should be persisted in the database
**And** the job should be executed exactly at the specified time
**And** the execution result should be logged in the database

### Scenario: Persistence across Restarts
**Given** a job "MarketCheck" is scheduled for every hour
**When** the scheduler process is restarted
**Then** the "MarketCheck" job should be reloaded from the database
**And** the next run time should remain accurate

### Scenario: Error Handling for Trigger Failures
**Given** a job target is unreachable
**When** the job trigger fires
**Then** the scheduler should log an ERROR event
**And** mark the job state as "FAILED"
**And** (optional) retry based on the defined retry policy

### Scenario: Legacy Script Compatibility
**Given** an existing Python script `cleanup.py` in the `scheduled/` folder
**When** a job is created with `TriggerType: "Exec"` and `Payload: "python3 scheduled/cleanup.py"`
**Then** the Go scheduler should execute the script using a sub-process
**And** capture the stdout/stderr for logging
