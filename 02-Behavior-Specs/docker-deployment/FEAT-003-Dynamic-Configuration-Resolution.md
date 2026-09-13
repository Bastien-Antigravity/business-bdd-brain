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
# FEAT-003: Dynamic Configuration Resolution & Precedence

*Back-link: [[docker-deployment/README|⬅️ docker-deployment]]*
---

# Feature: Dynamic Configuration & Service Topology

## 🎯 Business Intent
- **User Story**: As a platform developer or operator, I want all port assignments, IP bindings, and service URLs resolved dynamically from configuration definitions (`native.yaml`, service slices) and environment overrides so that no microservice or orchestrator script relies on hardcoded networking assumptions.
- **Problem Solved**: Eliminates brittle hardcoded ports, IP mismatches between local host and Docker container networks, and port conflicts across multi-developer environments.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Dynamic Port and IP Resolution
- **Given** an ecosystem service definition (e.g. `Config Server`, `TimescaleDB`, `NATS Server`)
- **When** the orchestrator or service starts
- **Then** it must query the service port and IP dynamically using helper routines (`get_service_port`, `get_service_ip`)
- **And** it must prioritize environment overrides (`*_PORT`, `*_IP`) over defaults
- **And** it must read declarative parameters from `modes/local/config/native.yaml` when running in native mode

### Scenario 2: Zero Hardcoded URLs
- **Given** microservices requiring connectivity to the Config Server
- **When** the native ecosystem is launched via `./fleet.sh local`
- **Then** the orchestrator must dynamically resolve the Config Server REST port and IP from configuration
- **And** it must export `CONFIG_SERVER_URL="http://<resolved_ip>:<resolved_port>"` to the environment
- **And** the watchdog supervisor must propagate this dynamic URL to child microservice processes

### Scenario 3: Isolated Configuration Profiles
- **Given** runtime execution modes `local`, `docker`, and `production`
- **When** a specific mode is launched
- **Then** `modes/local/` must strictly use `modes/local/config/native.yaml`
- **And** `modes/docker/` must strictly use dedicated microservice slices in `modes/docker/config/services/`
- **And** `modes/production/` must strictly use production configuration in `modes/production/config/`
- **And** deprecated monolithic configurations in `config/` must not be loaded

## 🛠️ Technical Constraints
- **Parser**: Standard library parsing and fallback AST evaluation via `common.py`.
- **Precedence Hierarchy**: CLI Flags > Environment Variables > YAML Configuration > Hardcoded Safe Fallbacks.
