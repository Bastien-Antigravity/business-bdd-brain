---
repo: log-server
feature_id: FEAT-001
status: draft
---

# Feature: TCP Ingestion (Cap'n Proto)

## 🎯 Business Intent
- **User Story**: As a system administrator, I want the log server to receive logs via a high-performance binary protocol so that we can handle thousands of messages per second with minimal CPU and memory usage.
- **Problem Solved**: Eliminates the overhead of text-based protocols (like JSON) and ensures message integrity via strict framing.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Successful Frame Decoding
- **Given** a TCP client sending a 4-byte big-endian length prefix `L`
- **And** a payload of exactly `L` bytes containing a Cap'n Proto `LogEntry`
- **When** the server receives the frame
- **Then** it must correctly decode the binary payload using the `logger.capnp` schema
- **And** it must convert the data into the internal `LogEntry` model
- **And** it must assign a global sequence number to the entry

### Scenario 2: Oversized Frame Rejection
- **Given** a client attempting to send a frame header claiming 100MB of data
- **When** the server reads the header
- **Then** it must immediately reject the frame if it exceeds the `MAX_FRAME_SIZE` (default 64KB)
- **And** it must close the connection to protect server memory

### Scenario 3: Partial Message Handling
- **Given** a client that sends a valid length header but disconnects before sending the full payload
- **When** the server's read timeout expires
- **Then** it must discard the partial data
- **And** it must log a "Protocol Violation" warning

## 🛠️ Technical Constraints
- **Protocol**: MUST use Big-Endian 4-byte length prefix.
- **Library**: MUST use the `capnp` Rust crate.
- **Buffer**: Use a pre-allocated buffer for reading to minimize heap allocations.
