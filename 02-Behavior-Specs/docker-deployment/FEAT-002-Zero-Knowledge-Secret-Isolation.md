---
microservice: docker-deployment
type: note
status: active
tags:
- '#service/docker-deployment'
- '#type/note'
- '#state/active'
- '#zone/3-fleet'
---
# FEAT-002: Sovereign Zero-Knowledge Secret Isolation

*Back-link: [[docker-deployment/README|⬅️ docker-deployment]]*
---

# Feature: Cryptographic Secret & Key Isolation

## 🎯 Business Intent
- **User Story**: As a security architect, I want cryptographic RSA keys stored strictly outside of all git repository workspaces (`~/.bastien/keys/`) and plaintext secrets kept strictly in volatile process memory so that accidental commits, repository clones, and container inspections never compromise ecosystem credentials.
- **Problem Solved**: Prevents credential leakage into Git trees, environment dumps, or container layers by enforcing filesystem isolation and read-only volume mounting.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Sovereign Key Isolation Outside Workspaces
- **Given** an orchestrator launch sequence (Native or Docker)
- **When** the key resolution engine executes (`ensure_keys_exist`)
- **Then** it must check for keys in the secure user directory `~/.bastien/keys/private.pem`
- **And** it must NEVER store, create, or check for private keys inside any git repository directory
- **And** if keys are missing, it must automatically generate a secure 4096-bit RSA key pair with restricted POSIX permissions (`0600`)

### Scenario 2: Zero-Knowledge Central Configuration
- **Given** microservices receiving configuration from `config-server` or `web-interface`
- **When** configuration files (`native.yaml`, service slices) are loaded or transmitted
- **Then** all sensitive credentials must remain strictly in `ENC(...)` ciphertext format
- **And** central distributors (`config-server`, `web-interface`) must NOT possess private keys and cannot decrypt payloads
- **And** only authorized consumer microservices possessing their local private key can perform on-demand decryption in memory

### Scenario 3: Read-Only Volume Mounting in Containers
- **Given** containerized execution (`modes/docker/` or `modes/production/`)
- **When** the Docker stack is launched
- **Then** the host private key must be mounted read-only (`:ro`) into container path `/etc/bastien/private.pem`
- **And** the private key MUST NOT be passed as a plaintext environment variable string (`BASTIEN_PRIVATE_KEY`) to prevent inspection leaks via `docker inspect`

## 🛠️ Technical Constraints
- **Key Algorithm**: RSA 4096-bit PEM format.
- **Storage Location**: `~/.bastien/keys/private.pem` (User Home directory, outside git roots).
- **Decryption Engine**: On-demand in-memory decryption via `microservice-toolbox` and `distributed-config`.
