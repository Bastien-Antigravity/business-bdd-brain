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
# Feature: Docker Guard (Network Isolation)

## 🎯 Business Intent
- **User Story**: As a DevOps engineer, I want my microservices to automatically adapt to containerized environments so that I don't have to change configuration files when moving from local development to a Docker cluster.
- **Problem Solved**: Prevents hardcoded local IPs from breaking service discovery in orchestrated environments (Kubernetes/Docker).

## 🎬 Scenarios (Gherkin)

### Scenario 1: Loopback Translation in Container
- **Given** a microservice running inside a Docker container (detection via `/.dockerenv`)
- **And** the configuration requests a bind to `127.0.0.1`
- **When** the toolbox initializes the network layer
- **Then** it should automatically resolve the primary network interface IP of the container
- **And** it should bind to that IP instead of loopback to allow external discovery

### Scenario 2: Suppression of CLI Network Overrides
- **Given** a containerized microservice
- **When** a user attempts to override the host via CLI flag `--host 192.168.1.50`
- **Then** the Docker Guard should detect the container environment
- **And** it should strictly ignore the CLI override to preserve the orchestrated network settings
- **And** it should log a warning that the override was suppressed

### Scenario 3: Local Development (No Docker)
- **Given** a microservice running directly on a host OS (no `/.dockerenv`)
- **When** the user provides `--host 0.0.0.0` via CLI
- **Then** the toolbox should respect the override
- **And** it should NOT perform any automatic loopback translation

## 🛠️ Technical Constraints
- **Detection**: MUST check for the existence of `/.dockerenv` or environment variables like `KUBERNETES_SERVICE_HOST`.
- **Flags**: Impacted flags: `--host`, `--port`, `--ip`.
