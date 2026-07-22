---
microservice: 08-Base-Scripts
type: note
status: active
tags:
- '#service/08-Base-Scripts'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---
# Feature: Identity Handshake (Hello Protocol)

## 🎯 Business Intent
- **User Story**: As a network operator, I want every connection to explicitly identify its origin (host, name, IP) so that I can perform service discovery and security auditing.
- **Problem Solved**: Replaces anonymous TCP connections with "Self-Identifying" sessions using a standardized handshake.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Peer Identity Exchange (Handshake)
- **Given** a TCP client and server both configured with the `hello` protocol
- **When** the client connects
- **Then** the client must send a `HelloMsg` serialized with Cap'n Proto
- **And** the message must contain the client's `AppName`, `Hostname`, and `LocalAddress`
- **And** the server must block the `Accept()` call until the `HelloMsg` is successfully received and parsed

### Scenario 2: Stateless Identity (UDP Envelope)
- **Given** a UDP connection using the `hello` protocol
- **When** a payload is sent
- **Then** the library must wrap the payload in a `PacketEnvelope`
- **And** it must include the `SenderID` in every packet to satisfy the stateless requirement
- **And** the receiver must decapsulate the envelope and reconstruct a partial `HelloMsg` for the application

### Scenario 3: Handshake Timeout
- **Given** a client that opens a TCP connection but never sends a `HelloMsg`
- **When** the server's handshake deadline (default 500ms) is reached
- **Then** the server must abort the handshake
- **And** it must close the connection and return an error to prevent the worker from hanging

## 🛠️ Technical Constraints
- **Serialization**: MUST use **Cap'n Proto** for `HelloMsg` and `PacketEnvelope`.
- **Payload Extraction**: `GetIdentity()` helper must be able to peel through `HeartbeatConnection` or `HandshakeConnection` to retrieve the peer's metadata.
- **Fields**: Mandatory fields in `HelloMsg`: `FromName`, `FromHost`, `FromAddress`.
