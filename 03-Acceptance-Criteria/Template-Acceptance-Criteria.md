---
title: Acceptance Criteria Template
type: acceptance-criteria
status: template
microservice: <target-microservice>
tags:
- \'#service/<target-microservice>\'
- type/acceptance-criteria
- '#type/acceptance-criteria'
- '#state/template'
---

# Acceptance Criteria: <Feature Name>

## Overview
Brief description of the feature or epic.

## Criteria

### AC-01: <Criteria Title>
- **Description**: What must be true for this criterion to pass.
- **Behavior Specs**: [02-Behavior-Specs/<microservice>/<spec-file>]
- **Sandbox Scenario**: [sandbox-testing/scenarios/<scenario-file>]

### AC-02: <Criteria Title>
- **Description**: ...
- **Behavior Specs**: ...
- **Sandbox Scenario**: ...

## Definition of Done
- [ ] All behavior specs pass in `sandbox-testing`.
- [ ] Documentation updated in the relevant microservice `README.md`.
- [ ] Session state updated in `AI-Session-State.md`.
