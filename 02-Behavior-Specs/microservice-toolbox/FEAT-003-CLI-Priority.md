---
type: spec
microservice: business-bdd-brain
repo: microservice-toolbox
feature_id: FEAT-003
status: draft
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---
# Feature: CLI Flag Precedence

## 🎯 Business Intent
- **User Story**: As an operator, I want to override any configuration setting using a command-line flag so that I can quickly fix issues or experiment without editing YAML files.
- **Problem Solved**: Provides an "Ultimate Override" mechanism for runtime control.

## 🎬 Scenarios (Gherkin)

### Scenario 1: CLI Overriding YAML
- **Given** a YAML configuration with `port: 8080`
- **When** the microservice is started with `--port 9090`
- **Then** the final configuration value for `port` must be "9090"
- **And** the YAML value should be ignored for this session

### Scenario 2: Standard Flag Set
- **Given** any microservice using the toolbox
- **When** the application starts
- **Then** it must automatically support these standard flags:
    - `--host`: Binds the server to an IP.
    - `--port`: Sets the listener port.
    - `--conf`: Path to a custom YAML file.
    - `--key`: Path to the RSA private key.
    - `--profile`: Active environment profile (prod, test, etc.).

### Scenario 3: Flag Consistency Across Languages
- **Given** a Go service and a Python service
- **When** both are started with `--port 7070`
- **Then** both must correctly parse and apply the port override
- **And** the parsing logic (flag names and behavior) must be identical

## 🛠️ Technical Constraints
- **Parser**: Use standard flag libraries (e.g. `flag` in Go, `argparse` in Python) but wrap them to ensure naming parity.
- **Priority**: CLI Flags MUST have higher priority than YAML and Remote Sync values.
