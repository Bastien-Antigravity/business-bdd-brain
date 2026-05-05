--- 
type: spec
microservice: obsidian-brain

repo: safe-socket
feature_id: FEAT-003
status: draft
---

# Feature: Deadline & Heartbeat Management

## 🎯 Business Intent
- **User Story**: As a system administrator, I want connections to automatically detect peer failures via heartbeats and deadlines so that resources are not leaked by "zombie" connections.
- **Problem Solved**: Automates the "Activity-Refresh" pattern and provides failsafe heartbeats based on a safety ratio.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Activity-Refresh on Data Flow
- **Given** a socket configured with an idle timeout of 5 seconds
- **When** a successful `Read` or `Write` operation occurs
- **Then** the absolute deadline for the socket should be pushed forward to `Now + 5 seconds`
- **And** this refresh should happen transparently without application intervention

### Scenario 2: Heartbeat Scheduling (2.5x Ratio)
- **Given** a socket with a 10-second deadline
- **When** the connection becomes idle
- **Then** the background heartbeat task should fire at an interval of `10 / 2.5 = 4 seconds`
- **And** it should send a heartbeat message to the peer to keep the connection alive

### Scenario 3: Infinite Wait (Deadline = 0)
- **Given** a socket configured with a deadline of 0
- **When** the connection is opened
- **Then** all internal activity-refresh timers should be disabled
- **And** kernel-level deadlines should be cleared
- **And** the connection should remain open indefinitely during idle periods

### Scenario 4: Adaptive Heartbeat Threshold (Low Latency)
- **Given** a local TCP connection (127.0.0.1) with a deadline of 100ms
- **When** the connection starts
- **Then** the library should detect the deadline is below the 150ms threshold for local traffic
- **And** it should disable the heartbeat to minimize latency overhead
- **And** it should print a warning to stdout

## 🛠️ Technical Constraints
- **Safety Ratio**: MUST be 2.5x (Interval = Deadline / 2.5).
- **Thresholds**: 
    - Network: 300ms
    - Local: 150ms
    - SHM: 50ms
