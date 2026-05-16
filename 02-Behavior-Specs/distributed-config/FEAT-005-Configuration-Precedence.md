---
type: spec
microservice: obsidian-brain
repo: distributed-config
feature_id: FEAT-005
status: draft
tags:
- \'#service/obsidian-brain\'
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Configuration Precedence

## 🎯 Business Intent
- **User Story**: As a developer, I want to understand which configuration source takes priority when the same key exists in multiple places (YAML, Server, Defaults) so that I can predict the final system state.
- **Problem Solved**: Provides a deterministic "Truth Hierarchy" for configuration resolution.

## 🎬 Scenarios (Gherkin)

### Scenario 1: YAML Overrides Everything
- **Given** a default value in code `timeout=5s`
- **And** a value in the remote Config Server `timeout=10s`
- **And** a value in the local YAML file `timeout: 15s`
- **When** the configuration is loaded
- **Then** the final value must be "15s" (YAML is the ultimate authority)

### Scenario 2: Remote Server Overrides Code Defaults
- **Given** a default value in code `max_connections=100`
- **And** no value in the local YAML file
- **And** a value in the remote Config Server `max_connections=500`
- **When** the service starts and syncs
- **Then** the final value must be "500"

### Scenario 3: Environment Variable Priority
- **Given** a YAML config using expansion: `api_key: "${ENV_API_KEY}"`
- **And** the environment variable `ENV_API_KEY` is set
- **When** the loader processes the file
- **Then** the expanded environment value must be used as the YAML value
- **And** it must therefore override any server-side or code-default values

## 🛠️ Technical Constraints
- **Hierarchy Order (High to Low)**:
    1. Local YAML (with expanded Environment Variables)
    2. Remote Config Server (Live Sync)
    3. Code-level Defaults
