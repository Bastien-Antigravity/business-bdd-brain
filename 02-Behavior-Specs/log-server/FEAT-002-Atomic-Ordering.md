---

type: spec
microservice: business-bdd-brain
repo: log-server
feature_id: FEAT-002
status: active
tags:
- '#zone/3-fleet'
- '#service/business-bdd-brain'
- '#state/active'
- '#type/spec'
---
# Feature: Atomic Sequencing & Re-ordering

## 🎯 Business Intent
- **User Story**: As a developer debugging a race condition, I want the log files to reflect the exact order in which the server received the messages, even if multiple clients are writing simultaneously, so that I can trust the timeline of events.
- **Problem Solved**: Prevents "Log Interleaving" where messages from different threads or services get mixed up out of order in the final file.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Arrival-Time Sequencing
- **Given** two messages `M1` and `M2` arriving from different clients
- **When** `M1` hits the server's input buffer 1ns before `M2`
- **Then** the `AtomicSequencer` must assign ID `N` to `M1` and `N+1` to `M2`
- **And** this assignment must be thread-safe

### Scenario 2: BTreeMap Re-ordering
- **Given** a batch of messages arriving out-of-order due to network jitter
- **When** the `LogWriter` prepares to write to disk
- **Then** it must store the messages in a `BTreeMap<u64, String>`
- **And** it must iterate over the map in ascending order of the sequence IDs
- **And** it must write the sorted results to the log file

### Scenario 3: Missing Sequence Warning
- **Given** the sequencer expects ID `N` but receives ID `N+1` (potential drop)
- **When** the "Gap Timer" (500ms) ticks without receiving ID `N`
- **Then** the server must log a `[SEQUENCE_GAP_WARNING]`
- **And** it must skip to ID `N+1` to resume processing.

### Scenario 4: Buffer Pressure (Force Progress)
- **Given** the internal `BTreeMap` reaches the current `batch_size`
- **And** the expected sequence number `current_sequence` is still missing
- **When** a new message arrives
- **Then** the server must "Force Progress" by jumping to the lowest available sequence number in the buffer
- **And** it must log a `[BUFFER_FULL_WARNING]` to indicate potential data loss due to memory constraints.

## 🛠️ Technical Constraints
- **Mechanism**: MUST use `std::sync::atomic::AtomicU64` for sequencing.
- **Storage**: Use `std::collections::BTreeMap` for efficient in-memory sorting.
- **Backpressure**: Buffer size is limited to 1024 entries before pressure logic triggers.
