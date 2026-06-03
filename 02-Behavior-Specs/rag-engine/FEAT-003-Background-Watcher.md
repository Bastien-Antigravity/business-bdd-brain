---
type: spec
microservice: obsidian-brain
repo: obsidian-brain
feature_id: FEAT-003
status: draft
tags:
- '#service/obsidian-brain'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#zone/3-fleet'
---
# Feature: Background File Watcher

## 🎯 Business Intent
- **User Story**: As an active developer, I want the RAG engine to automatically detect when I modify files and re-index them in the background, so that my search results are always up to date without manual intervention.
- **Problem Solved**: Keeps the knowledge base synchronized with the live workspace state.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Detecting File Modifications
- **Given** the background watcher is running
- **When** a tracked file in the workspace is modified or created
- **Then** the watcher must detect the filesystem event
- **And** it must dispatch the file path to the indexing pipeline callback.

### Scenario 2: Exclusion Filtering
- **Given** a file modification event
- **When** the watcher processes the event path
- **Then** it must check the path against the GLOBAL_EXCLUDES list
- **And** if the path matches an exclusion pattern or is a hidden file, it must ignore the event and do nothing.

## 🛠️ Technical Constraints
- **Integration**: Must safely execute the async indexing callback from the synchronous watchdog thread.
- **Filtering**: Must aggressively filter out hidden folders (e.g. .git, .venv) to prevent infinite indexing loops.

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Saving a file triggers the indexing pipeline automatically.
- [ ] Modifying a file in a .git or excluded folder does not trigger indexing.
- [ ] The async loop bridge is stable and does not cause runtime errors.

## 🔗 Sandbox Binding
- **Feature**: `[tests/features/FEAT-003-watcher.yaml]`
