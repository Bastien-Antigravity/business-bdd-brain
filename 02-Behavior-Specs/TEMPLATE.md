--- 
type: spec
microservice: obsidian-brain

repo: [repo-name]
feature_id: FEAT-XXX
status: draft # [draft, approved, implemented, deprecated]
---

# Feature: [Feature Name]

## 🎯 Business Intent
- **User Story**: As a [role], I want to [action] so that [benefit].
- **Problem Solved**: [Why are we doing this?]

## 🎬 Scenarios (Gherkin)

### Scenario 1: [Happy Path]
- **Given** [precondition]
- **And** [additional context]
- **When** [action performed]
- **Then** [expected outcome]
- **And** [verification step]

### Scenario 2: [Edge Case / Error]
- **Given** [unstable condition]
- **When** [action performed]
- **Then** [graceful failure behavior]

## 🛠️ Technical Constraints (The "How" for AI)
- **Architecture**: [e.g., Must use gRPC, Must be thread-safe]
- **Performance**: [e.g., < 10ms latency]
- **Observability**: [e.g., Log Level INFO on success, ERROR on failure]

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Logic implemented according to scenarios.
- [ ] Unit tests pass for all scenarios.
- [ ] Parity verified across all facades (if applicable).
- [ ] Documentation updated in README/ARCHITECTURE.

## 🔗 Sandbox Binding
- **Feature**: `[sandbox-testing/features/FEAT-XXX-<name>.yaml]`
- **Implementation**: `sandbox-testing/implementations/<lang>/<test_file>`
