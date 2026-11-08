---
microservice: log-server
type: note
status: active
tags:
- '#service/log-server'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
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

### Scenario 4: Real-Time Server-Side Streaming (StreamLogs)
- **Given** an active gRPC client connection from `web-interface`
- **When** the client invokes `rpc StreamLogs(StreamLogsRequest)`
- **Then** the server must subscribe to the internal Tokio broadcast channel
- **And** stream each newly ingested log message over HTTP/2 with sub-millisecond latency
- **And** apply optional `service_name` and `min_level` filters if specified in the request.

### Scenario 5: Buffered Tail Query (GetTailLogs)
- **Given** recent log entries deposited in the in-memory circular ring buffer (`LogRingBuffer`)
- **When** a client invokes `rpc GetTailLogs(TailLogsRequest)` with limit $N$
- **Then** the server must return up to $N$ entries matching the requested filters
- **And** include the total ingested counter in the `TailLogsResponse`.

## 🛠️ Technical Constraints
- **Framework**: MUST use `Tonic` (Rust) for the gRPC server implementation.
- **Protocol**: MUST use standard Protobuf v3 definitions (`log_service.proto`).
- **Concurrency**: Handle gRPC requests asynchronously using Tokio tasks and broadcast channels.
- **Memory Safety**: In-memory ring buffer MUST be strictly bounded to 5,000 entries.
