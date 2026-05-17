---
type: spec
microservice: business-bdd-brain
repo: safe-socket
feature_id: FEAT-008
status: active
tags:
- \'#zone/3-fleet\'
- '#service/business-bdd-brain'
- '#state/active'
- '#type/spec'
---

# Feature: Resource Boundaries & Hardening

## 🎯 Business Intent
- **User Story**: As a system administrator, I want to limit the memory and connection resources used by the library so that a single malicious or misconfigured peer cannot crash my entire server.
- **Problem Solved**: Prevents OOM (Out of Memory) attacks and File Descriptor exhaustion.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Maximum Message Size (OOM Protection)
- **Given** a `FramedTCP` connection with a configured `MaxPayloadSize` of 64MB
- **When** a peer sends a 4-byte header indicating a 1GB payload
- **Then** the library must immediately reject the message without allocating the 1GB buffer
- **And** it must close the connection and log a protocol violation error

### Scenario 2: Maximum Concurrent Connections (Server)
- **Given** a `SocketServer` with a `MaxConnections` limit of 1000
- **When** the 1001st client attempts to connect
- **Then** the server should refuse the connection or immediately close it
- **And** it should continue serving the existing 1000 clients without degradation

### Scenario 3: Buffer Pool Usage
- **Given** a high-frequency connection
- **When** multiple messages are sent/received
- **Then** the library should use a `sync.Pool` for byte buffers (if applicable) to reduce Garbage Collector pressure and memory fragmentation

## 🛠️ Technical Constraints
- **Defaults**: `MaxPayloadSize` should default to **64MB** unless overridden.
- **Protocol**: If a boundary is crossed, the connection MUST be closed (Fail-Fast) as it is considered "unreliable" or "compromised."
