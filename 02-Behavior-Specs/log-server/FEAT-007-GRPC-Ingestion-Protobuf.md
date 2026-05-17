---
type: spec
microservice: business-bdd-brain
repo: log-server
feature_id: FEAT-007
status: active
tags:
- \'#zone/3-fleet\'
- '#service/business-bdd-brain'
- '#state/active'
- '#type/spec'
---

# Feature: gRPC Ingestion & Protobuf Parity

## 🎯 Business Intent
- **User Story**: As a developer using modern languages (like Rust or Python), I want to send logs via gRPC so that I can benefit from automatic code generation and HTTP/2 performance.
- **Problem Solved**: Provides a standard ingestion path that co-exists with the high-performance binary TCP path.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Protobuf to Internal Model Mapping
- **Given** a `ProtoLogRequest` received via gRPC
- **When** the gRPC server processes the request
- **Then** it must perform a 1:1 mapping of all fields (timestamp, hostname, message, stack_trace, etc.) into the internal `LogEntry` struct
- **And** it must treat the gRPC client ID as the `service_name`.

### Scenario 2: Sequence Number Injection
- **Given** a log message arriving via gRPC
- **When** the message enters the `handle_grpc_message` handler
- **Then** the server must fetch and increment the global `AtomicU64` sequence counter
- **And** it must prepend the sequence number to the formatted message string before queuing it for the writer.

### Scenario 3: Response Confirmation
- **Given** a successful gRPC write
- **When** the message is accepted by the internal MPSC queue
- **Then** the gRPC service must return a `LogResponse` with `success: true`
- **And** it must complete the call in under 10ms.

## 🛠️ Technical Constraints
- **Framework**: MUST use `Tonic` (Rust) for the gRPC server implementation.
- **Protocol**: MUST use standard Protobuf v3 definitions.
- **Concurrency**: Handle gRPC requests asynchronously using Tokio tasks.
