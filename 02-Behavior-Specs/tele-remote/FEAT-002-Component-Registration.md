---
type: spec
microservice: business-bdd-brain
repo: tele-remote
feature_id: FEAT-002
status: draft
tags:
- \'#zone/3-fleet\'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: Component Registration Handshake

## 🎯 Business Intent
- **User Story**: As a developer, I want to add a new service to my ecosystem and have it automatically appear in my Telegram remote without rebooting the server.
- **Problem Solved**: Enables "Plug-and-Play" infrastructure.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Successful Registration
- **Given** a new component "OrderBookAggregator"
- **When** it connects to Tele-Remote via NATS or gRPC
- **Then** it must send a `REGISTRATION` payload containing its UUID and Menu JSON
- **And** Tele-Remote must validate the UUID
- **And** it must add the component to the "Active Components" list
- **And** it must broadcast a "New Component Connected" notification to authorized admins

### Scenario 2: Connection Heartbeat
- **Given** a registered component
- **When** the underlying transport (NATS/gRPC) detects a disconnection
- **Then** Tele-Remote must immediately remove the component from the Telegram menu
- **And** it must clean up the command-mapping associated with that instance

### Scenario 3: Identity Conflict (Duplicate UUID)
- **Given** two instances of the same component with identical names/UUIDs
- **When** the second instance tries to register
- **Then** Tele-Remote must (choose one: append a suffix or reject)
- **And** it must ensure the user can distinguish between the two in the UI

## 🛠️ Technical Constraints
- **Transport**: MUST support NATS as the primary registration bus.
- **Security**: Components must present a valid `RegistrationToken` if configured.
