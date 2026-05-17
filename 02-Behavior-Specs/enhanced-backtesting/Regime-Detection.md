---

type: spec
microservice: business-bdd-brain
repo: enhanced-backtesting
feature_id: FEAT-REGIME-DETECTION
status: approved
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/approved'
- '#type/spec'
---
# Feature: Market Regime Detection

## 🎯 Business Intent
- **User Story**: As a Quantitative Trader, I want the backtesting engine to automatically detect market regimes (trending, ranging, high/low volatility) so that I can develop and validate regime-aware trading strategies.
- **Problem Solved**: Strategies often perform differently in different market conditions. Detecting these conditions allows for better risk management and strategy optimization.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Volatility Regime Detection
- **Given** a price series with a sudden spike in volatility
- **And** historical volatility was relatively stable
- **When** the regime detector processes the data
- **Then** it should classify the period as "high_volatility"
- **And** the regime confidence score should reflect the statistical significance of the volatility spike.

### Scenario 2: Trend Strength Identification
- **Given** a strong upward price movement
- **And** volume confirmation (rising OBV)
- **And** bullish alignment of multiple Exponential Moving Averages (EMAs)
- **When** the regime detector processes the data
- **Then** it should identify a "strong_bull_trend" composite regime
- **And** the trend pillar score should be high (> 0.8).

### Scenario 3: Parameter Scaling by Timeframe
- **Given** a specific trading style (e.g., "day_trading")
- **When** the regime detector is initialized for a "1m" timeframe versus a "1d" timeframe
- **Then** it should apply different lookback multipliers (e.g., 0.1 for 1m, 2.5 for 1d)
- **And** the resulting lookback periods should be appropriately scaled for the timeframe's granularity.

### Scenario 4: Regime Persistence Filtering
- **Given** a noisy market environment where indicators flip states frequently
- **And** a regime persistence window is set to 5 periods
- **When** the composite regime is calculated
- **Then** it should apply a mode-based filter to ignore fluctuations shorter than the persistence window
- **And** provide a more stable market state output for strategy execution.

## 🛠️ Technical Constraints (The "How" for AI)
- **Architecture**: Must use a modular "Pillar" approach (Price Action, Momentum, Volume, Structure).
- **Storage**: Parameters must be synced between local JSON configs and a PostgreSQL database (`backtest.regime_detector_parameters`).
- **Performance**: Must handle large DataFrames efficiently using vectorized NumPy/Pandas operations.
- **Scaling**: Must support dynamic parameter scaling based on `TIMEFRAME_MULTIPLIERS`.

## ✅ Acceptance Criteria (Definition of Done)
- [x] Multi-methodology detection logic implemented (Volatility, Stationarity, Trend).
- [x] Volume confirmation integrated using OBV/MFI.
- [x] Multi-timeframe trend alignment calculation functional.
- [x] Regime persistence filter implemented to reduce noise.
- [x] Parameter syncing with PostgreSQL implemented and verified.
- [x] Unit tests for parameter scaling and regime classification pass.

## 🔗 Sandbox Binding
- **Feature**: `[sandbox-testing/features/FEAT-REGIME-DETECTION.yaml]`
- **Implementation**: `enhanced-backtesting/src/calculators/regime_detector.py`
