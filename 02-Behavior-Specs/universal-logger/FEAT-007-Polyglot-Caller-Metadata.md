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
# Feature: Polyglot Caller Metadata Preservation

## 🎯 Business Intent
- **User Story**: As a platform developer writing services in Python, Rust, C++, or VBA, I want my logging statements to preserve their originating source file, line number, procedure, and module across the CGO boundary so that distributed traces accurately reflect polyglot code locations without manual string formatting.
- **Problem Solved**: Eliminates loss of stack frame information, prevents silent fallback to raw messages, and preserves accurate caller telemetry across foreign function interfaces.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Preserving Polyglot Caller Context across FFI
- **Given** a logging client executing in Python, Rust, C++, or VBA
- **When** the client logs a message using native inspection (e.g. `sys._getframe`, `file!()`, `__FILE__`)
- **Then** the facade must transmit the filename, line number, function name, and module via `UniLog_LogWithMetadata`
- **And** the central Go engine must preserve these details without truncation into the output sink

### Scenario 2: Unwrapping Wrapped Engine Profiles
- **Given** a logger instance wrapped in a notification layer (e.g. `*profiles.NotifLoggerWrapper`)
- **When** `LogWithMetadata` receives a log call with caller attributes
- **Then** the dispatcher must unwrap the underlying `LogEngine` or invoke `LogWithCaller` directly
- **And** all stack frame attributes must reach the concrete sink formatters intact

### Scenario 3: Graceful Degradation on Non-Caller Sinks
- **Given** an underlying logger that does not implement the `CallerLogger` contract
- **When** `LogWithMetadata` attempts caller dispatch
- **Then** the utility must safely fall back to standard `logger.Log(level, format)`
- **And** it must not panic, crash, or discard the log payload

## 🛠️ Technical Constraints
- **FFI Stability**: C export signatures must maintain 64-bit integer level types across polyglot boundaries (`level int` / `c_longlong`).
- **Zero Allocation**: String pointers passed across CGO must be converted to Go strings without leaking heap memory.
