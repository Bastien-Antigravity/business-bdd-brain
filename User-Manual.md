---
title: "BDD Brain User Manual"
type: architecture
status: active
microservice: business-bdd-brain
---

# 📖 Business BDD Brain: User Manual

This document explains how to write and organize Behavior-Driven Development (BDD) specifications using markdown.

## 1. What is BDD?

Behavior-Driven Development defines the **expected behavior** of a system in plain language before any code is written. We then run tests to check if the system matches those expectations.

The cycle is:
1. **Define** behavior specs in `business-bdd-brain`.
2. **Generate** test skeletons via the AI QA Agent.
3. **Execute** tests in `sandbox-testing`.
4. **Verify** the microservice code passes the specs.

## 2. Writing a Behavior Spec

All behavior specs live in `02-Behavior-Specs/` and use the following markdown template:

```markdown
---
title: "Feature Name"
type: behavior-spec
status: active
microservice: <target-microservice>
priority: high | medium | low
tags:
  - domain/<domain-name>
  - type/behavior-spec
---

# Feature: <Feature Name>

## Context
Brief description of the feature and why it exists.

## Behavior

### Scenario: <Scenario Name>
- **Given** <initial context or precondition>
- **When** <action or event occurs>
- **Then** <expected outcome>
- **And** <additional outcome (optional)>

### Scenario: <Another Scenario>
- **Given** ...
- **When** ...
- **Then** ...

## Edge Cases
- What happens when <unusual condition>?
- What happens when <failure condition>?
```

## 3. Organizing Specs

Behavior specs are organized by **microservice domain**:
- `02-Behavior-Specs/log-server/` — All specs for the log-server.
- `02-Behavior-Specs/config-server/` — All specs for the config-server.
- `02-Behavior-Specs/safe-socket/` — All specs for safe-socket.

## 4. Linking to Sandbox Testing

Each behavior spec can be linked to its corresponding test scenario in `sandbox-testing` using standard Obsidian links:
- **Spec → Test**: Add `[[sandbox-testing/scenarios/<scenario-file>]]` in the behavior spec.
- **Test → Spec**: Add `[[business-bdd-brain/02-Behavior-Specs/<spec-file>]]` in the test scenario.

## 5. Domain Glossary

The `01-Domain-Glossary/` directory maintains a shared vocabulary (Ubiquitous Language) across all specs. When introducing a new domain term, add it to the glossary to keep specs consistent.

## 6. Acceptance Criteria

High-level acceptance criteria and user stories live in `03-Acceptance-Criteria/`. These are broader than individual behavior specs and typically map to epics or features that span multiple microservices.
