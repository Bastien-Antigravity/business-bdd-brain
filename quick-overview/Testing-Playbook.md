---
microservice: business-bdd-brain
type: architecture
status: active
tags:
- \'#zone/3-fleet\'
- '#ai/ignore'
- '#service/business-bdd-brain'
---

# Testing Playbook

The testing lifecycle for the Bastien-Antigravity fleet starts here.

## QA Protocol
1. **Specification**: Write behavior specs in this repository.
2. **Validation**: The AI QA Agent reads these specs to generate test skeletons.
3. **Execution**: Tests are run in `sandbox-testing` against the target microservice.

## Sentinel Audits
Daily audits ensure that:
- Every spec is indexed in the `Behavior-Specs-MOC.md`.
- Metadata follows the canonical template.
- Glossary terms match those used in the specs.
