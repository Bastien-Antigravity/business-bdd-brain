---
type: spec
microservice: obsidian-brain
repo: market-observer
feature_id: FEAT-003
status: draft
tags:
- \'#service/obsidian-brain\'
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Heartbeat Monitoring

## 🎯 Business Intent
- **User Story**: As a system administrator, I want the observer to send periodic heartbeats to the `log-server`, so that I can verify it is still alive and healthy even if there is no market activity.
- **Problem Solved**: Detects "Silent Failures" where the process is running but the logic or network is hung.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Periodic Heartbeat Emission
- **Given** a healthy market observer
- **When** the heartbeat interval (30s) is reached
- **Then** it must send a `HEARTBEAT` frame to the configured `log-server`
- **And** the frame must include the service ID and current status

### Scenario 2: Heartbeat Failure Alert
- **Given** a network failure prevents the heartbeat from being sent
- **When** the send operation fails
- **Then** it must attempt to reconnect to the `log-server`
- **And** it must log a "Heartbeat Failed" error locally

### Scenario 3: Status Reflection
- **Given** the observer is in "Reconnecting" state with an exchange
- **When** sending a heartbeat to the `log-server`
- **Then** the heartbeat status field must reflect "WARNING: EXCHANGE_DISCONNECTED"
- **And** it must NOT report "HEALTHY"

## 🛠️ Technical Constraints
- **Protocol**: MUST use the `SafeSocket` heartbeat frame or a dedicated Cap'n Proto message.
- **Interval**: 30 seconds (configurable).

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Heartbeat thread runs independently of data ingestion.
- [ ] Status correctly reflects internal state.

## 🔗 Sandbox Binding
- **Feature**: `[sandbox-testing/features/FEAT-003-heartbeat.yaml]`
