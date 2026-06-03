---
type: spec
microservice: obsidian-brain
repo: obsidian-brain
feature_id: FEAT-004
status: draft
tags:
- '#service/obsidian-brain'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#zone/3-fleet'
---
# Feature: Documentation Drift Alignment

## 🎯 Business Intent
- **User Story**: As an engineer, I want the system to warn me if the documentation I am reading describes code that has since been modified, so I don"t rely on obsolete knowledge.
- **Problem Solved**: Solves the classic "stale documentation" problem by tracking cryptographic hashes of source code at the moment documentation is written.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Registering a Link
- **Given** a code chunk and a new Obsidian documentation note
- **When** the user explicitly registers a link via the MCP tool
- **Then** the alignment service must compute the SHA-256 hash of the current raw code
- **And** it must store the bidirectional mapping (code_id <-> doc_id) along with the hash in SQLite.

### Scenario 2: Detecting Documentation Drift
- **Given** an existing link between a code chunk and a document
- **When** the code chunk content is modified and re-indexed
- **Then** the alignment service must compare the new content hash against the stored hash
- **And** if they differ, the alignment status must be reported as drifted.

### Scenario 3: Orphan Link Detection
- **Given** an alignment link in the database
- **When** the original source code file is deleted or renamed
- **Then** an audit sweep must identify that the code_id no longer exists
- **And** it must flag the linked document as an Orphan.

## 🛠️ Technical Constraints
- **Hash Algorithm**: Must use SHA-256 for deterministic content hashing.
- **Storage**: Links must be persisted in alignment_registry.db.

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Drift is correctly detected immediately after code is modified.
- [ ] Updating the documentation and re-registering the link clears the drift status.
- [ ] The MCP query tool surfaces the drift warning directly to the user.

## 🔗 Sandbox Binding
- **Feature**: `[tests/features/FEAT-004-alignment.yaml]`
