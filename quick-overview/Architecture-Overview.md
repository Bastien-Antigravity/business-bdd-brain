---
title: Architecture Overview - Business-BDD
microservice: business-bdd-brain
type: architecture
status: active
tags:
- '#zone/1-frozen'
- '#service/business-bdd-brain'
- '#ai/ignore'
- '#state/active'
- '#type/architecture'
---
# ⚖️ Business-BDD: Architecture Overview

The **Business BDD Brain** (02-Business-BDD) serves as the source of truth for the **Behavior Specification Layer** in the Bastien-Antigravity ecosystem.

## Core Pillars
1. **Ubiquitous Language**: Defined in the `01-Domain-Glossary/`, ensuring alignment between business logic and implementation.
2. **Behavior Specs**: Located in `02-Behavior-Specs/`, these Gherkin-style markdown files define the *what* of the system.
3. **Stateless Governance**: This repository contains no executable code, only behavioral logic that is consumed by the AI QA Agent and validated in the Sandbox.

## Execution Flow
Specs from this repo are bound to `sandbox-testing/` features, which are then executed against real microservices to ensure compliance with business intent.
