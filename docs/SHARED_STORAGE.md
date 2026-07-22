# Shared Storage

Warlock may consolidate compatible tool state into one repository-local object database, but consolidation is optional and configured independently for every tool.

The goal is operational simplicity. A repository using several Warlock tools should not be forced to carry a separate embedded object repository for each tool unless the user wants that isolation.

## Core rule

> Tools own their data. Users choose where that data is stored. Warlock coordinates only the stores explicitly assigned to it.

Installing Warlock must not automatically absorb, relocate, delete, or rewrite an existing Demon Docs, Grimoire, Arcana, Pitlord, or future tool store.

## Storage modes

Every compatible tool should support three storage modes:

- `standalone` — use the tool's normal repository-local store, such as `.ddocs/` or `.grimoire/`;
- `warlock` — use the shared Warlock object database under a tool-specific reference namespace; and
- `custom` — use an explicitly configured external or alternate path.

A repository may mix these modes freely.

```toml
[storage]
default = "standalone"

[tools.grimoire]
storage = "warlock"

[tools.demon_docs]
storage = "standalone"

[tools.arcana]
storage = "custom"
path = "D:/repository-state/arcana"
```

The repository-wide default is only a convenience. An explicit per-tool setting always wins.

## Warlock-managed layout

A shared store should remain a private object repository owned operationally by Warlock:

```text
.warlock/
├── config.toml
└── storage/
    ├── objects/
    └── refs/
```

Tools publish state through namespaced refs rather than Git tags:

```text
refs/warlock/grimoire/state
refs/warlock/demon-docs/state
refs/warlock/arcana/state
refs/warlock/pitlord/state
```

Additional refs remain inside the owning tool's namespace.

The shared object database may deduplicate identical immutable objects, but it does not create a shared schema. Grimoire records remain Grimoire records; Demon Docs records remain Demon Docs records; Arcana retains its own representation and may use shared storage only if its format and access model make that appropriate.

## Ownership boundaries

When a tool uses `warlock` storage:

Warlock owns:

- the physical object repository;
- cross-process write coordination;
- reference publication primitives;
- repository health checks;
- safe compaction, packing, pruning, and recovery;
- storage discovery; and
- migration orchestration.

The tool owns:

- record and index formats;
- reference names within its namespace;
- schema and format versions;
- migrations between its own versions;
- validation and corruption semantics;
- domain-specific reads and writes; and
- decisions about which objects are reachable from its refs.

Warlock must not interpret or mutate a tool's domain data except through that tool's declared storage contract.

## Installation and migration

Warlock installation should detect existing standalone stores and report them as migration candidates. It must not migrate them automatically.

A consolidation workflow should:

1. show every detected tool store and its current mode;
2. let the user select tools individually;
3. copy reachable objects into the shared object database;
4. recreate the tool's refs inside its Warlock namespace;
5. verify object integrity and tool-level readability;
6. update configuration only after verification succeeds; and
7. leave or remove the old standalone store according to an explicit user choice.

Failure must leave the original store usable and authoritative.

## Exemptions and reversibility

Any tool may remain exempt indefinitely. Warlock must continue to supervise or integrate with a tool whose state remains standalone or custom, provided the tool exposes the required runtime contracts.

Moving from `warlock` back to `standalone` or `custom` should also be supported as an explicit export operation. Storage consolidation is a deployment choice, not a permanent format conversion or product dependency.

## Standalone behavior

Every tool must remain independently runnable without Warlock. Its standalone defaults should continue to work without a `.warlock/` directory or Warlock process.

When Warlock is absent, a tool configured for standalone or custom storage behaves normally. A tool explicitly configured for Warlock-managed storage should fail clearly rather than silently creating a second standalone store.

## Configuration precedence

The intended resolution order is:

1. explicit command-line storage arguments;
2. explicit per-tool repository configuration;
3. repository-wide Warlock storage default;
4. the tool's standalone default.

Environment-level overrides may be added later, but they must not make storage selection ambiguous or silently move state.

## Initial adoption

Demon Docs and Grimoire are the first practical candidates because both use go-git-backed private object repositories. Their logical formats should remain separate while Warlock supplies a shared object database and namespaced publication surface.

Arcana should not be forced into this model. Its custom packed graph representation exists for workload-specific reasons, so integration should depend on demonstrated compatibility rather than toolchain uniformity.
