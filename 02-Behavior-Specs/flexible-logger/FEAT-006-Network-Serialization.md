---
type: spec
microservice: obsidian-brain
repo: flexible-logger
feature_id: FEAT-006
status: draft
tags:
- \'#service/obsidian-brain\'
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Network Serialization (Cap'n Proto)

## 🎯 Business Intent
- **User Story**: As a system performance engineer, I want the logs sent over the network to be as small and as fast to parse as possible so that I don't waste bandwidth or CPU on the Log Server.
- **Problem Solved**: Reduces network overhead compared to JSON and eliminates the cost of field mapping.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Binary Packing
- **Given** a `LogEntry` object
- **When** the `WriterSink` (Network) processes it
- **Then** it must use the **Cap'n Proto** schema (`logger.capnp`) to serialize the data
- **And** the resulting byte stream must be zero-copy where possible

### Scenario 2: Heartbeat Frame Integrity
- **Given** a silent network connection
- **When** a heartbeat is required
- **Then** the sink should send a 0-length frame (as per SafeSocket protocol)
- **And** it must NOT attempt to serialize a full log entry for a heartbeat

### Scenario 3: Large Message Handling
- **Given** a log message larger than 64KB
- **When** serialized via Cap'n Proto
- **Then** the library must correctly handle the multi-segment allocation required by the protocol
- **And** it must ensure the framing header reflects the true total size

## 🛠️ Technical Constraints
- **Library**: MUST use the `capnproto.org/go/capnp/v3` library.
- **Schema**: The schema MUST match the one used by the `log-server`.
