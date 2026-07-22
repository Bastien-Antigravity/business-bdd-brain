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
# Feature: Tag-Based Alert Routing

## 🎯 Business Intent
- **User Story**: As a SRE, I want my critical alerts to go to Telegram and my informational logs to go to Discord so that I only get paged for things that require immediate action.
- **Problem Solved**: Prevents "Notification Fatigue" by separating urgent alerts from background noise.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Routing by Single Tag
- **Given** a notification with tag `ALERT`
- **And** the server is configured to map `ALERT` to `TelegramSender`
- **When** the message is processed
- **Then** the server must forward the message to the Telegram API
- **And** it must NOT send it to Discord or Gmail

### Scenario 2: Multiple Tag Routing
- **Given** a notification with tags `[CRITICAL, SECURITY]`
- **And** `CRITICAL` is mapped to `Telegram` and `SECURITY` is mapped to `Gmail`
- **When** the message is processed
- **Then** the server must send the alert to BOTH Telegram and Gmail
- **And** it must ensure the message content is correctly formatted for each destination

### Scenario 3: Default Routing (No Tag Match)
- **Given** a notification with a tag `UNKNOWN`
- **And** no mapping exists for this tag
- **When** the message is processed
- **Then** the server should use the `DefaultSender` (if configured)
- **Or** it should log a "Routing Failure" and discard the message to prevent silent loss

## 🛠️ Technical Constraints
- **Config**: Routing maps must be loaded from `distributed-config` at startup.
- **Efficiency**: Routing lookup should be an O(1) map operation.
