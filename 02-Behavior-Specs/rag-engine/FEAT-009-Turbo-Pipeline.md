---
type: spec
microservice: rag-engine
repo: obsidian-brain
feature_id: FEAT-009
status: draft
tags:
- '#service/rag-engine'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#performance'
---
# Feature: Turbo Pipeline Optimization

## 🎯 Business Intent
- **User Story**: As a developer with a large codebase, I want the indexing process to be highly parallelized and efficient so that I don't have to wait a long time for my workspace to be searchable.
- **Problem Solved**: Addresses CPU-bound AST parsing bottlenecks and I/O overhead of single-file embedding operations through multiprocessing and batching.

## 🎬 Scenarios (Gherkin)

### Scenario 1: High-Throughput Bulk Indexing
- **Given** a workspace containing 1000 dummy files
- **And** the `RAG_WALKER_CONCURRENCY` is set to 64
- **When** the indexing pipeline is executed
- **Then** the total indexing time must be at least 50% faster than the sequential baseline
- **And** all 1000 files must be correctly present in the Parent Store, Vector Store, and Lexical Store.

### Scenario 2: Atomic Batch Persistence
- **Given** a batch of 10 files being processed
- **When** a simulated failure occurs during the storage phase (e.g., Vector Store write error)
- **Then** none of the file hashes for that batch should be updated in the Parent Store
- **And** a subsequent indexing run must attempt to re-process all 10 files from that failed batch.

### Scenario 3: Multiprocessing Chunk Integrity
- **Given** a multi-core environment
- **When** the `ProcessPoolExecutor` parses multiple large files concurrently
- **Then** all generated chunks must be correctly returned to the main process
- **And** no chunk data (content or metadata) should be corrupted or lost during inter-process communication.

### Scenario 4: LLM Enricher Concurrency Guard
- **Given** 64 chunks queued for enrichment
- **And** the `LLMEnricher` concurrency limit is set to 4
- **When** the enrichment process starts
- **Then** the number of active external LLM API calls must never exceed 4
- **And** all 64 chunks must eventually be enriched without triggering "429 Too Many Requests" errors.

## 🛠️ Technical Constraints
- **Multiprocessing**: Use `concurrent.futures.ProcessPoolExecutor` for CPU-bound tasks.
- **Batching**: Files must be batched for embedding/storage, but a single file's chunks must NOT be split across batches.
- **Rate Limiting**: Use `asyncio.Semaphore` to enforce concurrency limits in the `LLMEnricher`.

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Indexing 1000 small files completes in under 30 seconds (on standard dev hardware).
- [ ] Failed batches do not leave partial state (specifically, hashes remain old).
- [ ] No "429" errors encountered during stress tests with simulated LLM delays.
- [ ] 100% chunk integrity verified by comparing source file stats with store counts.
