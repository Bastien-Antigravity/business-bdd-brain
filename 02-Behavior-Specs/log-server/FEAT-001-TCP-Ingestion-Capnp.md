---
type: spec
microservice: business-bdd-brain
repo: log-server
feature_id: FEAT-001
status: active
tags:
- \'#zone/3-fleet\'
- '#service/business-bdd-brain'
- '#state/active'
- '#type/spec'
---

# Feature: TCP Ingestion (Cap'n Proto)

## 🎯 Business Intent
- **User Story**: As a high-performance service, I want to send logs over a persistent TCP connection using a binary protocol so that I can minimize latency and serialization overhead.
- **Problem Solved**: Eliminates the overhead of text-based protocols and ensures message integrity via strict binary framing.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Packed Frame Decoding
- **Given** an established TCP connection
- **When** the server receives a data frame
- **Then** it must decode the binary payload using the `serialize_packed::read_message` method
- **And** it must map the data to the internal `LogEntry` model using the `logger.capnp` schema.

### Scenario 2: Framing Constraints (SafeSocket)
- **Given** a raw TCP stream
- **When** the server reads from the stream
- **Then** it must use `SafeSocket` to enforce a 4-byte big-endian length prefix
- **And** it must reject any frame larger than `MAX_FRAME_SIZE` (64KB).

### Scenario 3: Deserialization Failure
- **Given** a valid frame with invalid Cap'n Proto content
- **When** deserialization fails
- **Then** the server must log a "Deserialization Failed" warning
- **And** it must NOT crash the connection, continuing to wait for the next frame.

## 🛠️ Technical Constraints
- **Encoding**: MUST use **Packed** Cap'n Proto for log messages (unlike the handshake).
- **Framing**: 4-byte Big-Endian length prefix.
- **Library**: `capnp` and `tokio`.
