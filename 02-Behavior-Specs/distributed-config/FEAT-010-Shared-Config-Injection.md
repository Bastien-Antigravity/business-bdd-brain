---
microservice: 08-Base-Scripts
type: note
status: active
tags:
- '#service/08-Base-Scripts'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---
# Feature: Shared Config Injection (JSON)

## 🎯 Business Intent
- **User Story**: As a developer, I want to inject complex, nested configuration data into the live system using a simple JSON string so that I can easily synchronize state between the application and the configuration engine.
- **Problem Solved**: Provides a flexible, language-agnostic way to update many configuration keys at once without multiple individual `Set()` calls.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Injecting a Flat JSON Map
- **Given** a JSON string `{"key1": "val1", "key2": "val2"}`
- **When** `ShareConfig(handle, jsonData)` is called
- **Then** the library should unmarshal the JSON
- **And** it should merge these keys into the `shared` section of the `LiveConfig`
- **And** existing values should be overwritten by the new ones

### Scenario 2: Injecting Nested Sections
- **Given** a nested JSON structure `{"database": {"host": "localhost", "port": "5432"}}`
- **When** `ShareConfig` is called
- **Then** the library should detect the nested map
- **And** it should create/update the `database` section in the `LiveConfig`
- **And** it should populate it with `host` and `port` keys

### Scenario 3: Malformed JSON Handling
- **Given** an invalid JSON string `{"key": "val"` (missing closing brace)
- **When** `ShareConfig` is called
- **Then** the library should return a "JSON Unmarshal Error"
- **And** the `LiveConfig` should remain unchanged (Atomic safety)

## 🛠️ Technical Constraints
- **Format**: MUST support both flat and nested JSON objects.
- **Atomicity**: The entire JSON payload MUST be applied in a single RCU pointer swap.
- **Casting**: All non-string values in the JSON (numbers, booleans) should be converted to strings for storage in the KV map.
