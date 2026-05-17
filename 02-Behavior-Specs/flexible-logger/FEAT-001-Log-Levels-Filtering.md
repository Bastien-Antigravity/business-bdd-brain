---

type: spec
microservice: business-bdd-brain
repo: flexible-logger
feature_id: FEAT-001
status: draft
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---
# Feature: Log Level Filtering

## 🎯 Business Intent
- **User Story**: As a developer, I want to control the verbosity of my logs (e.g., see Debug in dev but only Errors in production) so that I can manage log volume and storage costs without changing code.
- **Problem Solved**: Prevents log spam in high-traffic environments while maintaining visibility into critical failures.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Filtering by Minimum Level
- **Given** a logger configured with a minimum level of `INFO`
- **When** the application sends a `DEBUG` message
- **Then** the message should be dropped early in the `LogEngine`
- **And** it should NOT be sent to any Sinks or Notifiers
- **But** when the application sends an `ERROR` message
- **Then** it should be accepted and processed

### Scenario 2: Level Hierarchy
- **Given** a log level `L`
- **When** comparing levels
- **Then** the priority must follow this order: `DEBUG` < `INFO` < `WARNING` < `ERROR` < `CRITICAL`
- **And** a logger set to `L` must accept all messages where `MessageLevel >= L`

### Scenario 3: Panic/Fatal Behavior
- **Given** a message at `CRITICAL` level
- **When** logged via `Log.Fatal()`
- **Then** the message must be flushed to all sinks immediately
- **And** the application must terminate with exit code 1

## 🛠️ Technical Constraints
- **Performance**: Filtering MUST happen before any string formatting (`fmt.Sprintf`) or object allocation to minimize CPU overhead for dropped logs.
- **Thread Safety**: Level checks must be atomic to allow runtime level updates.
