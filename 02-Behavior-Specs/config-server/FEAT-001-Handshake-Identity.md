---
repo: config-server
microservice: config-server
type: behavior-spec
status: active
feature_id: FEAT-001
tags:
- \'#service/config-server\'
- '#type/behavior-spec'
- null
- '#state/active'
---
# FEAT-001: Mutual Identity Handshake

*Back-link: [[config-server/README|⬅️ config-server]]*
---

# Feature: Handshake & Identity Verification

## 🎯 Business Intent
- **User Story**: As a security officer, I want the config-server to verify the identity of every microservice that connects so that unauthorized processes cannot listen to our sensitive configuration updates.
- **Problem Solved**: Prevents "Shadow Services" from spoofing or snooping on the configuration fleet.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Successful Connection Handshake
- **Given** a microservice "order-service" in group "trading"
- **When** it opens a TCP connection to the Config Server
- **Then** the server must immediately expect an `IDENTITY` frame
- **And** the frame must contain the service name and group
- **And** the server must log the connection of "order-service"
- **And** it must add the socket to the `Active Listeners` broadcast pool

### Scenario 2: Rejected Identity (Invalid Format)
- **Given** a connection attempt
- **When** the client sends a malformed identity frame or skips the handshake
- **Then** the Config Server must immediately close the TCP socket
- **And** it must NOT allow any further requests from that connection
- **And** it must log a "Handshake Failed" security warning

### Scenario 3: Identity Conflict (Duplicate Name)
- **Given** a service "engine-1" is already connected
- **When** another service with the same name "engine-1" attempts to connect
- **Then** the server should (choose one: allow multiple or reject duplicate - standard is to allow but log unique IDs)
- **And** it must ensure both instances receive independent broadcast updates

## 🛠️ Technical Constraints
- **Protocol**: MUST use the `SafeSocket` framing (4-byte length prefix).
- **Timing**: The identity frame MUST be the first message after the TCP connect.
