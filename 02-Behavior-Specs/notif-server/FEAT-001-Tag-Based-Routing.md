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

### Scenario 3: Default Routing (No Tag Match or Unmapped Level)
- **Given** a notification with unmapped tags `[UNKNOWN]` or an unmapped severity level or no tags
- **And** no active provider mapping exists for this tag/level
- **When** the message is processed
- **Then** the server must route the message directly to the `DefaultSender` (Telegram)
- **And** the message content must be prefixed with a clear visual banner: `⚠️ [Default Fallback - <Reason>]` so operators immediately notice the fallback in the notification
- **And** it must log an informative message in daemon logs indicating default routing was used

## 🛠️ Technical Constraints
- **Config**: Routing maps must be loaded from `distributed-config` at startup.
- **Efficiency**: Routing lookup should be an O(1) map operation.
