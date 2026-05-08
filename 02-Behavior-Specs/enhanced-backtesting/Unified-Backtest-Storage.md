---
type: spec
microservice: obsidian-brain
repo: enhanced-backtesting
feature_id: FEAT-UNIFIED-STORAGE
status: approved
tags:
- '#state/approved'
- null
- '#type/spec'
---

# Feature: Unified Backtest Storage

## 🎯 Business Intent
- **User Story**: As a Quantitative Researcher, I want to store my backtest results, trades, and metrics in a persistent database so that I can compare different strategies and parameters over time.
- **Problem Solved**: Backtest results are often ephemeral. Storing them in a structured way (Postgres for metadata, ArcticDB/Pickle for high-frequency data) allows for long-term analysis and auditing.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Multi-Backend Storage Persistence
- **Given** a successful backtest execution for ticker "AAPL"
- **And** the storage engine is configured with PostgreSQL and ArcticDB/Pickle
- **When** the `store_backtest` method is invoked
- **Then** the summary metrics (Sharpe, ROI, Drawdown) must be saved to the `backtest.results` PostgreSQL table
- **And** the full trade history and equity curve must be saved to the time-series backend (ArcticDB or Pickle).

### Scenario 2: Result Filtering via Storage Thresholds
- **Given** a backtest result with a Sharpe Ratio of 0.2
- **And** the `MIN_SHARPE_RATIO` threshold is set to 0.5
- **When** the storage engine evaluates the result for persistence
- **Then** it should log a warning and skip the storage process
- **And** no new records should be created in the database.

### Scenario 3: Cross-Platform Compatibility (macOS Fallback)
- **Given** the framework is running on a Darwin (macOS) operating system
- **When** the `BacktestStorage` is initialized
- **Then** it should automatically select `PickleStorage` as the file-based backend
- **And** it should bypass ArcticDB initialization to prevent known LMDB stability issues on macOS.

### Scenario 4: Automated Schema Enforcement
- **Given** a new backtest result missing the mandatory `strategy_name` field
- **When** the storage engine attempts to save the result to PostgreSQL
- **Then** it should trigger a validation error
- **And** ensure that the database integrity is maintained by rejecting the malformed record.

## 🛠️ Technical Constraints (The "How" for AI)
- **Architecture**: Use Mixin-based inheritance from `ArcticDBStorage`, `PostgresStorage`, and `PickleStorage`.
- **Concurrency**: Database connections must be handled safely, ensuring cursors are closed after operations.
- **Data Integrity**: Use `STORAGE_THRESHOLDS` constants to prevent database bloat from low-quality results.
- **Portability**: Detect OS via `sys.platform` to toggle between storage backends.

## ✅ Acceptance Criteria (Definition of Done)
- [x] Unified interface `BacktestStorage` implemented.
- [x] PostgreSQL table creation logic for backtest results verified.
- [x] OS-specific backend selection (Pickle vs ArcticDB) working correctly.
- [x] Storage threshold logic implemented and tested.
- [x] JSON schema validation for backtest parameters functional.

## 🔗 Sandbox Binding
- **Feature**: `[sandbox-testing/features/FEAT-UNIFIED-STORAGE.yaml]`
- **Implementation**: `enhanced-backtesting/src/backtest_storage.py`
