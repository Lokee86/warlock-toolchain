# Grimoire

Grimoire is an in-development lightweight repository context engine. Its core job is to compile a small, relevant, token-bounded context package from a continuously maintained local index before a human, agent, editor, or automation system begins work.

Grimoire is independently useful. Arcana and Demon Docs can improve its results, but neither is required for its basic operation.

## Product definition

Grimoire is best described as a lightweight, deterministic-first RAG engine and low-latency context compiler.

Its base workflow is:

```text
task or query
    ↓
retrieve candidates from a prepared local index
    ↓
rank candidates with fixed, inspectable signals
    ↓
select structural chunks within a token budget
    ↓
emit a context package and selection manifest
```

This is retrieval for retrieval-augmented generation, but Grimoire does not own the generation step and is not tied to an agent. Its output should be usable by coding agents, IDEs, MCP clients, command-line workflows, benchmarks, automation, and humans.

## Core independence

Grimoire's basic function does not depend on Arcana.

Standalone Grimoire can index and rank evidence using:

- lexical retrieval such as BM25 or equivalent existing implementations;
- filenames, paths, symbols, headings, and exact terms;
- code and document chunk boundaries;
- test-file and directory conventions;
- Git metadata and current changes;
- configured importance, inclusion, and exclusion rules;
- optional local semantic embeddings; and
- fixed scoring and token-budget rules.

This provides fast, inspectable, repository-native retrieval without requiring a graph database, hosted service, remote model, or agent loop.

## Index maintenance and request path

Expensive repository work belongs on the index-maintenance path, not the context-request path.

```text
repository change
    ↓
re-chunk changed content
    ↓
update lexical records and metadata
    ↓
embed only changed chunks when semantic retrieval is enabled
    ↓
store prepared retrieval state
```

A context request should operate only on prepared state:

```text
new task
    ↓
produce one query embedding when semantic retrieval is enabled
    ↓
query prepared lexical, metadata, and vector indexes
    ↓
merge and rank candidates
    ↓
fit the context budget
    ↓
return immediately
```

Grimoire must not scan, parse, or re-embed the repository while a caller waits for context.

## Lightweight semantic retrieval

Grimoire may ship with a small local embedding model. The model is an indexing and retrieval dependency, not an embedded agent.

The intended constraints are:

- local and usable offline;
- small enough for CPU inference and continuous daemon use;
- fixed model weights, tokenizer, normalization, and chunking;
- incremental embedding of changed chunks only;
- no generative inference;
- no LLM query rewriting or reranking;
- no remote embedding API;
- no external vector database; and
- no semantic stage allowed to stall the fast path.

Embeddings add semantic similarity as one retrieval signal. They do not control the retrieval workflow.

A fixed embedding model can provide highly stable results. Perfect bit-for-bit reproducibility across every runtime and machine is not required. Grimoire may trade ordering stability among marginal candidates for lower latency, while keeping high-confidence results stable and recording enough metadata to inspect how a package was produced.

## Ranking

Initial ranking should remain simple and explainable. Candidate signals may include:

- lexical relevance;
- semantic similarity;
- path and filename relevance;
- symbol or heading matches;
- test relationships inferred from conventions;
- current Git changes and recency;
- repository configuration;
- documentation authority supplied by Demon Docs; and
- relationship evidence supplied by Arcana.

Grimoire should use existing libraries and formats wherever practical. It should not create a tokenizer, vector database, parser framework, ranking language, or model runtime unless existing options fail a demonstrated requirement.

## Token budgeting and chunking

Grimoire needs model-aware token counting, but it should consume an existing tokenizer implementation rather than build one.

The index should retain structural chunks such as:

- functions, methods, types, and related comments;
- Markdown heading sections and fenced blocks;
- configuration objects or named sections; and
- other language-adapter-provided ranges.

The context compiler selects whole useful chunks where possible, counts or conservatively estimates their cost, and stops when the caller's context budget is filled. Arbitrary cuts through functions, fenced blocks, structured data, or other meaningful units should be avoided.

## Latency requirement

Low latency is a defining product constraint.

Grimoire should be cheap enough to run before routine agent and developer interactions without becoming noticeable. A normal request should use warm indexes, bounded provider deadlines, cached token costs, and concurrent retrieval. It must not routinely add tens of seconds to time to first token.

The operating principle is:

> Maintain knowledge continuously; compile context immediately.

When providers or enrichment stages miss their deadline, Grimoire should return the best context already available rather than block for a theoretically perfect package.

## Context packages

A package should contain:

- the original task or query;
- selected files, chunks, and source ranges;
- token counts or conservative estimates;
- compact reasons for inclusion;
- index and provider freshness;
- retrieval sources that contributed candidates;
- omitted or timed-out optional stages; and
- a manifest suitable for inspection, caching, and benchmarks.

The package format should be independent of any specific agent or model provider.

## Optional integrations

### Arcana

Arcana is an optional relationship provider. It can supply candidates that lexical and semantic retrieval may miss, including indirect callers, state mutation, serialization, tests through abstractions, dependency paths, and other repository relationships.

Grimoire owns context selection and budgeting. Arcana owns durable repository relationships. Grimoire must not grow its own general-purpose relationship graph to duplicate Arcana.

### Demon Docs

Demon Docs is an optional documentation provider. It can supply document identities, indexes, codemaps, links, schemas, authority, validation state, and staleness evidence.

Grimoire may use those facts to rank documentation, but it does not own documentation maintenance or health.

### Warlock

Grimoire remains independently runnable. When hosted by Warlock, it should consume shared repository change events, lifecycle supervision, health reporting, and other concrete runtime services instead of duplicating them.

## Non-goals

Grimoire is not intended to become:

- an agent orchestrator;
- a generative assistant;
- a hosted RAG platform;
- an external vector-database deployment;
- an autonomous multi-stage retrieval loop;
- a replacement for Arcana;
- a replacement for Demon Docs; or
- a universal repository intelligence monolith.

## Current implementation baseline

The first implementation slice now exists in the standalone `grimoire` project directory. It provides:

- an incremental private go-git object repository with unchanged-file reuse;
- deterministic binary file records distributed across 256 content-addressed shards;
- atomic snapshot publication through `refs/grimoire/state`;
- standard root and nested `.gitignore` traversal, with an optional replacement ignore file;
- permanent exclusion of Git metadata, tool-state directories, worktree containers, and the configured Grimoire state path;
- a deterministic fallback chunker for supported text files;
- lexical, filename, path, and leading-line ranking signals;
- whole-chunk fitting under a conservative content-token budget;
- inspectable, agent-independent JSON context packages;
- a CLI path that reads only prepared index state during context requests; and
- tests plus a warm retrieval benchmark over 10,000 prepared chunks.

This is deliberately the lexical baseline. Language-aware chunking, model-specific tokenizers, semantic retrieval, Arcana evidence, Demon Docs evidence, daemon maintenance, and Warlock hosting remain later work.

## Implementation direction

Grimoire is implemented in Go because the product requires fast startup, simple distribution, predictable concurrency, and close operational compatibility with the rest of the toolchain. The product boundary remains language-independent.

The first useful version should remain narrow:

1. Maintain a local incremental chunk index.
2. Provide lexical retrieval and simple deterministic ranking.
3. Fit selected chunks into a model-aware token budget.
4. Emit inspectable context packages.
5. Add a small onboard embedding model once the lexical baseline and latency benchmarks can measure its value and cost.
6. Add Demon Docs and Arcana as optional evidence providers through stable interfaces.
