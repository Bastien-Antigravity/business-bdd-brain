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
# Feature: Metadata Enrichment & Caller Discovery

## 🎯 Business Intent
- **User Story**: As a DevOps engineer, I want every log entry to contain the hostname, process ID, and file/line number so that I can easily trace logs back to the specific source code and server.
- **Problem Solved**: Eliminates the "Mystery Log" problem where you don't know which service or which line of code generated an error.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Automatic System Metadata
- **Given** a new log entry is created
- **When** the `LogEngine` processes it
- **Then** it must automatically inject:
    - `Hostname`: The local machine name.
    - `ProcessID`: The current OS PID.
    - `Timestamp`: High-precision UTC time.

### Scenario 2: Smart Caller Discovery
- **Given** a log level of `WARNING` or higher
- **When** the message is processed
- **Then** the logger must use `runtime.Caller` to find the filename and line number of the original log call
- **But** if the level is `DEBUG` and the profile is `HighPerf`
- **Then** it should SKIP caller discovery to save CPU cycles

### Scenario 3: Contextual Tags
- **Given** a logger instance with attached "Tags" (e.g. `request_id: 123`)
- **When** a message is logged via that instance
- **Then** the final output must include those tags alongside the message

## 🛠️ Technical Constraints
- **Caller Skip**: The `runtime.Caller` depth MUST be correctly calculated to skip the logger's internal facade layers and point to the actual application code.
- **Optimization**: Hostname and PID should be cached at startup to avoid expensive system calls for every log.
