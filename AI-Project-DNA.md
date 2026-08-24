---
microservice: 08-Base-Scripts
type: note
status: active
tags:
- '#service/08-Base-Scripts'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---# Metadata
- Version: 0.0.1
- Classification: Orchestration

---
microservice: business-bdd-brain
type: dna
status: active
tags:
- '#zone/1-frozen'
- '#tier/dna'
- '#service/business-bdd-brain'
- '#type/dna'
- '#state/active'
---
# 🧬 AI Project DNA: Business-BDD

## 🎯 Intent
The primary intent of this zone is to provide a **Stateless Behavioral Baseline**. It is the "Source of Truth" for what the system does, decoupled from how it is built.

## ⚖️ Quality Gates
1. **Gherkin Compliance**: All behavioral specifications MUST use Given/When/Then syntax.
2. **Microservice Targeting**: Every file must explicitly name its target microservice in the `microservice` field.
3. **Immutability**: Once a spec is moved to `#zone/1-frozen`, it can only be modified through a formal **Strategic Pivot**.

## 🚫 Constraints
- **No Code**: This repository must remain 100% free of executable implementation logic.
- **No Infrastructure**: Do not document server paths, ports, or deployment details here; use the Tech-Stack for that.
