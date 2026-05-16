---
type: spec
microservice: obsidian-brain
repo: safe-socket
feature_id: FEAT-007
status: active
tags:
- \'#service/obsidian-brain\'
- '#state/active'
- null
- '#type/spec'
---

# Feature: Automatic Reconnection

## 🎯 Business Intent
- **User Story**: As a microservice developer, I want my client connections to automatically reconnect if the server restarts so that I don't have to write custom "retry" loops in my business logic.
- **Problem Solved**: Centralizes connection resilience logic and reduces boilerplate code in parent applications.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Transient Network Failure
- **Given** an established TCP client connection
- **When** the network cable is unplugged or the server crashes (EOF detected)
- **Then** the client must transition to a `reconnecting` state
- **And** it must start a reconnection loop using the same Profile and Identity as the original connection

### Scenario 2: Exponential Backoff (Client)
- **Given** a client in `reconnecting` state
- **When** the server is still down after multiple attempts
- **Then** the library should increase the delay between attempts (e.g. 1s, 2s, 4s, 8s)
- **And** it should eventually cap the delay at a maximum (e.g. 1 minute)

### Scenario 3: Successful Re-handshake
- **Given** a server that has just come back online
- **When** the client reconnects
- **Then** it MUST perform the full `hello` protocol handshake again to re-verify identity
- **And** once verified, it must notify the application that the socket is `Ready` again

## 🛠️ Technical Constraints
- **Backoff**: MUST use Jittered Exponential Backoff.
- **Status Visibility**: The application should be able to query the current connection state (`Connected`, `Reconnecting`, `Closed`).
