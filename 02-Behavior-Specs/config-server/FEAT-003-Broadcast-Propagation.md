--- 
type: spec
microservice: obsidian-brain

repo: config-server
feature_id: FEAT-003
status: draft
---

# Feature: Broadcast Propagation (Push Updates)

## 🎯 Business Intent
- **User Story**: As a developer, I want my service to automatically receive new configuration values as soon as they change on the server, so that I can react to feature flags or secret rotations without restarting my service.
- **Problem Solved**: Eliminates "Configuration Lag" and the need for expensive polling.

## 🎬 Scenarios (Gherkin)

### Scenario 1: State Propagation
- **Given** 10 microservices connected to the Config Server
- **When** a configuration key `market_open` is changed via an admin tool
- **Then** the Config Server must immediately identify all active listener sockets
- **And** it must push a `PROPAGATE_UPDATE` message to every socket
- **And** the message must contain the new key/value pair

### Scenario 2: Handling Disconnected Clients
- **Given** a service that was just disconnected (e.g. crash)
- **When** a broadcast occurs
- **Then** the server must detect the closed socket
- **And** it must gracefully remove the socket from the `Listeners` pool
- **And** it must NOT allow one dead socket to delay the broadcast to other healthy sockets (Async Broadcast)

### Scenario 3: Partial vs Full Propagation
- **Given** a single key update
- **When** broadcasting
- **Then** the server should prefer "Delta Updates" (sending only the changed key) to save bandwidth
- **But** it must support "Full Refresh" if requested by a client upon reconnection

## 🛠️ Technical Constraints
- **Concurrency**: Broadcasts MUST be non-blocking. If one client is slow to read, it must not block the entire broadcast loop.
- **Protocol**: Uses Protobuf `ConfigMsg` with type `PROPAGATE_MEM_CONFIG`.
