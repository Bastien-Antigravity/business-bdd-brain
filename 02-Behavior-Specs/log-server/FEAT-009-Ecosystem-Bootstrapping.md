--- 
type: spec
microservice: obsidian-brain

repo: log-server
feature_id: FEAT-009
status: active
---

# Feature: Ecosystem Bootstrapping (Minimal Toolbox Integration)

## 🎯 Business Intent
- **User Story**: As a resilient microservice, I want to initialize my configuration using the `microservice-toolbox` hierarchy so that I can start up reliably in any environment (Standalone, Dev, or Production) even with minimal local configuration.
- **Problem Solved**: Ensures the server can recover from a missing Config Server by falling back to local files and CLI overrides, maintaining the "Ecosystem Loading Law".

## 🎬 Scenarios (Gherkin)

### Scenario 1: Hierarchical Configuration Loading (The toolbox path)
- **Given** the server starts with the `standalone` profile
- **When** the `AppConfig` is initialized
- **Then** it must attempt to connect to the `libdistconf` bridge (Config Server)
- **And** if the bridge is unavailable, it must fall back to loading `standalone.yaml` from the local directory or `config/` folder
- **And** it must expand any environment variables in the YAML using the `${VAR:default}` syntax.

### Scenario 2: CLI Hard Overrides (Minimal Config)
- **Given** a local `standalone.yaml` specifies port `8000`
- **When** the user starts the server with `--port 9020`
- **Then** the toolbox must prioritize the CLI argument over the file-based configuration
- **And** the server must bind to the port provided in the CLI.

### Scenario 3: Relative gRPC Port Fallback (Convenience Logic)
- **Given** a TCP port is determined (either via CLI, Bridge, or File)
- **And** no explicit gRPC port is provided in the configuration or CLI
- **When** the server prepares to bind its components
- **Then** the `main.rs` logic must default the gRPC port to `TCP_PORT + 1`
- **And** it must successfully start the gRPC listener on this calculated address.

### Scenario 4: Concurrent Component Orchestration
- **Given** the configuration is fully resolved
- **When** the `LogServer::run` method executes
- **Then** it must spawn both the TCP and gRPC server tasks concurrently using `tokio::spawn`
- **And** it must share a single `LogWriter` and a single `AtomicU64` sequence counter between both servers to ensure unified ordering.

## 🛠️ Technical Constraints
- **Library**: MUST use `microservice_toolbox::config::load_config` for the primary bootstrap.
- **Port Fallback**: The `+1` gRPC logic MUST be handled in `main.rs`, as it is a `log-server` specific convenience, not a toolbox global rule.
- **Resilience**: The server MUST NOT crash if the `libdistconf` bridge is missing, provided a local `standalone.yaml` or enough CLI arguments are present.
