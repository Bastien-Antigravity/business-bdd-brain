---
microservice: behavior-specs-moc
type: moc
status: active
tags:
- '#zone/3-fleet'
- '#service/behavior-specs-moc'
- '#state/active'
- '#type/moc'
---
# Behavior Specs MOC

This index groups behavioral specifications by their respective microservices.

## [[Config-Server-Hub|config-server]]
- [[FEAT-001-Handshake-Identity]]
- [[FEAT-002-Atomic-State-Swap]]
- [[FEAT-003-Broadcast-Propagation]]
- [[FEAT-004-Persistence-Safety]]

## [[Data-Ingestor-Hub|data-ingestor]]
- [[FEAT-001-Data-Transformation]]
- [[FEAT-002-Batch-Persistence]]
- [[FEAT-003-Orderbook-Ingestion-E2E]]

## [[Distributed-Config-Hub|distributed-config]]
- [[FEAT-001-Configuration-Discovery]]
- [[FEAT-002-Environment-Expansion]]
- [[FEAT-003-Multi-Profile-Strategies]]
- [[FEAT-004-Live-Configuration-Sync]]
- [[FEAT-005-Configuration-Precedence]]
- [[FEAT-006-Secret-Decryption]]
- [[FEAT-007-CGO-FFI-Bridge]]
- [[FEAT-008-Resilience-Backoff]]
- [[FEAT-009-Mandatory-Service-Validation]]
- [[FEAT-010-Shared-Config-Injection]]
- [[FEAT-011-Memory-Space-Unification]]
- [[FEAT-012-Handle-Safety]]

## [[Enhanced-Backtesting-Hub|enhanced-backtesting]]
- [[Backtest-Engine]]
- [[Regime-Detection]]
- [[Unified-Backtest-Storage]]

## [[Flexible-Logger-Hub|flexible-logger]]
- [[FEAT-001-Log-Levels-Filtering]]
- [[FEAT-002-Sink-Strategies]]
- [[FEAT-003-Metadata-Enrichment]]
- [[FEAT-004-Object-Pooling]]
- [[FEAT-005-Independent-Notification]]
- [[FEAT-006-Network-Serialization]]

## [[Fundamental-Analysis-Hub|fundamental-analysis]]
- [[FEAT-001-Financial-Scraping]]
- [[FEAT-002-Valuation-Metrics]]

## [[Log-Server-Hub|log-server]]
- [[FEAT-001-TCP-Ingestion-Capnp]]
- [[FEAT-002-Atomic-Ordering]]
- [[FEAT-003-Dynamic-Batching]]
- [[FEAT-004-Rotation-Strategy]]
- [[FEAT-005-Heartbeat-Liveness]]
- [[FEAT-006-Handshake-Identity]]
- [[FEAT-007-GRPC-Ingestion-Protobuf]]
- [[FEAT-008-Terminal-UI-Enhancements]]
- [[FEAT-009-Ecosystem-Bootstrapping]]

## [[Market-Observer-Hub|market-observer]]
- [[FEAT-001-Websocket-Reconnection]]
- [[FEAT-002-Orderbook-Normalization]]
- [[FEAT-003-Heartbeat-Monitoring]]

## [[Microservice-Toolbox-Hub|microservice-toolbox]]
- [[FEAT-001-Docker-Guard]]
- [[FEAT-002-Polyglot-Parity]]
- [[FEAT-003-CLI-Priority]]
- [[FEAT-004-Connection-Resilience]]
- [[FEAT-005-In-Memory-Mirroring]]

## [[Notif-Server-Hub|notif-server]]
- [[FEAT-001-Tag-Based-Routing]]
- [[FEAT-002-Sender-Integrations]]
- [[FEAT-003-Unified-Ingestion]]

## [[Ontime-Scheduler-Hub|ontime-scheduler]]
- [[FEAT-001-Go-Migration]]
- [[FEAT-008-Scheduler-Migration]]
- [[LEGACY-Python-Behavior]]

## [[Orderbook-Aggregator-Hub|orderbook-aggregator]]
- [[FEAT-001-Orderbook-Ingestion]]
- [[FEAT-002-Orderbook-Persistence]]

## [[Safe-Socket-Hub|safe-socket]]
- [[FEAT-000-Connection-Lifecycle]]
- [[FEAT-001-Connection-Establishment]]
- [[FEAT-002-Framing-Protocol]]
- [[FEAT-003-Deadline-Management]]
- [[FEAT-004-Identity-Handshake]]
- [[FEAT-005-Shared-Memory-Transport]]
- [[FEAT-006-Graceful-Shutdown]]
- [[FEAT-007-Reconnection-Strategy]]
- [[FEAT-008-Resource-Boundaries]]

## [[Sandbox-Testing-Hub|sandbox-testing]]
- [[FEAT-000-System-Bootstrap]]

## [[Tele-Remote-Hub|tele-remote]]
- [[FEAT-001-Dynamic-Menu-Building]]
- [[FEAT-002-Component-Registration]]
- [[FEAT-003-Bidirectional-Command-Routing]]

## [[Universal-Logger-Hub|universal-logger]]
- [[FEAT-001-Unified-Bootstrap]]
- [[FEAT-002-C-String-Sanitization]]
- [[FEAT-003-VBA-Thread-Bridge]]
- [[FEAT-004-FFI-Handle-Lifecycle]]
- [[FEAT-005-Async-Language-Integration]]
- [[FEAT-006-Config-Injection]]
- [[FEAT-009-Excel-VBA-Integration]]
- [[FEAT-010-Cross-Platform-Compilation]]
- [[FEAT-011-Memory-Buffer-Management]]
- [[FEAT-012-VBA-Message-Loop-Hook]]

## [[Web-Interface-Hub|web-interface]]
- [[FEAT-007-UI-Bootstrap]]
