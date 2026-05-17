---
type: spec
microservice: business-bdd-brain
repo: tele-remote
feature_id: FEAT-003
status: draft
tags:
- \'#zone/3-fleet\'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: Bidirectional Command Routing

## 🎯 Business Intent
- **User Story**: As a trader, when I press "BUY" on Telegram, I want that command to reach my Trading Bot within milliseconds, so that I don't miss the market entry.
- **Problem Solved**: Provides low-latency "Remote Control" over the entire microservice fleet.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Executing a Remote Command
- **Given** a user clicks an inline button mapped to `cmd_reboot` for "MarketObserver"
- **When** the callback hits Tele-Remote
- **Then** the server must look up the communication channel for "MarketObserver"
- **And** it must publish a `COMMAND_EXEC` message to the component's private NATS subject
- **And** it must show a "Waiting for Confirmation..." spinner to the user

### Scenario 2: Command Acknowledgment
- **Given** a command was sent to a component
- **When** the component returns a `COMMAND_ACK` (Success)
- **Then** Tele-Remote must update the Telegram message to "✅ Command Executed"
- **And** it must close the interaction loop

### Scenario 3: Execution Timeout
- **Given** a command sent to a component
- **When** no response is received within 5 seconds
- **Then** Tele-Remote must update the message to "❌ Timeout: No response from component"
- **And** it must log a "Command Failure" for audit

## 🛠️ Technical Constraints
- **Latency**: Command dispatch must occur in <50ms.
- **Audit**: Every command sent from Telegram must be logged in the central `log-server` with the `UserID` and `CommandPayload`.
