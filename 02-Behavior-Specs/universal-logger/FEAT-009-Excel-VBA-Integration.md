---

type: spec
microservice: business-bdd-brain
repo: universal-logger
feature_id: FEAT-009
status: draft
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---
# Feature: Excel / VBA Integration Patterns

## 🎯 Business Intent
- **User Story**: As an Excel user, I want the logging and configuration system to feel like a native VBA library so that I can use standard VBA event handlers (like `Workbook_Open`) to initialize the system.
- **Problem Solved**: Standardizes the complex FFI boilerplate needed to talk from VBA (32/64-bit) to the Go-compiled DLL.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Workbook-Driven Initialization
- **Given** an Excel Workbook with the `UniLog.bas` module imported
- **When** the `Workbook_Open()` event is triggered
- **Then** it must call the `UniLog_Init` DLL function
- **And** it must provide the standard profile name and configuration path
- **And** it must store the resulting Handle in a global VBA variable for the duration of the session

### Scenario 2: 32-bit vs 64-bit Parity (PtrSafe)
- **Given** an installation of Excel (either 32-bit or 64-bit)
- **When** the VBA module is loaded
- **Then** it must use conditional compilation (`#If Win64`) to declare the correct `PtrSafe` functions
- **And** it must correctly handle the `LongPtr` vs `Long` types for the session handles

### Scenario 3: Cleanup on Workbook Close
- **Given** an active logging session in Excel
- **When** the user closes the workbook
- **Then** the `Workbook_BeforeClose` event must call `UniLog_Close(handle)`
- **And** it must wait for the Go core to confirm the release of network resources before finally exiting

## 🛠️ Technical Constraints
- **Module**: Must provide a standard `.bas` file for easy import into Excel.
- **Dependency**: Must NOT require any external DLLs beyond the `libunilog.dll` and standard Windows system libraries.
