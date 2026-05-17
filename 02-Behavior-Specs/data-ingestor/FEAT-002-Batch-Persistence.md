---
type: spec
microservice: business-bdd-brain
repo: data-ingestor
feature_id: FEAT-002
status: draft
tags:
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: Batch Persistence (TimescaleDB)

## 🎯 Business Intent
- **User Story**: As a database administrator, I want data to be written in batches rather than individually, so that the database IOPS are minimized and the system can handle higher throughput.
- **Problem Solved**: Reduces the "Commit Tax" on the database.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Flush on Batch Size Reached
- **Given** a batch limit of 1000 rows
- **When** the internal buffer reaches 1000 rows
- **Then** the ingestor must trigger a `COPY` or `INSERT` operation to TimescaleDB
- **And** it must clear the buffer after a successful write

### Scenario 2: Flush on Timeout (Linger)
- **Given** 100 rows in the buffer (below the limit)
- **And** a linger timeout of 1 second
- **When** the timeout expires
- **Then** the ingestor must flush the remaining rows to the database

### Scenario 3: Transactional Integrity
- **Given** a batch write operation
- **When** the database connection fails mid-write
- **Then** the ingestor must roll back the transaction
- **And** it must retry the entire batch after a delay

## 🛠️ Technical Constraints
- **Mechanism**: MUST use `COPY` command for high-speed ingestion if possible.
- **Concurrency**: Writing must happen on a separate background thread/goroutine.

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Batching logic verified with simulated load.
- [ ] Data verified in `hypertable` after flush.

## 🔗 Sandbox Binding
- **Feature**: `[sandbox-testing/features/FEAT-002-batch-persistence.yaml]`
