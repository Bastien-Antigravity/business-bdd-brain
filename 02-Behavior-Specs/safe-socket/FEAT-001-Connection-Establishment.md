---
type: spec
microservice: obsidian-brain
repo: safe-socket
feature_id: FEAT-001
status: draft
tags:
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Connection Establishment

## 🎯 Business Intent
- **User Story**: As a microservice developer, I want to establish a reliable connection to a peer using a simple string-based profile so that I don't have to manually configure low-level socket parameters.
- **Problem Solved**: Standardizes how services connect, ensuring identities are exchanged and transports are correctly selected based on environmental needs.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Successful TCP Client Connection with Identity
- **Given** a target address "127.0.0.1:8080" and a profile string "tcp-hello:my-service"
- **When** I call `factory.Create` with these parameters
- **Then** the factory should parse the profile as "tcp-hello" and the identity as "my-service"
- **And** it should return a TCP-backed Socket initialized with the Hello protocol
- **And** the connection should perform a handshake exchanging "my-service" as the local identity

### Scenario 2: SHM Transport Selection
- **Given** a target address "/tmp/my-socket.shm" and a profile string "shm-hello:my-service"
- **When** I call `factory.Create`
- **Then** the library should instantiate a Shared Memory transport
- **And** it should use a ring buffer with spin-wait synchronization

### Scenario 3: Missing Identity Fallback
- **Given** a profile string "tcp-hello" (no colon)
- **When** I call `factory.Create`
- **Then** the library should split the profile and find an empty identity
- **And** it should fall back to a generic identity like "TcpClient-Generic"
- **And** it should emit a warning to the logs about missing identity

## 🛠️ Technical Constraints
- **Architecture**: Use the Factory pattern to decouple transport selection from the API.
- **Syntax**: Profiles MUST follow the `profile:identity` convention.
- **Protocols**: Supported protocols include `hello` and `none`.
