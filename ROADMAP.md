# Warlock Toolchain Roadmap

This roadmap separates available software, active development, and intended direction. It is not a release commitment.

## Available

### Demon Docs

Demon Docs provides deterministic documentation maintenance and repository-owned Markdown governance, including indexes, links, schemas, frontmatter, document structure, codemap management, review workflows, and watcher automation.

## In development

### ArcanaGraph

ArcanaGraph is establishing a language-independent repository graph foundation.

Current work focuses on:

- synthetic graph workloads;
- alternative graph storage models;
- deterministic graph construction and querying;
- repository-scale performance characteristics; and
- a stable fact model that other Warlock tools can consume.

Likely next work includes repository extraction adapters, normalized nodes and edges, persistence contracts, and integration boundaries for documentation and policy tools.

## Planned

### Grimoire Context

Grimoire Context will select, assemble, and deliver repository context for humans, agents, and automation. Its intended role is to turn repository facts, documentation, current work, and dependency relationships into bounded, relevant context packages.

### Pitlord

Pitlord will provide polyglot architecture and ownership enforcement. Its intended role is to evaluate repository policy against normalized paths, dependencies, symbols, and relationships rather than embedding every rule in language-specific scripts.

### Cantrip

Cantrip will provide portable, versioned, and testable skills for agents and automation. Skills should be small, reusable capabilities that can travel with a repository or be distributed independently without depending on one agent, model, account, or provider.

### Homunculus

Homunculus will create controlled repository specimens from predetermined templates and schemas. It will support deterministic injection of specified defects into functional or functionally representative repositories, preserving declared invariants such as successful builds unless a scenario explicitly breaks them. Each scenario should produce a known manifest of mutations and expected findings for tool tests, demonstrations, benchmarks, and reproducible bug reports.

### Ritual

Ritual will define deterministic execution protocols for agent-assisted work. It is not an agent orchestrator. Where Cantrip describes reusable skills, Ritual will describe the larger bounded procedure: ordered phases, permitted scope, required checkpoints, expected artifacts, verification, failure handling, and completion conditions. Its purpose is to reduce behavioral variance and make agent-driven workflows more repeatable, inspectable, and testable.

## Shared integration direction

The toolchain is expected to converge on shared concepts where doing so creates real value:

- repository discovery and ignore behavior;
- stable file and document identity;
- normalized paths, symbols, dependencies, and relationships;
- incremental fingerprints and cache identities;
- machine-readable diagnostics;
- explicit ownership and mutation boundaries;
- portable configuration conventions; and
- versioned interfaces between independently useful tools.

Shared infrastructure should be extracted only after component needs make the boundary concrete. Warlock should remain a toolchain of focused products rather than becoming a monolithic application.
