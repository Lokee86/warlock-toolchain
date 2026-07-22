# Warlock Toolchain Roadmap

This roadmap separates available software, active development, and intended direction. It is not a release commitment.

## Available

### Demon Docs

Demon Docs provides deterministic documentation maintenance and repository-owned Markdown governance, including indexes, links, schemas, frontmatter, document structure, codemap management, review workflows, and watcher automation.

## In development

### Arcana

Arcana is establishing a language-independent repository graph foundation.

Current work focuses on:

- synthetic graph workloads;
- alternative graph storage models;
- deterministic graph construction and querying;
- repository-scale performance characteristics; and
- a stable fact model that other Warlock tools can consume.

Likely next work includes consuming Lexicon's normalized source facts, persistence contracts, and integration boundaries for documentation and policy tools. Language adapters are owned by Lexicon rather than duplicated inside Arcana.

### [Grimoire](docs/GRIMOIRE.md)

Grimoire is being built as a lightweight local context compiler around continuously maintained repository indexes. Its first lexical baseline now provides incremental indexing, deterministic ranking, bounded whole-chunk selection, and inspectable JSON context packages. Later stages will add structural metadata, model-aware tokenization, and optional onboard embedding retrieval.

Arcana and Demon Docs will be optional evidence providers rather than foundational dependencies. Grimoire should remain agent-independent, avoid generative models and heavy RAG infrastructure, and keep interactive context compilation within strict latency bounds by moving parsing, chunking, and repository embedding work to incremental index maintenance.

### [Lexicon](https://github.com/Lokee86/lexicon)

Lexicon is establishing the shared language-analysis layer for the Warlock toolchain: one comprehensive adapter per programming language plus a versioned normalized schema for symbols, dependencies, calls, references, source locations, and unresolved relationships.

The standalone repository now owns the neutral facts-v1 contract. Initial Ruby, Python, GDScript, Rust, and TypeScript adapters are under development in isolated streams. The existing Arcana Go adapter will migrate after active Arcana work leaves a safe extraction boundary.

Lexicon is an explicit shared dependency for tools that require language understanding. Tool independence means the tools remain separate usable products; it does not require duplicating language-analysis work in every repository.

### [Homunculus](docs/HOMUNCULUS.md)

Homunculus now has its first standalone implementation slice: deterministic Go and Python `layered-service` specimens, content-hashed manifests, one build-preserving `api-bypasses-service` mutation, expected relationship and Pitlord diagnostic ground truth, and language-level invariant verification.

Next work should validate the generated specimens through Lexicon and Arcana, then use them to drive Pitlord's first direct architecture-boundary rule before broadening the profile or mutation vocabulary.

## Planned

### Pitlord

Pitlord will provide polyglot architecture and ownership enforcement. It will consume normalized paths, dependencies, symbols, relationships, and source locations from Lexicon, then apply Pitlord-owned policy without maintaining separate language adapters.

### Cantrip

Cantrip will provide portable, versioned, and testable skills for agents and automation. Skills should be small, reusable capabilities that can travel with a repository or be distributed independently without depending on one agent, model, account, or provider.

### [Incubus](docs/INCUBUS.md)

Incubus will provide deterministic runtime-adversity testing for live systems. It will coordinate declared network, process, dependency, resource, timing, storage, and event-stream disruptions, capture the resulting evidence, and evaluate explicit recovery or convergence assertions.

Space Rocks will be the first concrete target. Initial work should focus on repeatable multiplayer and distributed-service failure scenarios rather than attempting to create a universal chaos platform. Incubus complements Homunculus: Homunculus creates or mutates controlled repository specimens, while Incubus subjects running systems to controlled hostile conditions.

### Ritual

Ritual will define deterministic execution protocols for agent-assisted work. It is not an agent orchestrator. Where Cantrip describes reusable skills, Ritual will describe the larger bounded procedure: ordered phases, permitted scope, required checkpoints, expected artifacts, verification, failure handling, and completion conditions. Its purpose is to reduce behavioral variance and make agent-driven workflows more repeatable, inspectable, and testable.

### [Warlock Runtime](docs/WARLOCK_RUNTIME.md)

Warlock itself will become the shared runtime and supervisor for the toolchain's daemon swarm. The planned runtime will coordinate component discovery, process lifecycle, shared repository watching, normalized change events, health, diagnostics, local IPC, and opt-in shared object storage while preserving standalone operation and domain ownership in every component.

Runtime contracts should begin with concrete duplication already present across multiple tools. Warlock should coordinate focused services rather than collapse them into a monolith.

## Shared integration direction

The toolchain is expected to converge on shared concepts where doing so creates real value:

- repository discovery and ignore behavior;
- shared repository watching and normalized change events;
- daemon discovery, supervision, health, and local IPC;
- per-tool opt-in shared object storage with standalone and custom-path exemptions;
- stable file and document identity;
- Lexicon-owned normalized paths, symbols, dependencies, and relationships;
- incremental fingerprints and cache identities;
- machine-readable diagnostics;
- explicit ownership and mutation boundaries;
- portable configuration conventions; and
- versioned interfaces between independently useful tools.

Shared infrastructure should be extracted only after component needs make the boundary concrete. Warlock's runtime should remove duplicated operational work while the toolchain remains a collection of focused, independently useful products rather than a monolithic application.
