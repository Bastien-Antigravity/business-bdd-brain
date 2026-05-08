---
type: spec
microservice: obsidian-brain
repo: config-server
feature_id: FEAT-002
status: draft
tags:
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Atomic State Swap (Read Consistency)

## 🎯 Business Intent
- **User Story**: As a platform engineer, I want the Config Server to remain responsive even during large configuration updates, so that services reading the configuration never experience latency or "partial" updates.
- **Problem Solved**: Eliminates "Dirty Reads" where a service might see a half-updated configuration state.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Non-Blocking Reads
- **Given** a service performing a `GET_DUMP` request (reading 1000+ keys)
- **When** simultaneously a `SET_UPDATE` occurs for a subset of those keys
- **Then** the reader must receive a consistent snapshot of the state as it existed at the start of the request
- **And** the reader must NOT be blocked by the writer

### Scenario 2: Atomic Update (Copy-on-Write)
- **Given** an existing configuration `S1`
- **When** an update `U` arrives
- **Then** the server must create a copy of `S1`
- **And** apply `U` to the copy to create `S2`
- **And** use `atomic.Value` or a pointer swap to replace the global state with `S2`
- **And** the switch from `S1` to `S2` must be instantaneous for all new requests

### Scenario 3: Update Concurrency (Serialized Writers)
- **Given** two simultaneous update requests from different clients
- **When** they attempt to write to the store
- **Then** the server must serialize these writes (e.g. via a mutex) to prevent lost updates
- **And** the final state must reflect the chronological order of the updates

## 🛠️ Technical Constraints
- **Mechanism**: MUST use Go's `atomic.Value` or `sync.RWMutex` with a pointer swap.
- **Efficiency**: Deep copies should only be performed if an update actually occurs.
