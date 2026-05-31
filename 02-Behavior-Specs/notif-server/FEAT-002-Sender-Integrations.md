---
type: spec
microservice: notif-server
repo: notif-server
feature_id: FEAT-002
status: draft
tags:
- '#service/notif-server'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#zone/1-frozen'
---
# Feature: External Sender Integrations (Reliability)

## 🎯 Business Intent
- **User Story**: As a developer, I want my notifications to be reliably delivered even if the Telegram API is temporarily down, so that no critical alerts are lost during an outage.
- **Problem Solved**: Handles transient failures of 3rd-party services without losing data.

## 🎬 Scenarios (Gherkin)

### Scenario 1: API Retry on Transient Failure
- **Given** a Telegram API that returns a `503 Service Unavailable`
- **When** the `TelegramSender` attempts to send a message
- **Then** it should wait for a randomized delay
- **And** it should retry the request up to 3 times
- **And** if all retries fail, it must move the message to a "Failed Notifications" queue/log

### Scenario 2: Async Delivery (Non-Blocking)
- **Given** an incoming burst of 100 notifications
- **When** the server processes them
- **Then** each external API call must be executed in a separate goroutine
- **And** a slow API (e.g. Gmail SMTP) must NOT block the delivery of fast API calls (e.g. Telegram)

### Scenario 3: Secret Management (API Keys)
- **Given** a configuration for a Telegram Bot
- **When** the server initializes
- **Then** it must retrieve the `BotToken` from the encrypted `distributed-config` store
- **And** it must NOT store the plaintext token in memory beyond the sender instance

## 🛠️ Technical Constraints
- **Concurrency**: Use a worker pool or bounded goroutines to prevent overloading external APIs.
- **Timeout**: Each API call MUST have a strict 10-second timeout.
