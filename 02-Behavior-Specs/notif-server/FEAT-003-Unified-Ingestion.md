---

type: spec
microservice: business-bdd-brain
repo: notif-server
feature_id: FEAT-003
status: draft
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
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

## 🛠️ Technical Constraints
- **Channel**: Use a buffered Go channel (`NotifChan`) to decouple ingestion from delivery.
- **Schemas**: Must maintain both `.capnp` and `.proto` files in sync.
