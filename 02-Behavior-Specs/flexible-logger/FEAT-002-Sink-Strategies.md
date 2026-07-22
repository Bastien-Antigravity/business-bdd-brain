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
# Feature: Sink Strategies (Best-Effort vs. Audit-Secure)

## 🎯 Business Intent
- **User Story**: As a platform architect, I want to choose between high-performance logging (where logs might be dropped) and audit logging (where the app waits for confirmation) so that I can balance system speed and data integrity.
- **Problem Solved**: Solves the conflict between "Non-blocking performance" and "Compliance/Legal requirements."

## 🎬 Scenarios (Gherkin)

### Scenario 1: Best-Effort (AsyncSink)
- **Given** a logger using the `Standard` or `HighPerf` profile
- **When** a log entry is submitted
- **Then** it should be placed in a buffered channel
- **And** the application thread should return immediately (Non-blocking)
- **But** if the buffer is full (Network congestion)
- **Then** the log entry should be dropped to prevent application stalling

### Scenario 2: Audit-Secure (Sync/Blocking)
- **Given** a logger using the `Audit` profile
- **When** a log entry is submitted
- **Then** it should bypass the async channel
- **And** it should perform a direct, blocking write to the `ManagedConnection`
- **And** the application must wait until the write is acknowledged by the kernel/socket before continuing

### Scenario 3: Multi-Sink Fan-out
- **Given** a `MultiSink` configured with both Console and Network destinations
- **When** a log is processed
- **Then** it must be delivered to BOTH destinations
- **And** a failure in one sink must NOT prevent delivery to the other

## 🛠️ Technical Constraints
- **Queue Limit**: `AsyncSink` MUST have a configurable buffer size (default 1024 entries).
- **Atomic Swap**: The `LogEntry` MUST be returned to the `sync.Pool` only after ALL sinks have finished processing it.
