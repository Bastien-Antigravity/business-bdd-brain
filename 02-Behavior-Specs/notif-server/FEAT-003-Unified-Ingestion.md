---
microservice: notif-server
type: note
status: active
tags:
- '#service/notif-server'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---
# Feature: Unified Ingestion (TCP & gRPC)

## 🎯 Business Intent
- **User Story**: As a polyglot developer, I want to send notifications from my C++ microservice (via TCP) and my Python service (via gRPC) and have them handled the same way by the server.
- **Problem Solved**: Provides a consistent entry point for all languages in the ecosystem.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Converging Paths
- **Given** an alert sent via TCP (Cap'n Proto)
- **And** the same alert sent via gRPC (Protobuf)
- **When** they reach the server
- **Then** both must be transformed into the internal `NotifMessage` format
- **And** both must go through the same routing and deduplication logic

### Scenario 2: Priority Handling
- **Given** a high-priority message arriving via gRPC
- **And** a low-priority message arriving via TCP
- **When** they enter the `NotifChan`
- **Then** (if implemented) the high-priority message should be moved to the front of the queue
- **And** it should be delivered first

### Scenario 3: Framing Parity
- **Given** a TCP client
- **When** sending a notification
- **Then** it must use the same 4-byte big-endian framing as the `log-server` and `config-server`
- **And** the server must validate the frame before processing

### Scenario 4: L2/L3 Microservice Toolbox Facade Dispatch
- **Given** an L2 or L3 microservice using `microservice-toolbox/go/pkg/notification`
- **When** calling `client.SendAlert(ctx, "TRADE", "Order filled", "trade_channel")`
- **Then** the client dynamically resolves `notif_server` capability address (gRPC Port 1027) via `AppConfig`
- **And** if `notif_server` capability is missing or unresolvable, initialization fails fast with an explicit error (zero silent fallback)
- **And** dispatches `NotifRequest` containing message, level, and tags
- **And** `notif-server` enqueues the request into `NotifChan` for tag/level routing

### Scenario 5: Polyglot CGO Bridge Dispatch (libnotif)
- **Given** a non-Go microservice (Python, Rust, C++, VBA) using `microservice-toolbox` notification client
- **When** calling `client.send_alert("ERROR", "Disk pressure", "infra")`
- **Then** the client invokes `Notif_Send` / `Notif_SendWithTimeout` exported by `libnotif` C ABI
- **And** the CGO runtime dispatches the request over gRPC to Port 1027
- **And** `notif-server` receives and enqueues the request without requiring per-language native gRPC stubs

## 🛠️ Technical Constraints
- **Channel**: Use a buffered Go channel (`NotifChan`) to decouple ingestion from delivery.
- **Schemas**: Must maintain both `.capnp` and `.proto` files in sync.
- **Client SDK**: `microservice-toolbox/go/pkg/notification` provides 1-line native Go API.
- **Polyglot Bridge**: `notif-server` compiles `cmd/libnotif/main.go` into `libnotif.{dylib,so,dll}`, consumed by Python (`ctypes`), Rust (`libloading`), C++ (`NotifBridgeLoader`), and VBA (`Declare PtrSafe`).

