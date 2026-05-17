---
type: spec
microservice: business-bdd-brain
repo: universal-logger
feature_id: FEAT-004
status: draft
tags:
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: FFI Session Handle Lifecycle (RAII)

## 🎯 Business Intent
- **User Story**: As a C++ or Rust developer, I want my logger session to be automatically cleaned up when the object goes out of scope so that I don't have to remember to call `Close()` and avoid memory leaks.
- **Problem Solved**: Centralizes resource management and prevents "Ghost Sessions" in the Go core.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Automatic Release on Drop (Rust/C++)
- **Given** a `UniLog` object in Rust or a C++ RAII wrapper
- **When** the object goes out of scope or is explicitly dropped
- **Then** the destructor must call the FFI `DistConf_Close(handle)` method
- **And** the Go core must immediately remove the session from its `HandleMap`
- **And** all background network connections for that session must be terminated

### Scenario 2: Handle Map Concurrency
- **Given** multiple threads in Python creating and destroying loggers simultaneously
- **When** handles are generated and deleted
- **Then** the `cgo_bridge` MUST use a thread-safe map to manage the `uintptr -> Session` mapping
- **And** it must ensure that a handle ID is never reused until it has been fully released

### Scenario 3: Zombie Handle Protection
- **Given** an application that attempts to use a handle that has already been closed
- **When** an FFI call is made (e.g. `Log(handle, ...)` )
- **Then** the bridge must detect the missing handle
- **And** it must return a specific "Invalid Handle" error code
- **And** it must NOT crash the process

## 🛠️ Technical Constraints
- **Uniqueness**: Handles MUST be unique for the lifetime of the process.
- **Cleanup**: The `Close` operation must be idempotent (safe to call multiple times).
