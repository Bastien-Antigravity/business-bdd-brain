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
# Feature: Log Rotation Strategy

## 🎯 Business Intent
- **User Story**: As a server admin, I want the log server to automatically rotate log files when they reach a certain size so that I don't run out of disk space and I can easily archive old logs.
- **Problem Solved**: Prevents "Disk Full" outages and makes log management manageable.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Size-Based Rotation Trigger
- **Given** the active log file `_main.log` reaches 1MB (default)
- **When** the current batch is written and the file exceeds `max_file_bytes`
- **Then** the server must trigger the rotation logic.

### Scenario 2: Sequential File Shifting
- **Given** a rotation is triggered
- **When** the server shifts files
- **Then** it must rename `_main.log.9` to `_main.log.10` (if 10 is the max)
- **And** it must rename `_main.log.0` to `_main.log.1`
- **And** finally it must rename the active `_main.log` to `_main.log.0`.

### Scenario 3: Atomic Re-creation
- **Given** the files have been shifted
- **When** the rotation finishes
- **Then** the server must create a new, empty `_main.log` file
- **And** it must reset the `file_size` counter to 0
- **And** it must resume writing from the buffer.

## 🛠️ Technical Constraints
- **Default Size**: 1MB per file.
- **Backup Count**: Default 10 files.
- **File Format**: `_main.log` (active), `_main.log.N` (backups).
