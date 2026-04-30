---
microservice: business-bdd-brain
type: repository
status: active
tags:
  - domain/bdd
  - domain/testing
  - type/architecture
---

# 🧪 Business BDD Brain

Welcome to the **Business BDD Brain**. This repository is **Tier 3** of the 4-Tier AI-KMS Architecture.

This repository serves as the **Behavior Specification Layer** for the Bastien-Antigravity ecosystem. It defines *what* the system should do in human-readable Behavior-Driven Development (BDD) format, using markdown-based Given/When/Then specifications.

## 🏗️ Architecture & Behavior

This brain is **100% Stateless** and contains no executable code. It stores pure behavioral expectations that are consumed by two downstream systems:

1. **AI QA Agent** (`core-kms-brain/Role-Prompts/04-QA/`): Reads the behavior specs to generate executable test skeletons.
2. **Sandbox Testing** (`sandbox-testing/`): Executes the generated tests against real infrastructure (Native or Docker mode).

## 📦 What's Inside?

- **`01-Domain-Glossary/`**: Domain-Driven Design ubiquitous language definitions. Shared terminology across the entire ecosystem.
- **`02-Behavior-Specs/`**: The core BDD specifications organized by microservice domain. Each `.md` file defines expected behaviors using Given/When/Then syntax with Obsidian metadata.
- **`03-Acceptance-Criteria/`**: High-level acceptance criteria and user stories that map to behavior specs.

## 🔗 How it connects to the Ecosystem

This repository is injected as a **Git Submodule** into the master `obsidian-brain` project. When the AI QA Agent processes a task, it reads the behavior specifications from this repository to ensure generated tests perfectly validate the expected system behavior.

The flow is:
```
business-bdd-brain (WHAT) → sandbox-testing (HOW) → microservice (CODE)
```

For detailed instructions on writing behavior specs, please read the **[[User-Manual.md]]**.
