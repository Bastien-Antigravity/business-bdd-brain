---
type: spec
microservice: universal-logger
repo: universal-logger
feature_id: FEAT-010
status: draft
tags:
- '#service/universal-logger'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#zone/1-frozen'
---
# Feature: Cross-Platform Compilation (C-Archive)

## 🎯 Business Intent
- **User Story**: As a CI/CD engineer, I want to compile the shared library for Windows, Linux, and Mac from a single command so that we always have up-to-date binaries for all developers.
- **Problem Solved**: Centralizes the complex CGO build flags needed for different operating systems.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Compiling for Windows (DLL)
- **Given** a Go environment with `CC=x86_64-w64-mingw32-gcc`
- **When** `make build-windows` is executed
- **Then** the Go compiler must produce a `.dll` file and a `.h` header
- **And** it must include the correct export symbols for the C ABI

### Scenario 2: Compiling for Linux/Mac (Shared Object)
- **Given** a Unix environment
- **When** `make build-unix` is executed
- **Then** it must produce a `.so` (Linux) or `.dylib` (Mac) file
- **And** it must ensure that the shared library is Position Independent (`-fPIC`)

### Scenario 3: Header File Generation
- **Given** a `go build` command with `-buildmode=c-shared`
- **When** the compilation finishes
- **Then** a `.h` file must be generated alongside the binary
- **And** this header must contain all `//export`ed functions from the `cgo_bridge` to be used by C++ and Rust

## 🛠️ Technical Constraints
- **Mode**: MUST use `-buildmode=c-shared` for dynamic libraries and `-buildmode=c-archive` for static linking (Rust).
- **Automation**: MUST be managed via the root `Makefile`.
