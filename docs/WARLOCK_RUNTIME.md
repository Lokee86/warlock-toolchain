# Warlock Runtime

Warlock is both the umbrella project for the toolchain and the planned shared runtime for its daemon swarm.

Demon Docs, Arcana, Grimoire, Pitlord, and future components remain independently useful products. When multiple components are installed, Warlock should coordinate their shared operational needs without absorbing their product ownership.

## Purpose

Without coordination, every background tool is likely to implement its own:

- repository discovery;
- filesystem watcher;
- Git and configuration change detection;
- process lifecycle;
- heartbeat and health reporting;
- logging and status commands;
- cache invalidation signals; and
- local IPC conventions.

That would multiply idle resource use and create conflicting or redundant repository work. Warlock should provide one practical runtime through which independently owned services can cooperate.

## Planned responsibilities

Warlock should eventually own:

- installed-component discovery;
- daemon startup, shutdown, restart, and supervision;
- one normalized repository change-event stream;
- shared Git, configuration, and ignore-rule change notifications;
- service health and capability discovery;
- common status and diagnostic reporting;
- bounded resource and lifecycle policy;
- stable local IPC conventions; and
- shared caches only where multiple concrete consumers justify them.

Warlock may summon and supervise services, but each service remains responsible for its own domain state and behavior.

## Runtime shape

```text
Warlock
├── repository discovery
├── shared watcher and change events
├── process supervision
├── health and status
├── local IPC
│
├── Demon Docs service
├── Arcana service
├── Grimoire service
├── Pitlord service
└── other installed services
```

A file change should be observed once and published as a normalized event. Each interested tool then performs only its domain-specific incremental work.

For example:

```text
source file changed
    ↓
Warlock publishes normalized change event
    ├── Arcana updates affected relationships
    ├── Grimoire updates affected retrieval chunks and embeddings
    ├── Pitlord re-evaluates affected policy
    └── Demon Docs updates only relevant documentation state
```

## Independence requirement

Warlock must be an integration advantage, not an installation requirement for every component.

Each component should retain a standalone mode that can:

- discover a repository;
- perform its core commands;
- maintain its own minimal required state; and
- expose its own diagnostics.

When Warlock is available, the component may delegate duplicate runtime concerns to it. A missing or stopped Warlock runtime must not make the component's core product unusable.

## Ownership boundaries

Warlock owns coordination, not domain intelligence.

- Demon Docs owns documentation integrity and maintenance.
- Arcana owns repository entities and relationships.
- Grimoire owns context retrieval, ranking, budgeting, and packaging.
- Pitlord owns architecture and ownership policy evaluation.
- Cantrip owns portable skills.
- Ritual owns deterministic execution protocols.
- Homunculus owns controlled repository specimens and static defect scenarios.
- Incubus owns declared runtime adversity and recovery scenarios.

Warlock should not merge these stores or APIs into one universal internal model. Shared contracts should be introduced only where multiple real tools need the same fact or event.

## Performance principle

The runtime exists partly to keep the toolchain fast and practical.

Shared watching and incremental events should allow tools to maintain prepared state continuously, so interactive commands perform bounded queries rather than repository-wide work. Background coordination should remain cheap enough to leave enabled during normal development.

The operating principle is:

> One repository observation, multiple focused reactions.

## Current status

The runtime is planned. The current Warlock repository owns the shared vision, terminology, integration boundaries, and roadmap. Runtime implementation should begin only when the first concrete cross-tool daemon requirements are ready to be expressed as stable contracts.
