# 🛡️ Spec: SafeSocket Hardening (OOM & Shutdown)

## FEAT-004: OOM Protection (Framed TCP)
**Context**: A malicious client or a protocol error can send a large length prefix in a TCP stream, causing the server to allocate massive amounts of memory.

### Scenario: Rejecting oversized frames
- **Given** a SafeSocket server is listening on TCP
- **And** the `MaxPayloadSize` is set to 16,777,216 bytes (16MB)
- **When** a client connects and sends a 4-byte header with value `20,000,000`
- **Then** the server MUST NOT allocate the 20MB buffer
- **And** the server MUST close the connection immediately
- **And** the server SHOULD log a "Payload too large" warning

---

## FEAT-003: Synchronous Graceful Shutdown
**Context**: When a server stops, it must ensure that all active connections are closed and their resources (goroutines, buffers) are released before the process exits.

### Scenario: Waiting for active connections
- **Given** a SafeSocket server has 5 active client connections
- **When** the server `Close()` method is called
- **Then** the listener MUST stop accepting new connections
- **And** the `Close()` method MUST block until all 5 active connections have been closed
- **And** the server process can then exit safely
