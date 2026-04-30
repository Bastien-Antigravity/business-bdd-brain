---
title: "Domain Glossary"
type: architecture
status: active
microservice: ecosystem-wide
tags:
  - domain/ddd
  - type/reference
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

## Services

| Term | Definition |
|------|-----------|
| **Log Server** | The centralized log aggregation service that receives logs over SafeSocket TCP. |
| **Config Server** | The gRPC-based configuration distribution server. |
| **Notif Server** | The notification relay service. |
| **Sandbox** | The `sandbox-testing` repository where behavior specs are executed as automated tests. |

## BDD Terminology

| Term | Definition |
|------|-----------|
| **Behavior Spec** | A markdown document defining expected system behavior using Given/When/Then syntax. |
| **Scenario** | A single test case within a behavior spec, describing one specific interaction flow. |
| **Acceptance Criteria** | High-level requirements that a feature must satisfy to be considered complete. |
| **Edge Case** | A boundary or failure condition that must be explicitly tested. |
