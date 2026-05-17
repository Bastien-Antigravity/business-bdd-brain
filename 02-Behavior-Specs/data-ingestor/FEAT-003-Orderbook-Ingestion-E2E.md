---
repo: data-ingestor
feature_id: FEAT-003
status: draft
type: behavior-spec
tags:
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/behavior-spec'
microservice: business-bdd-brain
---

# Feature: End-to-End Orderbook Ingestion

## 🎯 Business Intent
- **User Story**: As a trading system, I want a reliable pipeline that captures real-time orderbook data from Binance, transforms it into a high-precision format, and stores it in a persistence layer, so that it can be used for both live strategy execution and historical backtesting.
- **Goal**: "One Orderbook Packet from Exchange to Storage."

## 🎬 Scenarios (Gherkin)

### Scenario 1: Initial Connection & Data Flow
- **Given** the `data-ingestor` is initialized with a `standalone` profile
- **And** the `Binance` broker is configured for symbol `BTCUSDT`
- **When** the service starts
- **Then** it must establish a WebSocket connection to Binance
- **And** it must successfully subscribe to the `@depth@100ms` stream
- **And** it must receive a raw JSON payload containing orderbook updates

### Scenario 2: High-Precision Transformation
- **Given** a raw Binance depth event is received
- **When** the ingestor maps the data to the `MMarketData` model
- **Then** it MUST use **Fixed-Point Decimal** (8 decimal places) for all price and quantity values
- **And** it must normalize the timestamp to UTC nanoseconds
- **And** it must preserve the event sequence number (`u`) for gap detection

### Scenario 3: Successful Persistence Hand-off
- **Given** a transformed `MMarketData` packet
- **When** the ingestor hands it off to the publisher
- **Then** it must publish the packet to the NATS subject `marketdata.enriched.binance.btcusdt`
- **And** it must log a success message via `universal-logger`
- **And** it must increment the "Packets Ingested" metric

### Scenario 4: Error Handling & Resilience
- **Given** an invalid or malformed JSON packet from the exchange
- **When** the parser attempts to process it
- **Then** it must discard the packet
- **And** it must log a "Critical Parse Error" to the `log-server`
- **And** it must NOT crash the ingestion loop

## 🛠️ Technical Constraints
- **Precision**: Use `shopspring/decimal` for Go or scaled `int64` to avoid `float64` drift.
- **Latency**: End-to-end latency (Ingest -> Transform -> Publish) must be < 5ms.
- **Observability**: Every packet processed must be traceable via a unique `SessionID`.

## ✅ Acceptance Criteria (Definition of Done)
- [ ] BDD Scenarios pass in `sandbox-testing`.
- [ ] Log output verified in `log-server`.
- [ ] Data integrity check: published values match raw exchange values exactly.

## 🔗 Sandbox Binding
- **Feature**: `[sandbox-testing/features/FEAT-003-ingestion-e2e.yaml]`
