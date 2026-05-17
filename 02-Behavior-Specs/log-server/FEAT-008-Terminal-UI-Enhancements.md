---

type: spec
microservice: business-bdd-brain
repo: log-server
feature_id: FEAT-008
status: active
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/active'
- '#type/spec'
---
# Feature: Terminal UI & Level Colorization

## 🎯 Business Intent
- **User Story**: As a developer watching the log server output in a terminal, I want the log levels to be color-coded (e.g. red for ERROR, yellow for WARN) so that I can quickly spot issues at a glance.
- **Problem Solved**: Improves visual scanning speed during live monitoring.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Fixed-Offset Level Detection
- **Given** a formatted log string where the log level is located at indices `70` to `80`
- **When** the `LogWriter` outputs to `STDOUT`
- **Then** it must extract the substring at `[70:80]` and trim whitespace
- **And** it must identify the level (INFO, WARN, ERROR, DEBUG).

### Scenario 2: ANSI Color Injection
- **Given** a detected log level
- **When** the level is `ERROR` or `FATAL`, it must be colored **Red**
- **When** the level is `WARN`, it must be colored **Yellow**
- **When** the level is `INFO`, it must be colored **Green**
- **When** the level is `DEBUG`, it must be colored **Blue**
- **Then** the final output line must contain the appropriate ANSI escape codes.

### Scenario 3: Falling Back to Plain Text
- **Given** a log line shorter than 80 characters
- **When** the server attempts to colorize it
- **Then** it must skip the colorization logic and print the raw line to prevent index-out-of-bounds errors.

## 🛠️ Technical Constraints
- **Library**: Use the `colored` crate or manual ANSI codes.
- **Performance**: Colorization MUST be performed only for the `STDOUT` stream, not for the file-system writes.
