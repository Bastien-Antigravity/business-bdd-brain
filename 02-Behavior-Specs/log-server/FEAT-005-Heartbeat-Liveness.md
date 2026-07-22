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
# Feature: Heartbeat & Client Liveness

## 🎯 Business Intent
- **User Story**: As a monitoring engineer, I want to know which services are currently sending logs and which ones have disconnected, so that I can detect if a service has crashed or is "silent" due to a bug.
- **Problem Solved**: Provides real-time visibility into the "Health" of the microservice fleet.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Tracking Active Clients
- **Given** 5 microservices connected to the Log Server
- **When** a service sends a log message or a heartbeat frame
- **Then** the server must update the "Last Seen" timestamp for that service ID
- **And** it must expose this list via an internal registry or a status endpoint

### Scenario 2: Silent Client Detection
- **Given** a service that hasn't sent a message for 30 seconds
- **When** the "Liveness Check" occurs
- **Then** the server must mark that service as "IDLE" or "DISCONNECTED"
- **And** it must log a warning if a mandatory service goes silent

### Scenario 3: Heartbeat Frame Integrity
- **Given** a 0-length frame (SafeSocket Heartbeat)
- **When** the server receives it
- **Then** it must NOT attempt to decode it as a Cap'n Proto `LogEntry`
- **And** it must treat it as a "Liveness Refresh" only
- **And** it must NOT write anything to the log file for this frame

## 🛠️ Technical Constraints
- **Tracking**: Use a `DashMap` or `HashMap` with a `RwLock` in Rust to track `ServiceID -> Timestamp`.
- **Interval**: Liveness check should run every 5-10 seconds.
