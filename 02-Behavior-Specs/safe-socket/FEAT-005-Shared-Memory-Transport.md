---
type: spec
microservice: obsidian-brain
repo: safe-socket
feature_id: FEAT-005
status: draft
tags:
- \'#service/obsidian-brain\'
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Shared Memory (SHM) Transport

## 🎯 Business Intent
- **User Story**: As a high-frequency trading developer, I want two processes on the same machine to communicate with sub-microsecond latency so that network overhead doesn't slow down the signal chain.
- **Problem Solved**: Provides an ultra-low-latency IPC mechanism that bypasses the kernel's network stack.

## 🎬 Scenarios (Gherkin)

### Scenario 1: SHM Buffer Initialization
- **Given** an address pointing to a file path (e.g., "/tmp/safe.shm")
- **When** the SHM transport is initialized
- **Then** it must create or open the file and `mmap` it into the process address space
- **And** it must set up a circular ring buffer for bidirectional communication

### Scenario 2: Zero-Copy Messaging (Spin-Wait)
- **Given** an empty SHM buffer
- **When** the sender writes a message
- **Then** it must update the "Tail" pointer in the shared memory segment
- **And** the receiver, using a **Spin-Wait** strategy (busy loop), must detect the pointer update immediately without waiting for a kernel interrupt

### Scenario 3: Cleanup on Exit
- **Given** an active SHM connection
- **When** the socket is closed
- **Then** it must `munmap` the memory segment
- **And** if it was the last owner, it should attempt to delete the temporary `.shm` file from the filesystem

## 🛠️ Technical Constraints
- **Concurrency**: MUST use atomic memory operations or spin-locks for head/tail pointer synchronization.
- **Latency Threshold**: Heartbeats should be disabled if the deadline is `< 50ms` (SHM-specific threshold).
- **Security**: File permissions on the `.shm` file should be restricted to the user running the processes.
