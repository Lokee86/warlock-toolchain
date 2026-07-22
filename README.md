# Warlock Toolchain

**Preserve the lore. Bind the doctrine. Enforce the wards.**

Warlock is a repository intelligence and governance toolchain. It externalizes documentation, architecture constraints, dependency knowledge, and working context into portable, deterministic systems that improve repository health and make agent-assisted development more reliable and reproducible.

## The toolchain

| Project | Purpose | Status |
| --- | --- | --- |
| [Demon Docs](https://github.com/Lokee86/demon-docs) | Documentation integrity, schemas, links, indexes, codemaps, and maintenance | Available |
| [Arcana](https://github.com/Lokee86/arcana-graph) | Language-independent repository relationship graph | In development |
| [**Grimoire**](docs/GRIMOIRE.md) | Lightweight indexed retrieval and token-bounded repository context compilation | In development |
| **Pitlord** | Polyglot architecture, dependency, and ownership enforcement | Planned |
| **Cantrip** | Portable, versioned, and testable skills for agents and automation | Planned |
| **Homunculus** | Schema-driven functional repository specimens and deterministic defect injection | Planned |
| [**Incubus**](docs/INCUBUS.md) | Deterministic runtime adversity, disruption, and recovery testing | Planned |
| **Ritual** | Deterministic execution protocols for agent-assisted work | Planned |

Each component remains independently useful. Together, they are intended to share repository facts, identities, relationships, and policy so humans and software agents can work from the same durable understanding of a codebase. [Warlock itself](docs/WARLOCK_RUNTIME.md) is also intended to become the shared runtime and supervisor for the toolchain's daemon swarm.

## Why Warlock exists

Repositories should not depend on institutional memory or conversation history.

Important knowledge is commonly scattered across documentation, source code, conventions, issue history, and the memories of individual developers. Software agents face the same problem more sharply: useful context and constraints can disappear when a session ends, a model changes, or work moves to another environment.

Warlock moves that knowledge into repository-owned systems that can be inspected, versioned, tested, and carried between projects. Its deterministic tools do not require an LLM, while agent integrations can use the same facts and guardrails to reason and act more reliably.

## Toolchain vision

```text
Warlock runtime
    discovers, supervises, and feeds the daemon swarm
    │
    ├── Demon Docs preserves documented knowledge
    ├── Arcana maps repository relationships
    ├── Grimoire compiles fast, bounded working context
    └── Pitlord enforces architectural boundaries

Cantrip
    supplies reusable skills
           ↓
Ritual
    makes agent execution bounded and repeatable

Homunculus
    creates controlled repository specimens for testing the toolchain

Incubus
    applies reproducible adversity to running systems
```

Grimoire's base retrieval engine does not require Arcana or Demon Docs; both are optional evidence providers. Every component remains independently runnable, while the planned Warlock runtime will provide shared watching, lifecycle, events, and IPC when multiple daemons are installed.

This is the intended integration direction, not a claim that every component or integration is complete today. See the [roadmap](ROADMAP.md), [Grimoire design](docs/GRIMOIRE.md), [Incubus design](docs/INCUBUS.md), [runtime design](docs/WARLOCK_RUNTIME.md), and [shared-storage policy](docs/SHARED_STORAGE.md) for current status.

## Principles

- **Deterministic first.** Core behavior should be stable, inspectable, and testable without a generative LLM; exact reproducibility may be traded at the margins for practical latency.
- **Useful to humans and agents.** Repository health is not an agent-only concern.
- **Repository-owned knowledge.** Important context and constraints should travel with the project.
- **Agent and provider independent.** No component should depend on one account, conversation, generative model, agent, or hosted provider.
- **Explicit ownership.** Tools should mutate only declared surfaces and enforce clearly defined boundaries.
- **Polyglot where practical.** Shared facts and policies should cross language and framework boundaries.
- **Composable, not monolithic.** Each tool should stand alone while supporting deeper integration with the others.

## Current status

[Demon Docs](https://github.com/Lokee86/demon-docs) is the first available Warlock component. [Arcana](https://github.com/Lokee86/arcana-graph) is under active development, beginning with language-independent graph storage and synthetic workload evaluation. Grimoire is now also in development with an incremental lexical index, deterministic ranking, bounded whole-chunk selection, and inspectable context packages. Pitlord, Cantrip, Homunculus, Incubus, and Ritual currently describe planned product boundaries.

Warlock itself currently serves as the documentation and coordination repository. It owns the shared vision, terminology, integration direction, and cross-tool roadmap. It is also planned to become the shared daemon runtime and supervisor, while domain implementation remains in the independently useful component repositories.

## Origins

Warlock grew out of the repository practices developed while building [Space Rocks](https://github.com/Lokee86/space-rocks) and Demon Docs. The goal is to make those practices portable and reproducible across projects without depending on access to one ChatGPT account or accumulated conversation history.

## Contributing

Changes to a component belong in that component's repository. This repository accepts improvements to the shared vision, terminology, integration contracts, and roadmap. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Warlock Toolchain documentation and other material in this repository are available under the [Apache License 2.0](LICENSE). Each component repository is licensed independently.
