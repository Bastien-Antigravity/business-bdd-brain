---
type: spec
microservice: business-bdd-brain
repo: microservice-toolbox
feature_id: FEAT-005
status: draft
tags:
- \'#zone/3-fleet\'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: In-Memory Configuration Mirroring

## 🎯 Business Intent
- **User Story**: As a Python developer, I want to read configuration values at high frequency without paying the performance penalty of a CGO/FFI call for every single read.
- **Problem Solved**: Eliminates the overhead of cross-language communication by caching the state in the native language's memory.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Initial Sync on Startup
- **Given** a Python microservice starting up
- **When** the toolbox initializes the configuration engine (Go side)
- **Then** the Python wrapper should fetch the entire configuration state as a JSON string
- **And** it should unmarshal this JSON into a native Python dictionary
- **And** all future reads within that process should use the Python dictionary

### Scenario 2: Synchronizing Live Updates
- **Given** an active Python service with a mirrored configuration
- **When** a remote update arrives at the Go core (via Sync/Network)
- **Then** the Go core must trigger a callback in the Python wrapper
- **And** the Python wrapper must update its local dictionary to reflect the new system state

### Scenario 3: Memory Consistency
- **Given** a mirrored configuration state
- **When** the application reads a value
- **Then** it must be guaranteed that the value is identical to what the Go core would return at that same moment (Snapshot Consistency)

## 🛠️ Technical Constraints
- **Format**: JSON should be used as the exchange format for mirroring to simplify cross-language mapping.
- **Efficiency**: Use callbacks or signals to update the mirror instead of polling the Go core.
