---
type: spec
microservice: business-bdd-brain
repo: safe-socket
feature_id: FEAT-002
status: draft
tags:
- \'#zone/3-fleet\'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: 4-Byte Header Framing

## 🎯 Business Intent
- **User Story**: As a network architect, I want TCP streams to be framed with message boundaries so that the receiver doesn't have to deal with partial or merged packets.
- **Problem Solved**: Solves the "TCP Stream Fragmentation" problem by prepending every message with its length.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Sending a Framed Message
- **Given** an established TCP connection using `FramedTCP` transport
- **When** I send a payload of 1024 bytes
- **Then** the transport should calculate the length (1024)
- **And** it should write a 4-byte Big-Endian header followed by the payload
- **And** the total bytes written to the raw wire should be 1028 bytes

### Scenario 2: Receiving a Framed Message
- **Given** an incoming stream of bytes on a `FramedTCP` socket
- **When** the receiver reads the first 4 bytes
- **Then** it should parse the 4-byte header as the message length `N`
- **And** it should perform a `ReadFull` operation to capture exactly `N` bytes
- **And** it should return the payload to the application only after the full `N` bytes are received

### Scenario 3: Malformed Header (Protocol Violation)
- **Given** a corrupted stream where the 4-byte header indicates a length larger than the maximum buffer size
- **When** the receiver attempts to parse the header
- **Then** it should detect the size violation
- **And** it should close the connection and return an error to prevent memory exhaustion

## 🛠️ Technical Constraints
- **Endianness**: MUST use Big-Endian (Network Byte Order) for the header.
- **Header Size**: Fixed at 4 bytes (Uint32).
- **Max Payload**: Should be bounded by a configurable limit (default e.g., 64MB) to prevent OOM attacks.
