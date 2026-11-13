---
microservice: microservice-toolbox
type: note
status: active
tags:
- '#service/microservice-toolbox'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---
# Feature: Docker Guard (Network Isolation)

## 🎯 Business Intent
- **User Story**: As a DevOps engineer, I want my microservices to automatically adapt to containerized environments so that I don't have to change configuration files when moving from local development to a Docker cluster.
- **Problem Solved**: Prevents hardcoded local IPs from breaking service discovery in orchestrated environments (Kubernetes/Docker).

## 🎬 Scenarios (Gherkin)

### Scenario 1: Config-Verbatim Preservation in Container (`connectivity.Resolver`)
- **Given** a microservice running inside a Docker container (detection via `/.dockerenv` or `DOCKER_ENV=true`)
- **And** the validated `AppConfig` specifies a configured IP/host and port (e.g., `127.0.0.1` or `config-server`)
- **When** the toolbox resolves the bind or capability address via `connectivity.Resolver`
- **Then** it must preserve the configured `AppConfig` IP/host and port verbatim without rewriting to `0.0.0.0` or substituting fallback addresses
- **And** it must fail fast if the configured IP or port is empty

### Scenario 2: Suppression of CLI Network Overrides
- **Given** a containerized microservice
- **When** a user attempts to override the host or port via CLI flags (`--host 192.168.1.50`, `--port`, `--grpc_host`, `--grpc_port`)
- **Then** the Docker Guard should detect the container environment
- **And** it should strictly ignore the CLI override to preserve the orchestrated YAML network settings
- **And** it should log a warning that the override was suppressed

### Scenario 3: Local Development (No Docker)
- **Given** a microservice running directly on a host OS (no `/.dockerenv`)
- **When** the user provides `--host 0.0.0.0` via CLI
- **Then** the toolbox should respect the override
- **And** `connectivity.Resolver` must still preserve the resulting `AppConfig` host/port verbatim

## 🛠️ Technical Constraints
- **Detection**: MUST check for the existence of `/.dockerenv` or environment variables like `DOCKER_ENV=true` / `KUBERNETES_SERVICE_HOST`.
- **Flags**: Impacted flags: `--host`, `--port`, `--grpc_host`, `--grpc_port`.
