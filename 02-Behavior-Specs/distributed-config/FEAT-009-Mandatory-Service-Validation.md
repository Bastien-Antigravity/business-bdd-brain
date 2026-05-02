---
repo: distributed-config
feature_id: FEAT-009
status: draft
---

# Feature: Mandatory Service Validation

## 🎯 Business Intent
- **User Story**: As a system architect, I want to ensure that every microservice has at least a Log Server and a Config Server configured so that I don't have "blind" services running in production.
- **Problem Solved**: Enforces a minimum "Ecosystem Standard" during the boot phase.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Valid Ecosystem Configuration
- **Given** a YAML config containing both `log_server` and `config_server` capabilities
- **And** all mandatory fields (ip, port, name) are present
- **When** `ValidateMandatoryServices()` is called
- **Then** the validation should pass
- **And** the service should continue its startup

### Scenario 2: Missing Mandatory Service
- **Given** a YAML config that is missing the `log_server` capability
- **When** `ValidateMandatoryServices()` is called
- **Then** it should return a "Mandatory Service missing" error
- **And** the service should abort startup

### Scenario 3: Missing Required Fields within a Service
- **Given** a `config_server` capability is present
- **But** it is missing the `ip` field
- **When** validation occurs
- **Then** it should detect the missing field within the schema
- **And** return a validation error

## 🛠️ Technical Constraints
- **Scope**: Validation MUST cover at least `log_server` and `config_server`.
- **Timing**: Validation MUST occur after YAML loading and environment expansion, but BEFORE any network activity or business logic starts.
