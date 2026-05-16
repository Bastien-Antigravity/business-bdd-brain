---
type: spec
microservice: obsidian-brain
repo: universal-logger
feature_id: FEAT-012
status: draft
tags:
- \'#service/obsidian-brain\'
- '#state/draft'
- null
- '#type/spec'
---

# Feature: VBA Message Loop Hook

## 🎯 Business Intent
- **User Story**: As a VBA developer, I want the configuration updates to be processed even if Excel is busy (e.g., during a long calculation), so that my system state is always current.
- **Problem Solved**: Ensures the "Heartbeat" of the Windows message pump continues even when the main thread is occupied by VBA code.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Processing the Message Queue
- **Given** an active `HWND_MESSAGE` window created by the bridge
- **When** the Go core posts a message
- **Then** the VBA application must periodically call `DoEvents` or use a `PeekMessage` loop
- **And** it must correctly dispatch the message to the internal `UniLog_WndProc` handler

### Scenario 2: Synchronous Wait (Deadlock Prevention)
- **Given** a VBA thread that is waiting for a configuration value
- **When** a message is posted from Go
- **Then** the VBA side must NOT block indefinitely (Deadlock)
- **And** it must allow the Windows Message queue to process the pending update before continuing

### Scenario 3: Proxy Window Lifetime
- **Given** a hidden proxy window
- **When** the VBA session ends
- **Then** it MUST call `DestroyWindow` or a Go cleanup function to release the `HWND` and its associated resources in the Win32 subsystem

## 🛠️ Technical Constraints
- **Subclassing**: Optional but recommended for high-performance message interception.
- **Threading**: This logic ensures that the cross-thread communication remains "Non-Blocking" for both Go and Excel.
