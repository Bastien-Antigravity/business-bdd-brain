---
microservice: orderbook-aggregator
type: spec
status: frozen
tags:
- '#service/orderbook-aggregator'
- '#state/frozen'
- '#tier/behavior'
- '#type/spec'
- '#zone/1-frozen'
---
# 📝 Behavior Spec: FEAT-001 Orderbook Ingestion

**Status**: Draft
**Service**: `market-observer`, `data-ingestor`
**Priority**: High

## 📋 Context
To perform technical analysis, we need high-fidelity orderbook data. This feature focuses on establishing a stable stream from Binance (WebSocket) into our system.

## 🎭 Scenarios

### Scenario 1: Successful Connection to Binance WebSocket
**Given** the `market-observer` service is healthy
**And** the Binance WebSocket URL is correctly configured
**When** the service starts the "Binance-Ingestor" module
**Then** it should establish a secure WebSocket connection
**And** it should subscribe to the `btcusdt@depth20@100ms` stream
**And** it should log a "CONNECTION_SUCCESS" event to the `universal-logger`.

### Scenario 2: Data Normalization
**Given** raw JSON depth events are being received from Binance
**When** the `data-ingestor` receives a raw event
**Then** it should map the `b` (bids) and `a` (asks) arrays into the internal `OrderbookEvent` model
**And** it should include the exchange timestamp (`E`) and local ingestion timestamp
**And** it should discard any events with empty ask or bid arrays.

### Scenario 3: Connection Resiliency (SafeSocket)
**Given** an active connection to Binance
**When** the connection is lost (Network error)
**Then** the `market-observer` should attempt to reconnect using an exponential backoff
**And** it should notify the `notif-server` if reconnection fails after 5 attempts.

### Scenario 4: Silent Data Detection (Heartbeat)
**Given** an active connection to Binance
**When** no data packets are received for 30 seconds (stale socket)
**Then** the `market-observer` should terminate the stale connection
**And** it should initiate a fresh reconnection sequence.

### Scenario 5: Multi-Broker Aggregation
**Given** active streams from multiple brokers (e.g., Binance, Kraken, OKX)
**When** the `orderbook-aggregator` receives depth updates for the same symbol (e.g., BTC/USDT)
**Then** it should merge the bids and asks into a single "Unified Orderbook"
**And** it should tag each price level with its source broker ID
**And** it should sort the unified book by price (Bids descending, Asks ascending).

## 🧪 Acceptance Criteria
- [ ] Raw Binance JSON is parsed without data loss.
- [ ] Normalization latency is < 5ms.
- [ ] Reconnection logic triggers automatically.
- [ ] Heartbeat timeout (30s) triggers fresh connection.
- [ ] Multi-broker updates are correctly merged and sorted in the unified book.
- [ ] Data is stored in TimescaleDB with a valid `hypertable` schema.
