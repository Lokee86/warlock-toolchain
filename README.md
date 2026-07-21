# Warlock Toolchain

**Preserve the lore. Bind the doctrine. Enforce the wards.**

Warlock is a repository intelligence and governance toolchain. It externalizes documentation, architecture constraints, dependency knowledge, and working context into portable, deterministic systems that improve repository health and make agent-assisted development more reliable and reproducible.

## The toolchain

| Project | Purpose | Status |
| --- | --- | --- |
| [Demon Docs](https://github.com/Lokee86/demon-docs) | Documentation integrity, schemas, links, indexes, codemaps, and maintenance | Available |
| [ArcanaGraph](https://github.com/Lokee86/arcana-graph) | Language-independent repository relationship graph | In development |
| **Grimoire Context** | Repository context discovery, selection, packaging, and delivery | Planned |
| **Pitlord** | Polyglot architecture, dependency, and ownership enforcement | Planned |

Each component remains independently useful. Together, they are intended to share repository facts, identities, relationships, and policy so humans and software agents can work from the same durable understanding of a codebase.

## Why Warlock exists

Repositories should not depend on institutional memory or conversation history.

Important knowledge is commonly scattered across documentation, source code, conventions, issue history, and the memories of individual developers. Software agents face the same problem more sharply: useful context and constraints can disappear when a session ends, a model changes, or work moves to another environment.

Warlock moves that knowledge into repository-owned systems that can be inspected, versioned, tested, and carried between projects. Its deterministic tools do not require an LLM, while agent integrations can use the same facts and guardrails to reason and act more reliably.

## Toolchain vision

```text
Demon Docs
    preserves documented knowledge
           ↓
ArcanaGraph
    maps repository relationships
           ↓
Grimoire Context
    assembles relevant working context
           ↓
Pitlord
    enforces architectural boundaries
```

This is the intended integration direction, not a claim that every component or integration is complete today. See the [roadmap](ROADMAP.md) for current status.

## Principles

- **Deterministic first.** Core behavior should be reproducible, inspectable, and testable without an LLM.
- **Useful to humans and agents.** Repository health is not an agent-only concern.
- **Repository-owned knowledge.** Important context and constraints should travel with the project.
- **Model-independent.** No component should depend on one account, conversation, model, or provider.
- **Explicit ownership.** Tools should mutate only declared surfaces and enforce clearly defined boundaries.
- **Polyglot where practical.** Shared facts and policies should cross language and framework boundaries.
- **Composable, not monolithic.** Each tool should stand alone while supporting deeper integration with the others.

## Current status

[Demon Docs](https://github.com/Lokee86/demon-docs) is the first available Warlock component. [ArcanaGraph](https://github.com/Lokee86/arcana-graph) is under active development, beginning with language-independent graph storage and synthetic workload evaluation. Grimoire Context and Pitlord currently describe planned product boundaries.

Warlock itself is initially a documentation and coordination repository. It owns the shared vision, terminology, integration direction, and cross-tool roadmap. Implementation remains in the component repositories.

## Origins

Warlock grew out of the repository practices developed while building [Space Rocks](https://github.com/Lokee86/space-rocks) and Demon Docs. The goal is to make those practices portable and reproducible across projects without depending on access to one ChatGPT account or accumulated conversation history.

## Contributing

Changes to a component belong in that component's repository. This repository accepts improvements to the shared vision, terminology, integration contracts, and roadmap. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Warlock Toolchain documentation and other material in this repository are available under the [MIT License](LICENSE.md). Each component repository is licensed independently.
