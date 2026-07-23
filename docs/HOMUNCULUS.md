# Homunculus

**Status:** In development

Homunculus is a deterministic source-mutation system for creating controlled repository defects with known ground truth. Its current implementation introduces architecture defects into real repositories so architecture-analysis and enforcement tools can be tested precisely.

Its primary requirement is unusual:

> Damage the internal architecture while keeping the program observably functional under its verification suite.

Generated repositories remain useful as internal smoke fixtures, but real-repository mutation is the main product.

## Ownership

Homunculus owns the experiment:

- isolate a specific repository commit in a detached worktree;
- apply one declared source transformation;
- verify pre-edit hashes and permitted paths;
- run builds and tests;
- reject unexpected changes;
- remove failed or disposable experiments; and
- record exact evidence.

Lexicon supplies normalized semantic facts and source spans. Language adapters perform syntax-aware source transformations. Pitlord evaluates whether the resulting architecture is allowed.

## Current mutation types

### Static dependency injection

The original Go and Python adapters add an otherwise unused import relationship. This is retained as a parser and policy smoke primitive, but it does not meaningfully alter control flow.

### Real call-chain bypass

The first semantic recipe rewrites:

```text
caller -> middle wrapper -> target
```

into:

```text
caller -----------------> target
```

The caller's existing source is changed to invoke the target directly. The Go adapter repairs imports, receiver expressions, argument substitution, and argument ordering, then runs `go test ./...` for the complete owning module.

This produces a genuine new direct call relationship and removes reliance on the intermediary at that call site.

## Lexicon-driven corpus

Homunculus consumes Lexicon's definite `calls` edges, symbol ownership, and source spans. It excludes `possible-calls` and constructs deterministic three-stage call chains without searching for named functions.

The first Space Rocks game-server scan produced:

```text
10,734 definite three-stage call chains
82 structurally eligible v1 bypasses
```

The corpus is sorted and divided into development, validation, and holdout sets. The benchmark selects distinct production chains from every split and avoids reusing the same middle symbol. Holdout chains are not used to shape individual rewrite examples.

## Go bypass v1

The first operator accepts conservative forwarding wrappers such as:

```go
func Forward(first, second Value) Result {
    return target.Apply(second, first)
}
```

A caller expression such as:

```go
middle.Forward(left, right)
```

can become:

```go
target.Apply(right, left)
```

Current support includes:

- package-function forwarding;
- method calls rooted in a forwarded parameter;
- middle-receiver substitution;
- imported downstream packages;
- direct parameter substitution; and
- reordered arguments.

The operator rejects wrappers that compute arguments, use local variables, branch, lock, log, convert results, adapt errors, forward variadic arguments, or otherwise require unsupported behavior to be reproduced.

A structurally eligible candidate can still fail compilation or tests. That is recorded as a failed experiment rather than concealed by selecting a different chain.

## Security mutation direction

Cybersecurity is a natural future mutation profile for Homunculus, but it does not redefine the product as an offensive-security tool. The same controlled transformation model can introduce known security-relevant defects into owned repositories so analyzers, fuzzers, tests, review agents, and defensive controls can be measured against exact ground truth.

Potential recipes include:

- removing or bypassing an authorization check;
- weakening validation, sanitization, or encoding at a declared boundary;
- introducing an unsafe file, path, parser, serialization, or logging pattern;
- exposing a secret through an existing diagnostic path;
- weakening a cryptographic or transport configuration;
- introducing unsafe shared state or a resource-exhaustion path; and
- changing dependency or call structure so a trusted boundary is bypassed.

Every recipe must remain syntax-aware, narrowly declared, reversible through worktree disposal, and verified against explicit build and test requirements. Homunculus creates the controlled source condition; it does not probe remote targets or exercise the runtime consequence. Incubus owns that runtime-adversity surface.

## Safety model

Every experiment:

1. resolves a specific Git commit;
2. creates a detached worktree;
3. verifies edited-file SHA-256 preimages;
4. applies only declared edits;
5. runs verification commands directly without a shell;
6. rejects unplanned changed files;
7. removes failed experiments; and
8. writes `.homunculus/architecture-mutation.json` after success.

If the source checkout contains uncommitted work, Homunculus records that fact but mutates only the selected committed revision. The source checkout is never modified.

Passing verification proves the selected checks passed. It is not mathematical proof that all possible behavior is identical.

## First real Space Rocks bypass

The first validation mutation rewrote this real chain:

```text
cmd/game-server.runWithContext
    -> networking.NewRoomManager
    -> rooms.NewRoomManager
```

into a direct call from `runWithContext` to `rooms.NewRoomManager`. The complete game-server `go test ./...` suite passed. The transformation was derived from Lexicon spans and the generic forwarding rule, not from hard-coded function names.

## Benchmark workflow

```text
Lexicon facts
    ↓
Deterministic A -> B -> C corpus
    ↓
Structural eligibility filter
    ↓
Development / validation / holdout selection
    ↓
Fresh detached worktree per chain
    ↓
Generic source rewrite
    ↓
Full module test suite
    ↓
Pass/fail evidence and worktree cleanup
```

The benchmark command can run multiple distinct chains per split and records each chain, mutation, base commit, file hashes, and verification output.

The first six-chain run passed four mutations and failed two because the proposed rewrites crossed a package boundary through an unexported field. That failure exposed a missing general eligibility rule. After adding cross-package visibility validation, the candidate set narrowed from 94 to 82. The final benchmark passed all six selected mutations: two development, two validation, and two holdout chains, each originating in a different caller file.

This validates Homunculus's rewrite, isolation, and build/test behavior. Pitlord has not yet been run against these mutations.

## Current boundaries

Homunculus currently mutates architecture only, and architecture remains the immediate implementation priority. Future deterministic mutation profiles may include security-relevant source defects and other narrowly defined defect classes when they can preserve the same ground-truth, isolation, and verification guarantees.

Runtime adversity and exploitability exercises belong to Incubus. Homunculus owns the source transformation and resulting defect manifest, not remote probing or live-system attack behavior.

The current semantic recipe supports Go. The corpus, benchmark runner, and experiment contracts are language-neutral and receive syntax behavior through an adapter; additional languages require their own bypass adapters.

The next integration is to rescan each successful mutation with Lexicon and require Pitlord to report the exact known direct relationship without unrelated diagnostics.
