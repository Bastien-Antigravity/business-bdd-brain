--- 
type: spec
microservice: obsidian-brain

repo: distributed-config
feature_id: FEAT-006
status: draft
---

# Feature: Secret Decryption (AES-GCM)

## 🎯 Business Intent
- **User Story**: As a security officer, I want sensitive configuration values (like API keys) to be encrypted at rest in YAML and decrypted only in memory so that a compromised file system doesn't leak production secrets.
- **Problem Solved**: Prevents "Secrets in Plaintext" vulnerabilities.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Decrypting an Encrypted Key
- **Given** a YAML configuration containing an encrypted value `password: "ENC(base64_blob)"`
- **And** the correct `private.key` file exists in the secure vault directory
- **When** the configuration is loaded
- **Then** the library should detect the `ENC()` prefix
- **And** it should use the private key to decrypt the blob using AES-GCM
- **And** the final value in the `Config` object should be the plaintext password

### Scenario 2: Missing Decryption Key
- **Given** an encrypted value `ENC(...)` in the YAML
- **But** the `private.key` file is missing or inaccessible
- **When** the configuration is loaded
- **Then** the library should log a CRITICAL error
- **And** it should fail to boot the microservice (Fail-Fast) to prevent running with invalid secrets

### Scenario 3: Malformed Encryption Blob
- **Given** an encrypted value `ENC(invalid_base64)`
- **When** decryption is attempted
- **Then** the library should detect the corruption
- **And** it should return a decryption failure error

## 🛠️ Technical Constraints
- **Algorithm**: MUST use **AES-256-GCM** (authenticated encryption).
- **Prefix**: MUST use the `ENC(...)` marker to identify encrypted values.
- **Key Management**: Keys should be loaded from a path defined in `common.common_file_path`.
