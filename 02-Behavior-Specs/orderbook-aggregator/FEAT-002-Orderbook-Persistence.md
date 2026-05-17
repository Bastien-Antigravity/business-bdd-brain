---

microservice: business-bdd-brain
type: spec
status: draft
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---
# 📝 Behavior Spec: FEAT-002 Orderbook Persistence

**Status**: Draft
**Service**: `data-ingestor`, `timescaledb`
**Priority**: High

## 📋 Context
To enable backtesting and historical analysis, the unified orderbook data must be persisted in a high-performance time-series database. We utilize TimescaleDB's hypertable architecture to handle the high volume of depth updates while preserving broker-level metadata.

## 🎭 Scenarios

### Scenario 1: Hypertable Schema Enforcement
**Given** the TimescaleDB instance is healthy
**When** the "Orderbook-Persistence" module initializes
**Then** it should ensure the schema `orderbook_aggregator` exists
**And** it should ensure the `orderbook_history` table exists within that schema
**And** it should be configured as a `hypertable` partitioned by `time` (1 day intervals) and `symbol`.

### Scenario 2: Persistent Unified Storage
**Given** a `UnifiedOrderBook` event is received from the NATS bus
**When** the `data-ingestor` processes the event for storage
**Then** it should insert one row per price level into the `orderbook_aggregator.orderbook_history` table
**And** it should store the broker-specific volume breakdown in a `jsonb` column named `broker_data`
**And** it should include both `exchange_ts` and `ingestion_ts`.

### Scenario 3: Batch Write Efficiency
**Given** a high-frequency stream of orderbook updates
**When** the `data-ingestor` reaches a buffer size of 500 rows or a 1-second timeout
**Then** it should execute a batch "COPY" or multi-value "INSERT" to TimescaleDB
**And** it should log the batch latency to the `universal-logger`.

### Scenario 4: Data Retention & Compression
**Given** orderbook data older than 7 days
**When** the TimescaleDB background policy runs
**Then** it should compress the old chunks to save disk space
**And** it should ensure queries on compressed data remain functional.

## 🧪 Acceptance Criteria
- [ ] Table `orderbook_history` is a valid TimescaleDB hypertable.
- [ ] Broker metadata is searchable via JSONB operators.
- [ ] Batch write latency is < 50ms per batch.
- [ ] No data loss during high-volatility bursts (1000+ rows/sec).
- [ ] Compression ratio of at least 10:1 achieved for historical data.
