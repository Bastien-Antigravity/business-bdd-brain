---
type: spec
microservice: obsidian-brain
repo: log-server
feature_id: FEAT-006
status: active
tags:
- \'#service/obsidian-brain\'
- '#state/active'
- null
- '#type/spec'
---

# Feature: Handshake Identity (Layout Hack)

## 🎯 Business Intent
- **User Story**: As a server administrator, I want to see the name and hostname of the connecting service immediately upon connection, even before any logs are sent, so that I can monitor the live fleet status.
- **Problem Solved**: Allows early identification of clients using a specialized "Hello" handshake that minimizes protocol complexity.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Hello Handshake Processing
- **Given** a new TCP connection from a client
- **When** the client sends a "Hello" frame (Unpacked Cap'n Proto)
- **Then** the server must decode the message using the `serialize::read_message` method
- **And** it must resolve the client identity as `fromName@fromHost`

### Scenario 2: The Layout Hack (Structural Abuse)
- **Given** the `HelloMsg` and `LoggerMsg` have identical field types at the same indices (@0 and @1)
- **When** the server identifies the client
- **Then** it must be able to cast the `HelloMsg` into a `LoggerMsg` reader to extract identity fields without maintaining two separate reader logic paths
- **And** this must reliably extract the name and host strings.

### Scenario 3: Handshake Rejection
- **Given** a client that sends a malformed handshake or skips the handshake
- **When** the server fails to resolve the identity
- **Then** it must log an error and close the connection to prevent anonymous/unauthorized access.

## 🛠️ Technical Constraints
- **Encoding**: Handshake MUST be **Unpacked** Cap'n Proto.
- **Indices**: `@0` (timestamp/name) and `@1` (hostname/host) must align between schemas.
- **Identity Format**: `[name]@[host]`.
