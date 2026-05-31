---
type: spec
microservice: universal-logger
repo: universal-logger
feature_id: FEAT-006
status: draft
tags:
- '#service/universal-logger'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#zone/1-frozen'
---
# Feature: Manual Configuration Injection

## 🎯 Business Intent
- **User Story**: As a developer, I want to manually inject a configuration object into the logger instead of having it search for files, so that I can have total control over the settings in highly specialized environments.
- **Problem Solved**: Provides an "Escape Hatch" from the automatic file-based discovery.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Injecting Config via Handle
- **Given** an active `distributed-config` handle (ID: 42)
- **When** initializing a new `UniversalLogger`
- **Then** the bootstrap should accept handle `42` as a parameter
- **And** it should link the new logger to the existing configuration session
- **And** it should NOT attempt to load any new YAML files

### Scenario 2: Parity across Shared Sessions
- **Given** a configuration session that is updated via `ShareConfig(json_data)`
- **When** the linked logger processes its next message
- **Then** it must use the updated parameters (e.g. new log level or new target IP)
- **And** both components must reflect the same internal state

### Scenario 3: Invalid Handle Rejection
- **Given** an invalid or expired config handle
- **When** injection is attempted
- **Then** the bootstrap must reject the injection
- **And** it should return a "Configuration Session Not Found" error

## 🛠️ Technical Constraints
- **Architecture**: The `LogEngine` must accept an `interfaces.Config` interface to allow for both real and mocked configuration objects.
