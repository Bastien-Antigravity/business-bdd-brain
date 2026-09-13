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
# FEAT-001: Multi-Mode Execution & Explicit CLI Dispatch

*Back-link: [[docker-deployment/README|⬅️ docker-deployment]]*
---

# Feature: Multi-Mode Ecosystem Orchestration

## 🎯 Business Intent
- **User Story**: As a platform developer or DevOps engineer, I want explicit, isolated runtime execution modes (`local`, `docker`, `production`) and unambiguous CLI parameters so that the ecosystem can run natively on workstations for development or in containerized topologies on VPS hosts without unexpected fallback behavior.
- **Problem Solved**: Eliminates CLI ambiguity, silent fallback launches, and environment cross-contamination by strictly validating execution mode and routing directly to dedicated mode packages (`modes/local/`, `modes/docker/`, `modes/production/`).

## 🎬 Scenarios (Gherkin)

### Scenario 1: Native Host Stack Execution
- **Given** developer dependencies (Go, Rust, Python 3) are installed on the host machine
- **When** the developer runs `./fleet.sh local` (or `./fleet.sh local --infra=docker`)
- **Then** the orchestrator must execute pre-flight Git branch checks on branch `develop`
- **And** it must configure Antigravity IDE workspace settings if absent
- **And** it must ensure backing databases (TimescaleDB on 5432, NATS on 4222) are reachable
- **And** it must spawn `watchdog-agent` process supervisor to manage all 11 native microservices
- **And** it must wait for health and readiness across all service ports and HTTP endpoints before confirming success

### Scenario 2: Containerized Loopback Isolation
- **Given** the local Docker daemon is running
- **When** the developer runs `./fleet.sh docker`
- **Then** the orchestrator must configure the isolated loopback alias `127.0.0.2`
- **And** it must terminate any conflicting host-bound native processes
- **And** it must deploy the container fleet defined in `modes/docker/docker-compose.yaml`
- **And** all published ports must bind exclusively to `127.0.0.2` to prevent collisions with host-level services

### Scenario 3: Production VPS Stack with Automated Delivery
- **Given** a production Linux VPS host
- **When** the operator runs `./fleet.sh production` (or `./fleet.sh prod`)
- **Then** the orchestrator must deploy the container fleet defined in `modes/production/docker-compose.yaml`
- **And** containers must bind to `0.0.0.0`
- **And** Watchtower must be activated to continuously poll and pull updated container images from GHCR

### Scenario 4: Strict Rejection of Invalid Subcommands
- **Given** an operator invokes an unconfigured or deprecated subcommand (e.g. `./fleet.sh start` or `./fleet.sh unknown`)
- **When** the command is parsed
- **Then** the orchestrator must reject the invocation immediately with a clear error message
- **And** it must display valid available choices without falling back to any default mode
- **And** it must terminate with a non-zero exit code (exit 2)

### Scenario 5: Contextual Command-Level Help
- **Given** a user requests help for a specific command (e.g. `./fleet.sh local --help`)
- **When** the `--help` or `-h` flag is parsed
- **Then** the CLI parser must output documentation specific only to that command
- **And** it must document all accepted options (`--infra`, `--timeout`) and maintenance actions (`ide-setup`, `branch-check`, `ghcr-login`, `ghcr-publish`)

## 🛠️ Technical Constraints
- **Runtime**: Python 3.8+ standard library only (`argparse`, `subprocess`, `urllib.request`). Zero external pip packages allowed.
- **Entrypoint**: Cross-platform delegation via `fleet.sh` (POSIX) and `fleet.cmd` (Windows) directly into `scripts/fleet.py`.
