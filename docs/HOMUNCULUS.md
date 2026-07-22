# Homunculus

**Status:** Planned concept

Homunculus is a deterministic repository specimen builder and defect-injection tool. It creates or modifies functional, or functionally representative, repositories according to explicit scenario definitions so repository tooling can be tested against known structures, defects, and expected outcomes.

The core idea is not random corruption. Homunculus should produce controlled repository states with plausible causes and consequences.

## Purpose

Homunculus should make it possible to create reproducible repositories for:

- integration and regression testing;
- documentation-tool evaluation;
- architecture and dependency analysis;
- agent and workflow evaluation;
- demonstrations and tutorials;
- benchmark construction; and
- reproducible bug reports.

Every specimen should have a known answer key: what was created or changed, which invariants were preserved, what defects exist, and what participating tools are expected to detect.

## Core model

```text
Known repository or maintained template
              ↓
      Homunculus scenario
              ↓
Controlled repository specimen
              ↓
Mutation manifest + expected findings
```

A scenario should describe intent semantically where practical rather than merely naming arbitrary text edits.

Examples include:

- partially migrate an API contract while leaving one consumer stale;
- rename a symbol across only part of its dependent graph;
- introduce a dependency cycle between valid packages;
- bypass an intended architecture boundary;
- update implementation behavior without updating related documentation;
- move a file while leaving selected references or ownership metadata stale; and
- invalidate the wrong portion of a cache dependency chain.

Low-level corruptions may still be useful for parser and resilience testing, but graph-informed logical defects should be the primary value.

## Operating modes

### Build a specimen

Create a predetermined repository from a maintained template and scenario.

```text
homunculus build scenarios/go-service-decay.yaml
```

The result should be reproducible from the same template version and scenario definition.

### Corrupt a repository

Apply declared defects to a copy of an existing repository.

```text
homunculus corrupt ./fixture-repo scenarios/partial-migration.yaml
```

The tool should refuse destructive ambiguity. A scenario must identify valid targets or define deterministic selection rules.

## Scenario shape

The exact schema remains undecided, but a scenario will likely need to express:

- base template or input repository;
- template and schema versions;
- required repository facts;
- declared invariants;
- ordered mutations;
- permitted files or graph regions;
- expected build and test state;
- expected structural graph delta;
- expected diagnostics; and
- rollback or clean regeneration behavior.

Illustrative example:

```yaml
scenario: stale-service-migration
base:
  template: go-http-service-v1

preserve:
  build: true
  formatting: true

mutations:
  - type: incomplete_contract_migration
    contract: UserCreatedEvent
    update:
      producer: true
      consumers: all_except_one
      documentation: false

expect:
  stale_consumers: 1
  stale_documents: 1
  failing_tests: 1
```

This is a concept sketch, not a committed schema.

## Arcana integration

Arcana could make Homunculus substantially more precise.

Before mutation, Arcana could provide:

- package and module relationships;
- symbol definitions and references;
- producer and consumer relationships;
- documentation-to-code relationships;
- ownership and architecture boundaries; and
- candidate mutation targets satisfying scenario constraints.

After mutation, Arcana could verify the intended structural result:

```yaml
expected_graph_delta:
  added_edges: 1
  removed_edges: 2
  new_cycles: 1
  orphaned_nodes: 1
```

A graph-informed workflow would be:

```text
Functional repository
        ↓
Arcana maps relationships
        ↓
Homunculus selects a valid defect pattern
        ↓
Homunculus applies deterministic mutations
        ↓
Arcana verifies the structural delta
        ↓
Build, tests, and diagnostics verify the scenario
```

Arcana integration should be optional. Homunculus should still support simple file-level scenarios independently, while using Arcana for semantic defects and relationship-aware target selection.

## Invariants and verification

Scenarios should explicitly state what remains valid and what is intentionally broken.

Examples:

```yaml
preserve:
  - compilation
  - formatting
  - repository cleanliness

break:
  - documentation accuracy
  - architecture policy
```

Homunculus should verify these claims rather than assuming that a mutation had the intended effect. A scenario that requests one broken consumer but produces three should fail generation.

Possible verification layers include:

- source and file assertions;
- Arcana before-and-after comparison;
- build status;
- selected test results;
- Demon Docs diagnostics;
- Pitlord policy results; and
- scenario-specific assertions.

## Outputs

Each run should emit a machine-readable manifest containing at least:

- scenario identity and version;
- input repository or template identity;
- mutations applied;
- changed files;
- deliberately untouched related files;
- preserved invariants;
- intended defects;
- expected findings; and
- verification results.

The generated repository and manifest together form the specimen.

## Initial scope

A useful first prototype should remain narrow:

1. Start from one maintained, functional repository template.
2. Support a small declarative scenario format.
3. Implement several deterministic file- and relationship-level defects.
4. Emit a complete mutation manifest.
5. Verify build or test expectations.
6. Add Arcana-backed target selection once its graph contracts are stable enough.

The first milestone should prove that Homunculus can create the same functional specimen with the same known defects repeatedly. Broad language support and arbitrary repository mutation can come later.

## Non-goals

Homunculus is not intended to be:

- a random source-code generator;
- a fuzzing replacement;
- a universal program synthesizer;
- a general-purpose code translator;
- an autonomous bug-writing agent; or
- a guarantee that every requested semantic defect can be introduced safely.

## Open questions

- Should templates live with Homunculus, in separate repositories, or both?
- How should scenarios identify semantic targets before Arcana integration exists?
- Which invariants must be verified by Homunculus itself versus delegated tools?
- Should corruption always operate on disposable copies or support explicit in-place mode?
- How should scenario and template versions compose into a stable specimen identity?
- What is the smallest useful defect vocabulary for the first prototype?
