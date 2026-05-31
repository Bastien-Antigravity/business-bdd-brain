---
type: spec
microservice: universal-logger
repo: universal-logger
feature_id: FEAT-011
status: draft
tags:
- '#service/universal-logger'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#zone/1-frozen'
---
# Feature: FFI Memory & Buffer Management

## 🎯 Business Intent
- **User Story**: As a C++ developer, I want to receive strings from the Go library without causing memory leaks, so that my long-running application remains stable over time.
- **Problem Solved**: Defines a strict "Owner-to-User" protocol for memory passing between Go and other languages.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Returning Strings to C (Ownership)
- **Given** a Go function returning a `*C.char` (e.g., `DistConf_Get`)
- **When** the string is created using `C.CString()`
- **Then** the Go core MUST NOT attempt to free this memory automatically (to prevent Use-After-Free in the caller)
- **And** the caller (Python/C++/Rust) becomes the owner of this memory

### Scenario 2: Explicit Freeing (The Free Protocol)
- **Given** a pointer received from the Go library
- **When** the application has finished using the data
- **Then** it MUST call `DistConf_FreeString(ptr)` to return the memory to the Go allocator (via C.free)
- **And** failure to do so must be recorded as a critical leak in the developer logs

### Scenario 3: Large Buffer Safety
- **Given** a request for a large JSON configuration snapshot
- **When** the data exceeds the standard C-stack size
- **Then** the Go bridge must allocate the buffer on the heap
- **And** it must ensure the buffer is NULL-terminated before passing the pointer to the FFI boundary

## 🛠️ Technical Constraints
- **Function**: `DistConf_FreeString` MUST be exported in the C ABI.
- **Safety**: Go's GC cannot see the memory once it is passed as a `*C.char`, so manual tracking in the native wrappers (e.g. using `ctypes` in Python) is mandatory.
