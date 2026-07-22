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

Likely next work includes repository extraction adapters, normalized nodes and edges, persistence contracts, and integration boundaries for documentation and policy tools.

### [Grimoire](docs/GRIMOIRE.md)

Grimoire is being built as a lightweight local context compiler around continuously maintained repository indexes. Its first lexical baseline now provides incremental indexing, deterministic ranking, bounded whole-chunk selection, and inspectable JSON context packages. Later stages will add structural metadata, model-aware tokenization, and optional onboard embedding retrieval.

Arcana and Demon Docs will be optional evidence providers rather than foundational dependencies. Grimoire should remain agent-independent, avoid generative models and heavy RAG infrastructure, and keep interactive context compilation within strict latency bounds by moving parsing, chunking, and repository embedding work to incremental index maintenance.

## Planned

### Pitlord

Pitlord will provide polyglot architecture and ownership enforcement. Its intended role is to evaluate repository policy against normalized paths, dependencies, symbols, and relationships rather than embedding every rule in language-specific scripts.

### Cantrip

Cantrip will provide portable, versioned, and testable skills for agents and automation. Skills should be small, reusable capabilities that can travel with a repository or be distributed independently without depending on one agent, model, account, or provider.

### [Homunculus](docs/HOMUNCULUS.md)

Homunculus will create controlled repository specimens from predetermined templates and schemas. It will support deterministic injection of specified defects into functional or functionally representative repositories, preserving declared invariants such as successful builds unless a scenario explicitly breaks them. Each scenario should produce a known manifest of mutations and expected findings for tool tests, demonstrations, benchmarks, and reproducible bug reports. Arcana may provide relationship-aware target selection and before-and-after structural verification for more plausible logical defects.

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
- normalized paths, symbols, dependencies, and relationships;
- incremental fingerprints and cache identities;
- machine-readable diagnostics;
- explicit ownership and mutation boundaries;
- portable configuration conventions; and
- versioned interfaces between independently useful tools.

Shared infrastructure should be extracted only after component needs make the boundary concrete. Warlock's runtime should remove duplicated operational work while the toolchain remains a collection of focused, independently useful products rather than a monolithic application.
