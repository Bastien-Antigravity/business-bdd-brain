---
type: spec
microservice: obsidian-brain
repo: distributed-config
feature_id: FEAT-002
status: draft
tags:
- \'#service/obsidian-brain\'
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Environment Variable Expansion

## 🎯 Business Intent
- **User Story**: As a cloud architect, I want to inject secrets and environment-specific settings (like database URLs) into my YAML config using environment variables so that I don't store sensitive data in git.
- **Problem Solved**: Enables dynamic configuration without modifying the static YAML files.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Standard Variable Expansion
- **Given** an environment variable `DB_HOST=prod-db.internal`
- **And** a YAML configuration containing `host: "${DB_HOST}"`
- **When** the configuration is loaded
- **Then** the value of `host` should be expanded to "prod-db.internal"

### Scenario 2: Variable with Default Value
- **Given** the environment variable `LOG_LEVEL` is NOT set
- **And** a YAML configuration containing `level: "${LOG_LEVEL:info}"`
- **When** the configuration is loaded
- **Then** the value should fallback to the default value "info"

### Scenario 3: Recursive Expansion
- **Given** `APP_NAME=my-app`
- **And** `LOG_PATH="/logs/${APP_NAME}/"`
- **And** a YAML config containing `path: "${LOG_PATH}"`
- **When** the configuration is loaded
- **Then** the library should perform recursive expansion
- **And** the final path should be "/logs/my-app/"

### Scenario 4: Missing Variable without Default
- **Given** the environment variable `SECRET_KEY` is NOT set
- **And** a YAML configuration containing `key: "${SECRET_KEY}"` (no default provided)
- **When** the configuration is loaded
- **Then** the value should remain as the literal string "${SECRET_KEY}" or return an empty string depending on the safety policy
- **And** a warning should be logged about the missing variable

## 🛠️ Technical Constraints
- **Syntax**: MUST support `${VAR}` and `${VAR:default}`.
- **Timing**: Expansion MUST happen after YAML parsing but before the configuration object is finalized.
- **Safety**: Avoid expanding variables inside comments or non-string fields if possible.
