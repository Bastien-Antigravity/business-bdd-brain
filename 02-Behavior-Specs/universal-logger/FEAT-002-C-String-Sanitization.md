---
type: spec
microservice: business-bdd-brain
repo: universal-logger
feature_id: FEAT-002
status: draft
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---
# Feature: C-String Sanitization & Trimming

## 🎯 Business Intent
- **User Story**: As a polyglot developer, I want to pass strings from Python or VBA to the Go core without worrying about invisible characters (like NULL terminators or extra spaces) breaking my configuration lookups.
- **Problem Solved**: Prevents "Invisible Key Failures" where a profile like `"production"` fails to load because it was passed as `"production\0"` from the C boundary.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Trimming NULL Terminators
- **Given** a string `profile_name` passed from C/C++ containing a trailing `\0`
- **When** the string enters the `cgo_bridge`
- **Then** the bridge must automatically detect and remove the NULL terminator
- **And** the resulting Go string must be clean (e.g. `"prod"`, not `"prod\x00"`)

### Scenario 2: Trimming Whitespace
- **Given** a user accidentally passes a profile as `"  staging "` from a VBA text box
- **When** it enters the bridge
- **Then** the bridge must perform a `TrimSpace()` operation
- **And** it must use the resulting `"staging"` for strategy lookup

### Scenario 3: Empty String Safety
- **Given** a NULL pointer or empty string passed from the FFI
- **When** sanitization occurs
- **Then** it should return a safe, empty Go string `""`
- **And** it must NOT cause a pointer dereference panic

## 🛠️ Technical Constraints
- **Performance**: Sanitization must be high-performance (zero-allocation if the string is already clean).
- **Scope**: MUST be applied to all `char*` inputs in the `cgo_bridge`.
