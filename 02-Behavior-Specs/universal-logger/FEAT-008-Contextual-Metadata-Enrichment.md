---
microservice: universal-logger
type: spec
status: active
tags:
- '#service/universal-logger'
- '#domain/observability'
- '#layer/library'
- '#type/spec'
- '#state/active'
- '#zone/3-fleet'
---
# Feature: Contextual Metadata Enrichment

## 🎯 Business Intent
- **User Story**: As a platform operator, I want microservices to attach contextual key-value metadata to logger instances safely under concurrent execution so that structured tags (environment, version, worker IDs) are automatically appended to all log records.
- **Problem Solved**: Eliminates race conditions during metadata mutation, ensures thread-safety under heavy concurrent workloads, and provides automated tag formatting across all polyglot runtimes.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Thread-Safe Concurrent Metadata Mutation
- **Given** an active `UniLog` instance shared across multiple concurrent workers
- **When** workers concurrently invoke `AddMetadata`, `SetMetadata`, and `GetMetadata`
- **Then** the internal metadata map must be protected by reader-writer locks (`RWMutex`)
- **And** no data race, panic, or concurrent map read/write violation must occur

### Scenario 2: Bulk JSON Ingestion via FFI
- **Given** a polyglot caller providing a JSON-serialized metadata dictionary
- **When** passed through `UniLog_SetMetadata`
- **Then** the CGO layer must parse and validate the JSON object
- **And** the logger must atomically replace its metadata store with the new key-value pairs

### Scenario 3: Automated Contextual Formatting in Sinks
- **Given** a logger instance with configured metadata tags (e.g. `env=staging`, `worker=42`)
- **When** any log statement is executed
- **Then** the message formatter must append deterministic, alphabetically sorted metadata tags `[meta: env=staging worker=42]`
- **And** raw messages must remain untouched when no metadata is registered

## 🛠️ Technical Constraints
- **Concurrency**: Guarded with `sync.RWMutex` to allow concurrent log readers without blocking.
- **Serialization**: Keys must be sorted deterministically to guarantee consistent log line hashing and diffing.
