---
title: Domain Glossary
type: architecture
status: active
microservice: ecosystem-wide
tags:
- '#zone/3-fleet'
- '#service/ecosystem-wide'
- '#type/reference'
- '#domain/ddd'
- '#state/active'
- '#type/architecture'
---
# 📘 Domain Glossary (Ubiquitous Language)

This glossary defines the shared terminology used across all Behavior-Driven Development specifications. Using consistent language ensures that specs, tests, and code all refer to the same concepts.

---

## Core Infrastructure

| Term | Definition |
|------|-----------|
| **SafeSocket** | The custom TCP transport layer using length-prefixed framing for reliable message delivery. |
| **Heartbeat** | A periodic ping/pong mechanism to detect zombie connections in SafeSocket. |
| **Flexible Logger** | The client-side logging facade that abstracts concrete logging implementations. |
| **Universal Logger** | The ecosystem-wide logging interface standard that all services implement. |
| **Distributed Config** | The configuration library providing environment-first bootstrapping with file resolution. |
| **FFI (Foreign Function Interface)** | The mechanism used to call Go or Rust logic from other languages (VBA, C++, Python). |
| **CGO** | The Go toolchain extension for calling C code, used in the FFI bridge for distributed config. |
| **Cap'n Proto** | A zero-copy serialization protocol used for high-performance logging ingestion. |
| **Gherkin** | The business-readable language (Given/When/Then) used to define BDD scenarios. |
| **NATS** | The high-performance message bus used for fleet-wide event distribution and inter-service communication. |
| **Atomic Sequencing** | A thread-safe mechanism using atomic counters to ensure strict message ordering across concurrent processes. |
| **JSONB** | The binary-optimized JSON format used in PostgreSQL for storing semi-structured broker metadata and event payloads. |
| **FFI Bridge** | A cross-language interface that allows high-level languages (Python/VBA) to call low-level Go or Rust performance tiers. |
| **Bidirectional Routing** | A communication pattern where commands can be sent to a service, and the service can return asynchronous acknowledgments or data updates. |
| **Docker Guard** | A safety mechanism within the toolbox that detects containerized environments and automatically adjusts network binding (e.g., 127.0.0.1 to Container IP). |
| **Microservice Toolbox** | The shared internal library containing common bootstrapping, networking, and resilience patterns used by all services. |
| **Handshake Identity** | The initial authentication and registration phase where a client identifies its service name and capability tier to a server. |

## Services

| Term | Definition |
|------|-----------|
| **Log Server** | The centralized log aggregation service that receives logs over SafeSocket TCP. |
| **Config Server** | The gRPC-based configuration distribution server. |
| **Notif Server** | The notification relay service. |
| **Sandbox** | The `sandbox-testing` repository where behavior specs are executed as automated tests. |
| **TimescaleDB** | A time-series database extension for PostgreSQL used for high-performance persistence of market and orderbook data. |
| **Hypertable** | The primary abstraction in TimescaleDB that automatically partitions data into manageable "chunks" based on time. |
| **Tele-Remote** | The Telegram-based gateway for remote management and tele-command execution across the fleet. |

## BDD Terminology

| Term | Definition |
|------|-----------|
| **Behavior Spec** | A markdown document defining expected system behavior using Given/When/Then syntax. |
| **Scenario** | A single test case within a behavior spec, describing one specific interaction flow. |
| **Acceptance Criteria** | High-level requirements that a feature must satisfy to be considered complete. |
| **Edge Case** | A boundary or failure condition that must be explicitly tested. |
| **Sentinel Audit** | A compliance check performed by the AI to ensure repository integrity. |
| **Backpressure** | A flow-control mechanism that prevents a producer from overwhelming a consumer when buffers are full. |
| **Ecosystem Bootstrapping** | The standardized startup sequence for services, following the Loading Law: Bridge (Config Server) -> Local YAML -> CLI Overrides. |

## Trading Domains

| Term | Definition |
|------|-----------|
| **Orderbook Normalization** | The process of converting exchange-specific orderbook formats into a unified internal schema. |
| **Regime Detection** | Algorithmic identification of current market states (e.g., trending, range-bound, high-volatility). |
| **Backtest Engine** | The simulation environment for validating trading strategies against historical data. |
| **UnifiedOrderBook** | The standardized internal schema for market depth data, merging volume from multiple brokers and exchanges. |
