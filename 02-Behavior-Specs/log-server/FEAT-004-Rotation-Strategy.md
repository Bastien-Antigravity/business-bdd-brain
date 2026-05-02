---
repo: log-server
feature_id: FEAT-004
status: draft
---

# Feature: Log Rotation & Pruning

## 🎯 Business Intent
- **User Story**: As a server admin, I want the log server to automatically rotate log files when they reach a certain size so that I don't run out of disk space and I can easily archive old logs.
- **Problem Solved**: Prevents "Disk Full" outages and makes log management manageable.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Size-Based Rotation
- **Given** the active log file `_main.log` reaches 100MB
- **When** the next log entry is written
- **Then** the server must close the current file
- **And** it must rename it to `_main.log.1` (or timestamp-based)
- **And** it must create a fresh, empty `_main.log` for new entries
- **And** it should shift existing backups (`.1` -> `.2`, etc.)

### Scenario 2: Atomic Handover
- **Given** a rotation in progress
- **When** new logs arrive during the file swap
- **Then** the server must buffer those logs in memory
- **And** it must NOT drop them while the new file is being created
- **And** it must write them as the first entries in the new file

### Scenario 3: Retention Policy (Pruning)
- **Given** a maximum of 10 backup files
- **When** the 11th rotation occurs
- **Then** the server must delete the oldest backup file (`.10`)
- **And** it must log the deletion to confirm disk space management

## 🛠️ Technical Constraints
- **Library**: Recommend using `flexi_logger` or `log4rs` in Rust for the low-level file management, but the "Atomic Handover" must be guaranteed by the `LogWriter` logic.
