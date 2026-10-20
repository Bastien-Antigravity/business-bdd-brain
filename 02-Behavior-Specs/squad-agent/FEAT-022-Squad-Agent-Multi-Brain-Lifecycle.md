---
type: spec
microservice: squad-agent
repo: obsidian-brain
feature_id: FEAT-022
status: active
tags:
- '#service/squad-agent'
- '#state/active'
- '#tier/behavior'
- '#type/spec'
- '#zone/0-orchestration'
---
# Feature: Squad Agent Multi-Brain Full Lifecycle & Sandbox Isolation

## 🎯 Business Intent
- **User Story**: As a fleet developer or autonomous domain agent, I want satellite brains (e.g., `trading-brain`, `finance-brain`) to seamlessly plug into the Bastien-Antigravity ecosystem with strict multi-tenant isolation, container filesystem boundaries, zero-crosstalk NATS messaging, network-based RAG indexing, and atomic unmerging with zero residual state.
- **Problem Solved**: Eliminates cross-brain state contamination, filesystem coupling across containers, hardcoded port/network values, and ensures clean sovereign lifecycle management for satellite nodes.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Dynamic Brain Scoping & Topic Namespacing
- **Given** a satellite brain initialized with identifier `sandbox-satellite-xxx`
- **When** the squad agent daemon initializes its event bus and memory layers
- **Then** the NATS chat topic must be namespaced to `bastien-antigravity.<brain_id>.squad.chat`
- **And** intra-brain messages must be strictly isolated from the root room `bastien-antigravity.squad.chat` with 0% crosstalk.

### Scenario 2: Decoupled Push-over-HTTP RAG Indexing
- **Given** local markdown documentation files inside a satellite brain container
- **When** the local `DocIndexerAgent` scans the local vault
- **Then** documents must be transmitted via HTTP REST to `POST /api/v1/index/document` tagged with `brain_id`
- **And** the central `rag-engine` must index chunks into PostgreSQL without requiring filesystem mount access to the satellite container.

### Scenario 3: Cross-Brain Delegation & Scoped Reply Routing
- **Given** an agent or user in a satellite room needing engineering assistance
- **When** a message tags a central agent using compound syntax `@{target_brain}:{role}` (e.g. `@root:developer`)
- **Then** the message must be routed over NATS to the central engineering agent
- **And** the central agent's response must be dispatched directly back to the satellite's `reply_topic`.

### Scenario 4: Autonomous Tool Calling Security Guardrails
- **Given** an autonomous agent executing file or system operations
- **When** a path traversal argument (e.g. `../../../../../etc/passwd`) is supplied to `read_workspace_file` or `write_workspace_file`
- **Then** the tool execution must be blocked with an access denied security error
- **And** when a dangerous keyword (e.g. `rm`, `chmod`, `sudo`) is passed to `execute_shell_command`
- **Then** the command must be rejected immediately by the command firewall.

### Scenario 5: QA Verification Agent Execution
- **Given** a generated microservice or modified codebase
- **When** the `@qa` agent runs the verification suite
- **Then** unit test runners and syntax validators must execute cleanly within the local sandbox environment.

### Scenario 6: Atomic Clean Unmerge & Root Vault Protection
- **Given** an obsolete or decommissioned satellite brain
- **When** `purge_brain_index` is invoked for that `brain_id`
- **Then** all conversational records, BM25 indices, parent chunks, and vector embeddings must be deleted atomically from PostgreSQL
- **And** when an attempt is made to purge the authoritative root `obsidian-brain`
- **Then** the operation must be blocked immediately to prevent ecosystem data loss.

## 🛠️ Technical Constraints
- **Zero Hardcoded Values**: Ports, host IPs, and brain identifiers must resolve dynamically via `microservice-toolbox`.
- **Filesystem Boundaries**: Containerized satellite microservices live strictly in `<satellite>/06-Microservices/`; central agents never write across container volumes.
- **Fail-Fast**: If required capabilities are missing or offline in production mode, systems fail-fast without silent loopback fallbacks.

## ✅ Acceptance Criteria (Definition of Done)
- [x] Multi-brain topic namespacing and zero crosstalk verified in sandbox.
- [x] Push-over-HTTP RAG ingestion verified.
- [x] Cross-brain compound tag routing (`@{brain}:{role}`) verified.
- [x] Path traversal and shell injection security firewalls active.
- [x] Automated QA test runner verified.
- [x] Clean unmerging verified with root brain protection invariant.
- [x] 100% scenario pass in `sandbox-testing`.

## 🔗 Sandbox Binding
- **Scenario Specification**: `[[sandbox-testing/01-Specifications/FEAT-022-Squad-Agent-Multi-Brain-Lifecycle.yaml]]`
- **Scenario Runner**: `sandbox-testing/02-Scenarios/python/squad_agent_scenario_test.py`
