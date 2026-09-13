---
microservice: docker-deployment
type: note
status: active
tags:
- '#service/docker-deployment'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---
# FEAT-004: Health Gating & Readiness Diagnostics

*Back-link: [[docker-deployment/README|⬅️ docker-deployment]]*
---

# Feature: Multi-Tier Ecosystem Readiness & Gating

## 🎯 Business Intent
- **User Story**: As a developer or operations engineer, I want the fleet orchestrator to actively poll, verify, and gate the readiness of all supervised services before declaring the ecosystem healthy, and to present immediate diagnostic reports if any service stalls, hangs, or crashes.
- **Problem Solved**: Prevents false-positive startup reporting ("started in background") when microservices fail to initialize, crash on missing secrets, or encounter port conflicts.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Multi-Tier Health Verification Fast Path
- **Given** an ecosystem startup initiated via `./fleet.sh local`
- **When** the startup sequence triggers `wait_for_fleet_readiness`
- **Then** the readiness engine must dynamically probe all TCP listening ports across services
- **And** it must probe all HTTP endpoints (`/health`, `/api/v1/status`, `/`)
- **And** it must query the Watchdog Agent REST API (`http://127.0.0.1:9095/api/v1/status`)
- **And** as soon as all ports are open, HTTP probes succeed (< 500), and watchdog reports all services running, it must immediately exit the loop and confirm success without waiting for full timeout

### Scenario 2: Actionable Diagnostic Reporting on Timeout
- **Given** one or more microservices failing to open their port or process crashing during startup
- **When** the configured readiness timeout (default: 25 seconds) elapses
- **Then** the orchestrator must output a clear diagnostic breakdown
- **And** it must explicitly enumerate unopened ports, unresponsive HTTP endpoints, and stopped watchdog child processes
- **And** it must extract and print the last 15 lines of `watchdog.log` to immediately expose stack traces or panic logs to the operator

### Scenario 3: Real-Time Terminal Progress Indicator
- **Given** microservice initialization taking multiple seconds
- **When** the polling loop executes each second
- **Then** the orchestrator must render a single-line dynamic progress status on the terminal:
  `⏳ Verifying services readiness... [Xs/Ys] Ports: N/M, HTTP: A/B`
- **And** it must refresh in-place using standard terminal carriage return (`\r`) without flooding the scrollback buffer

## 🛠️ Technical Constraints
- **Zero Third-Party Libraries**: Implemented strictly using `urllib.request`, `socket`, and `time`.
- **Mandatory Wait**: Gating is non-bypassable. Services must always be verified for operational readiness.
