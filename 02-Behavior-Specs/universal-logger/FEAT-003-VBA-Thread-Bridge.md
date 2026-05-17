---
type: spec
microservice: business-bdd-brain
repo: universal-logger
feature_id: FEAT-003
status: draft
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---
# Feature: VBA Thread Bridge (Windows Message Proxy)

## 🎯 Business Intent
- **User Story**: As an Excel/VBA developer, I want to receive configuration updates and log events from the background Go threads without crashing Excel, which is single-threaded.
- **Problem Solved**: Prevents "Deadly Thread Access" crashes in Excel when a background Go goroutine attempts to directly call a VBA callback.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Proxying a Callback via Windows Message
- **Given** a VBA application registered to receive configuration updates
- **When** the Go core receives a `BROADCAST_SYNC` from the server
- **Then** the `cgo_bridge` must NOT call the VBA function directly
- **And** it must instead use `PostMessageA` to send a specific `WM_USER` message to a hidden proxy window (`HWND_MESSAGE`)
- **And** the VBA side must pick up this message on the main thread and execute the final callback safely

### Scenario 2: Hidden Window Initialization
- **Given** the first call to `UniLog_Init` on Windows
- **When** the library starts
- **Then** it must automatically register a new Window Class and create a hidden `HWND_MESSAGE` window
- **And** this window must survive for the entire duration of the process to act as the "Thread Bridge."

### Scenario 3: Memory Safety (WPARAM/LPARAM)
- **Given** a data update (JSON string) being sent to VBA
- **When** the Windows Message is posted
- **Then** the bridge must ensure the memory for the string is pinned or safely copied
- **And** it must be released ONLY after the VBA thread has confirmed receipt of the message

## 🛠️ Technical Constraints
- **Platform**: This feature is EXCLUSIVELY for Windows (`GOOS=windows`).
- **Mechanism**: MUST use the Win32 API (`User32.dll`) for window management and message pumping.
