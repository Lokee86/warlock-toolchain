# Possible Future Lexicon–Arcana Consolidation

## Status

This is a deferred post-0.1.0 architectural option, not an accepted redesign and not a release blocker.

The 0.1.0 suite should preserve the current working boundary:

- Lexicon is the standalone language-analysis application and normalized fact producer.
- Arcana is the standalone Rust graph engine that compiles Lexicon facts into packed snapshots and answers deep relationship queries.

The implementations remain separate unless later evidence justifies a larger change.

## Current distinction

Lexicon extracts and proves direct source-code relationships. Its language adapters resolve symbols, calls, imports, implementations, references, ownership, source spans, and certainty. It owns the immutable analysis snapshot from which those facts are published.

Arcana maps those facts into a complete repository graph. It owns packed graph storage, forward and reverse indexes, overlays, compaction, reachability, paths, transitive impact, cycles, and other graph-wide analysis.

In simplified form:

```text
source code
    -> Lexicon semantic facts
    -> Arcana graph snapshots and traversal
    -> Pitlord, Grimoire, Homunculus, and other consumers
```

The difference is therefore partly depth:

- Lexicon establishes trustworthy direct edges and their source evidence.
- Arcana connects and analyzes the full network implied by those edges.

## Why the product boundary may be too strong

The implementation boundary remains useful, but presenting Lexicon and Arcana as equally separate user-facing systems may create unnecessary complexity.

Potential costs include:

- consumers choosing between direct Lexicon queries and Arcana graph queries;
- duplicated installation, configuration, status, snapshot, and lifecycle concepts;
- coordination of schema and snapshot versions across two public interfaces;
- repeated explanations of where simple edge joins end and graph traversal begins;
- consumers integrating both products when they conceptually need one source-analysis service.

This becomes more visible as Lexicon adapters add richer relationships such as dataflow, state access, interface dispatch, callbacks, events, and serialization paths. The adapters must discover those relationships, while Arcana must represent and traverse them. Both systems evolve together even though their technical ownership remains distinct.

## Possible target structure

A likely future shape is to make Lexicon the primary product and consumer interface while retaining Arcana as its optimized graph backend:

```text
Lexicon
|-- language adapters
|-- normalized fact schema
|-- content-addressed analysis snapshots
|-- direct fact and source-location queries
`-- Arcana graph backend
    |-- packed graph storage
    |-- graph snapshots and overlays
    |-- reachability and path queries
    |-- transitive impact and dependency analysis
    `-- graph-wide operational summaries
```

Under this model:

- ordinary consumers integrate with Lexicon;
- Lexicon delegates deep graph queries to Arcana when available;
- Arcana remains a separate Rust process, executable, and testable component;
- specialized graph consumers may still use Arcana directly;
- Lexicon can remain useful without materializing an Arcana graph when only direct facts are required.

This would consolidate the product surface without forcing a codebase or implementation-language merger.

## Non-goals

A future review should not assume any of the following:

- rewriting Arcana in Go;
- moving language adapters into Arcana;
- duplicating graph traversal inside Lexicon;
- removing Arcana's standalone CLI, protocol, benchmarks, or storage format;
- requiring every Lexicon scan to build an Arcana graph;
- changing the current boundary before the 0.1.0 suite is released.

## Decision criteria

Revisit this only after real cross-tool use provides evidence. The decision should consider:

1. how many consumers genuinely need both direct facts and deep graph queries;
2. whether separate public interfaces cause duplicated orchestration or configuration;
3. whether the Lexicon-to-Arcana snapshot contract remains stable in practice;
4. whether hiding Arcana behind Lexicon would reduce useful independent operation;
5. packaging, startup, update, and failure-isolation costs;
6. performance differences between direct fact queries and graph-backed queries;
7. the needs of Pitlord, Grimoire, Homunculus, and Demon Docs after integration.

## Possible outcomes

The future review may choose any of three outcomes:

1. Keep both products and public interfaces separate.
2. Make Lexicon the primary public interface while retaining Arcana as a separate backend and advanced interface.
3. Merge the products more deeply only if the first two structures prove inadequate.

The second option currently appears most plausible, but it is not yet a decision.

## 0.1.0 position

Do not restructure the suite for this possibility before 0.1.0.

The existing separation has produced a working semantic-analysis application, a working packed graph engine, and a stable integration path between them. Releasing and exercising that boundary will provide better evidence than redesigning it immediately before the first suite release.
