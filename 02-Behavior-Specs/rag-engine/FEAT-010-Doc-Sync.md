---
type: spec
microservice: rag-engine
feature_id: FEAT-010
status: draft
tags:
- '#zone/2-brain'
- '#service/rag-engine'
- '#state/draft'
- '#type/spec'
---
# Feature: Code-Doc Semantic Synchronizer

## 🎯 Business Intent
- **User Story**: As an Architect, I want my Obsidian documentation to automatically reflect the current state of the codebase, including semantic relationships and manual links, so that documentation never drifts from reality.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Skeleton Generation for New Code
- **Given** a new source file exists in the repository
- **When** the `sync-docs` command is executed
- **Then** a new file in `obsidian-brain/11-Code-Doc/` must be created.

### Scenario 2: Graph Context Injection
- **When** the `sync-docs` command is executed
- **Then** the section `## 🏗️ Architectural Context` must list dependencies and consumers.

### Scenario 3: Idempotency
- **When** the `sync-docs` command is executed multiple times
- **Then** manual notes outside sync markers must remain untouched.

### Scenario 4: Tag Extraction
- **Given** a source file containing `@obsidian [[Link]]`
- **When** the `sync-docs` command is executed
- **Then** a link must be registered in the `alignment_links` database table.

### Scenario 5: Intent-First File Description Injection
- **Given** a source file containing an `ESSENTIAL PROCESS:` header
- **When** the `sync-docs` command is executed
- **Then** the `## 📝 Description` section in the mirror note must contain the extracted essential process text.

### Scenario 6: Symbol Intent Summary Annotation
- **Given** a symbol with an accompanying docstring or doc comment
- **When** the `sync-docs` command is executed
- **Then** the outbound dependency link for that symbol must be annotated with a 1-line intent summary.

