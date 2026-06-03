---
type: spec
microservice: obsidian-brain
repo: obsidian-brain
feature_id: FEAT-002
status: draft
tags:
- '#service/obsidian-brain'
- '#state/draft'
- '#tier/behavior'
- '#type/spec'
- '#zone/3-fleet'
---
# Feature: Hybrid Query Engine

## 🎯 Business Intent
- **User Story**: As a user, I want to query the RAG engine using natural language and receive results that combine both semantic meaning and exact keyword matches to maximize relevance.
- **Problem Solved**: Overcomes the limitations of purely semantic search by merging it with BM25 lexical search using Reciprocal Rank Fusion (RRF).

## 🎬 Scenarios (Gherkin)

### Scenario 1: Parallel Execution & Fusion
- **Given** a search query and a requested result count (n)
- **When** the hybrid query engine processes the request
- **Then** it must query the Vector Store and Lexical Store in parallel
- **And** it must combine both result sets using Reciprocal Rank Fusion (RRF)
- **And** it must return the top n most relevant chunks.

### Scenario 2: Context Hydration
- **Given** a set of ranked chunks from the hybrid engine
- **When** the facade returns the results
- **Then** each chunk raw content must be hydrated from the Parent Store
- **And** the chunk alignment status and linked Obsidian notes must be retrieved from the Alignment Service.

## 🛠️ Technical Constraints
- **Performance**: The underlying queries to Chroma and BM25 MUST be executed asynchronously in parallel.
- **Algorithm**: The RRF constant `k` should be set to 60 by default.

## ✅ Acceptance Criteria (Definition of Done)
- [ ] Semantic and Lexical searches execute concurrently.
- [ ] RRF algorithm correctly ranks results present in both stores higher than isolated results.
- [ ] Final output contains fully hydrated raw content and alignment data.

## 🔗 Sandbox Binding
- **Feature**: `[tests/features/FEAT-002-query.yaml]`
