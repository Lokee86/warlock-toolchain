# Lexicon

Lexicon is the shared language-analysis subsystem for the Warlock toolchain.

It owns one reusable adapter per programming language and a common normalized schema for the symbols and relationships those adapters discover. Warlock tools consume Lexicon rather than duplicating language parsing and semantic analysis in every repository.

## Responsibility

A Lexicon adapter translates source code in one programming language into normalized repository facts.

Typical facts include:

- modules, packages, namespaces, files, and directories;
- types, functions, methods, fields, variables, and tests;
- imports, definitions, references, calls, implementations, inheritance, and containment;
- source locations and stable identities;
- unresolved or ambiguous relationships where the language prevents a definite answer.

The adapter understands the source language generally. It does not contain a predefined list of symbols for individual repositories; it discovers each repository's symbols by parsing and analyzing that repository.

## Ownership boundary

Lexicon owns:

- language-specific parsing and semantic analysis;
- one comprehensive adapter per supported language;
- the normalized cross-language fact schema;
- adapter capability and schema versioning;
- deterministic extraction output.

Lexicon does not own:

- Arcana's graph storage or graph-query engine;
- Pitlord's architecture and ownership policies;
- Grimoire's retrieval and context-selection logic;
- Demon Docs' documentation rules and codemap behavior;
- Homunculus mutation strategy;
- Warlock daemon supervision.

## Consumers

Lexicon is a shared dependency where language understanding is required:

- Arcana consumes the complete fact stream to build its repository graph.
- Pitlord consumes packages, dependencies, ownership, symbols, and source locations to enforce architecture policy.
- Grimoire consumes symbols and relationships to improve repository context retrieval.
- Demon Docs consumes definitions and relationships for code-aware documentation features.
- Homunculus consumes semantic structure to select plausible mutation targets.

Each consumer uses only the facts it needs. Consumers should not maintain thinner duplicate adapters.

## Independence and dependencies

Warlock tools remain separate products, but separate products do not need to be dependency-free.

Where a tool requires language analysis, depending on Lexicon is preferable to reproducing the same parser, type-resolution, and relationship-extraction work in every repository. A tool may still have useful modes that do not require Lexicon, but language-aware features may require it explicitly.

## Adapter implementation

Adapters may be implemented in the language best suited to analyze their target language. For example, the Go adapter may be written in Go so it can use Go's parser, type checker, package loader, SSA, and related analysis libraries.

Adapters should remain independently executable components behind a versioned, language-neutral contract. This avoids requiring every consumer to embed every adapter implementation language.

## Initial implementation

The existing Arcana Go adapter is the starting point for Lexicon's Go adapter. It already performs repository-wide Go analysis using AST extraction, type information, SSA, and variable-type analysis.

The first extraction work should:

1. separate the adapter's normalized language facts from Arcana-specific naming and storage assumptions;
2. establish the Lexicon schema and adapter version contract;
3. preserve Arcana consumption through a compatibility boundary;
4. make the same Go facts directly consumable by Pitlord and other Warlock tools;
5. move adapter ownership into a dedicated Lexicon project once the boundary is stable.

The initial goal is not to build every language adapter. It is to establish the shared contract with Go and prove it against the needs of Arcana and Pitlord before adding other languages.

## Initial consumer requirements

The first schema must support the facts already produced by the Go adapter:

- repository, directory, file, package/module, import, type, function, method, variable, and test nodes;
- contains, defines, imports, calls, possible-calls, converts-to, implements, extends, and references relationships;
- source spans on definitions and relationships;
- stable identities and file content identities;
- unresolved references with reason and candidate information;
- adapter capabilities and schema version.

The first consumers need different subsets of that same output:

- Arcana needs the complete fact stream.
- Pitlord initially needs packages/modules, files, containment, imports, definitions, implementation/inheritance relationships, calls or references used by architecture rules, and source spans for violations.
- Grimoire needs symbol definitions, containment, references, calls, and source spans for structural context retrieval.
- Demon Docs needs definitions, dependencies, references, and source spans for code-aware codemaps and documentation evidence.

## Language priority for current projects

1. Go: already substantially implemented and immediately useful to Arcana, Grimoire, Demon Docs, and Pitlord.
2. Rust: required to analyze Arcana itself.
3. GDScript: required for the Space Rocks client and can replace Demon Docs' current lightweight GDScript symbol extraction.
4. Ruby: required for the Space Rocks Rails service.
5. TypeScript: required for the Space Rocks web code and other TypeScript repositories.

This order can change when a consumer creates a stronger immediate requirement, but Go is the contract-defining adapter.
