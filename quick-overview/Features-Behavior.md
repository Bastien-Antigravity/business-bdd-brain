---
microservice: business-bdd-brain
type: architecture
status: active
tags:
- '#ai/ignore'
- '#service/business-bdd-brain'
---

# Features & Behavior

This repository organizes behavioral expectations by microservice domain.

## Organization
- Each subdirectory in `02-Behavior-Specs/` corresponds to a microservice in the fleet.
- Files follow the `FEAT-NNN-Name.md` convention.
- Content must follow the `Given/When/Then` syntax for clear, testable expectations.

## Key Domains
- **Distributed Config**: Management of environment-first bootstrapping.
- **Universal Logger**: Standardization of logging interfaces.
- **SafeSocket**: Reliable TCP transport protocols.
- **Microservice toolbox**: Default tools for microservices.
