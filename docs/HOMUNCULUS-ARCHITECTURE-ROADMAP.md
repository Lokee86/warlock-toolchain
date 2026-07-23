# Homunculus architecture mutation roadmap

Homunculus should grow from its first call-chain bypass into a deterministic architecture-degradation engine. It should create realistic ownership and dependency defects through narrow, reproducible transformations rather than arbitrary source generation.

The detailed recipe definitions live in the Homunculus repository at `docs/architecture-mutation-roadmap.md`. This document records the Warlock-level direction and tool boundaries.

## Direction

Lexicon identifies semantic possibilities in a repository. A language adapter filters those possibilities to candidates that match a supported transformation. Homunculus applies one candidate in an isolated worktree, verifies the result, and records the exact expected graph delta. Pitlord then evaluates the resulting architecture.

Candidate selection may be explicit or seeded-random from the adapter-approved set. The repository commit, fact snapshot, adapter version, candidate population, selection mode, and random seed must make every experiment reproducible.

## Mutation families

- single and partial layer/boundary bypasses — implemented for conservative Go, Python, GDScript, and JavaScript/TypeScript forwarding wrappers;
- generated forwarding packages for configurable call and dependency depth — next;
- relocation of low-coupling functions into architecturally incorrect packages;
- responsibility duplication with only some callers redirected;
- interface-to-concrete coupling;
- controlled encapsulation breaches;
- rerouting through existing compatible components;
- architecture-level cycles that remain legal to the implementation language; and
- gradual responsibility accumulation into god packages.

## Deep nesting

Homunculus should support two forms of depth.

**Controlled synthetic depth** inserts mechanically transparent forwarding stages. It provides exact ground truth for maximum-depth rules, long-path queries, transitive impact, redundant layers, diagnostic quality, and graph-performance testing.

**Organic dependency depth** reroutes work through existing compatible components. It better resembles real architectural decay, but requires stronger proof that inserted stages do not change observable behavior through validation, state changes, logging, locking, or other side effects.

Both are useful. Synthetic depth is precise and scalable; organic depth is more realistic.

## Composition

Complex defects should be built by composing independently verified mutations. A specimen might duplicate a responsibility into the wrong package, redirect one caller to it, insert forwarding layers, and then couple that path to a concrete implementation.

Each step must confirm its own source edit, compiler result, behavioral checks, and Lexicon graph delta before the next step is applied. Failed composition discards the experiment or returns to the last verified state.

## Recipe contract

Every mutation recipe must declare:

- required Lexicon nodes and relationships;
- language-specific eligibility rules;
- allowed file edits and created files;
- exact added, removed, and relocated graph relationships;
- compiler, test, and differential verification requirements;
- expected Pitlord diagnostic class;
- explicit rejection reasons; and
- whether the recipe can compose with other mutations.

The principle remains: a narrow recipe that safely applies to a diverse set of real targets is more valuable than a broad rewrite that cannot preserve reliable ground truth.

## Tool boundaries

Lexicon owns normalized language facts and candidate evidence. Homunculus owns source transformation, isolation, verification, mutation composition, and the defect manifest. Pitlord owns architectural policy and diagnosis. Incubus owns runtime adversity and exploitability exercises; Homunculus does not probe remote systems or exercise attacks.
