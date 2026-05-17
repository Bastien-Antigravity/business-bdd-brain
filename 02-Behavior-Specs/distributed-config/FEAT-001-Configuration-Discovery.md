---
type: spec
microservice: business-bdd-brain
repo: distributed-config
feature_id: FEAT-001
status: draft
tags:
- '#service/business-bdd-brain'
- '#state/draft'
- '#type/spec'
---

# Feature: Configuration File Discovery

## 🎯 Business Intent
- **User Story**: As a DevOps engineer, I want the microservice to automatically find its configuration file in standard locations (CWD or Binary directory) so that I don't have to provide absolute paths in every deployment script.
- **Problem Solved**: Eliminates "File Not Found" errors during deployment by providing a resilient, multi-step search strategy.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Discovery in Current Working Directory (CWD)
- **Given** a microservice named "data-ingestor" running in `/home/user/app/`
- **And** a configuration file exists at `/home/user/app/config/data-ingestor.yaml`
- **When** the service initializes the `distributed-config` library
- **Then** the loader should find the file in the `config/` subdirectory of the CWD
- **And** it should stop searching further

### Scenario 2: Fallback to Binary Directory
- **Given** the CWD is empty of configuration
- **And** the microservice binary is located at `/usr/local/bin/data-ingestor`
- **And** a configuration file exists at `/usr/local/bin/config/data-ingestor.yaml`
- **When** the service initializes
- **Then** the loader should fail to find the file in the CWD
- **And** it should successfully resolve the binary path and find the file in the binary's `config/` folder

### Scenario 3: The 6-Step Search Sequence
- **Given** no explicit configuration path is provided
- **When** the loader starts
- **Then** it must follow this exact priority order:
    1. `[CWD]/config/[profile].yaml`
    2. `[CWD]/config/[exe_name].yaml`
    3. `[CWD]/[exe_name].yaml`
    4. `[BIN_DIR]/config/[profile].yaml`
    5. `[BIN_DIR]/config/[exe_name].yaml`
    6. `[BIN_DIR]/[exe_name].yaml`

### Scenario 4: Fail-Fast on Missing Config
- **Given** no configuration file exists in any of the 6 standard locations
- **When** the service initializes
- **Then** the library should return a "Configuration Not Found" error
- **And** the microservice should abort its startup (Fail-Fast)

## 🛠️ Technical Constraints
- **Resolution**: Use `os.Executable()` to determine the binary directory.
- **Logging**: The loader should log the final resolved path for troubleshooting.
