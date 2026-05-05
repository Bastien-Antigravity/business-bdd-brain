--- 
type: spec
microservice: obsidian-brain

repo: distributed-config
feature_id: FEAT-004
status: draft
---

# Feature: Live Configuration Sync & RCU

## 🎯 Business Intent
- **User Story**: As a platform engineer, I want to update configuration settings (like feature flags or log levels) at runtime without restarting the service so that I can respond to incidents instantly.
- **Problem Solved**: Enables zero-downtime configuration updates using a non-blocking, thread-safe synchronization mechanism.

## ✅ Acceptance Criteria (Definition of Done)
- [x] Logic implemented according to scenarios.
- [ ] **FAILED AUDIT (2026-05-02)**: GetAddress/GetCapability helpers cache static YAML values and ignore live updates.
- [ ] Unit tests pass for all scenarios.
- [ ] RCU atomic swap verified.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Non-Blocking Read (RCU)
- **Given** a high-throughput microservice performing 10k config reads per second
- **When** a remote `BROADCAST_SYNC` update arrives from the Config Server
- **Then** the library must perform an atomic pointer swap to the new configuration map
- **And** existing readers must continue to see their consistent snapshot without any locking overhead
- **And** new readers must immediately see the updated values

### Scenario 2: User Callbacks on Update
- **Given** an application has registered a callback for the key `log_level`
- **When** the `log_level` is updated via remote sync
- **Then** the library must trigger the registered callback with the new value
- **And** the callback must be executed in a non-blocking manner (or handled by the user's thread pool)

### Scenario 3: Local to Remote Propagation (Production Only)
- **Given** a service running in "production"
- **When** the application calls `cfg.Set("my_key", "my_value")`
- **Then** the library must update the local `LiveConfig` map
- **And** it must immediately attempt to push this update to the remote Config Server via `PUT_SYNC`

## 🛠️ Technical Constraints
- **Concurrency**: MUST use `atomic.Pointer` (Read-Copy-Update) for the configuration storage.
- **Transport**: Remote sync MUST use the `safe-socket` framed TCP protocol.
- **Format**: Dynamic updates MUST use a flexible JSON-based key-value structure.
