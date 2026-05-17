---
type: spec
microservice: business-bdd-brain
repo: distributed-config
feature_id: FEAT-007
status: draft
tags:
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: CGO / FFI Bridge (Polyglot Support)

## 🎯 Business Intent
- **User Story**: As a Python or Rust developer, I want to use the same configuration logic as the Go services without rewriting the complex discovery and sync code so that my service behaves identically to the rest of the ecosystem.
- **Problem Solved**: Ensures behavioral parity across different programming languages.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Handle-Based Session Management
- **Given** a Python application calling `libdistconf.so`
- **When** the application initializes a new configuration session
- **Then** the Go bridge should create a new `Config` instance
- **And** it should return an Opaque Handle (uintptr) to the caller
- **And** subsequent calls (Get, Set) must provide this handle to access the correct session state

### Scenario 2: JSON Pass-Through for Complex Data
- **Given** a request to get a nested "Capability" object from Rust
- **When** the Rust wrapper calls the CGO bridge
- **Then** the bridge should marshal the internal Go map/struct into a JSON string
- **And** it should return this JSON string to the caller
- **And** the caller should unmarshal it into its native language structures (e.g., Serde in Rust)

### Scenario 3: Memory Safety & Unloading
- **Given** a CGO bridge shared library
- **When** the parent process (Python/Rust) finishes a session
- **Then** it MUST call `DistConf_Close(handle)` to release the Go-side references
- **And** the library MUST NOT be unloaded via `dlclose` due to Go runtime limitations

## 🛠️ Technical Constraints
- **State Management**: MUST use a centralized `FacadeStore` in the Go bridge to share state between `libdistconf` and `libunilog`.
- **String Handling**: C-strings returned to the caller MUST be freed by the caller or managed via a buffer to prevent memory leaks.
