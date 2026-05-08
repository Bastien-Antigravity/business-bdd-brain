---
type: spec
microservice: obsidian-brain
repo: market-observer
feature_id: FEAT-001
status: draft
tags:
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Websocket Reconnection Logic

## 🎯 Business Intent
- **User Story**: As a market data engineer, I want the observer to automatically reconnect to exchange WebSockets if the connection drops, so that I don't lose critical market updates.
- **Problem Solved**: Prevents "Data Gaps" and manual intervention when exchange connections are unstable.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Automatic Reconnection on Disconnect
- **Given** an active WebSocket connection to "Binance"
- **When** the connection is lost due to a network timeout
- **Then** the observer must immediately enter a "Reconnecting" state
- **And** it must attempt to re-establish the connection using exponential backoff
- **And** it must log a "Websocket Disconnected" warning

### Scenario 2: Successful Recovery
- **Given** an observer in "Reconnecting" state
- **When** the connection is successfully re-established
- **Then** it must resubscribe to all previously active streams
- **And** it must log a "Websocket Reconnected" info message
- **And** it must resume data ingestion

### Scenario 3: Maximum Retry Alert
- **Given** an observer failing to reconnect for 5 consecutive attempts
- **When** the 5th attempt fails
- **Then** it must send a critical alert to the `notif-server`
- **And** it must continue retrying with the maximum backoff delay

## 🛠️ Technical Constraints
- **Mechanism**: MUST use exponential backoff with jitter.
- **Observability**: Connection state transitions must be logged.
- **Performance**: Re-subscription must happen in <100ms after connection.

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Logic implemented according to scenarios.
- [ ] Unit tests pass for reconnection loop.
- [ ] Integration with `notif-server` verified.

## 🔗 Sandbox Binding
- **Feature**: `[sandbox-testing/features/FEAT-001-ws-reconnect.yaml]`
