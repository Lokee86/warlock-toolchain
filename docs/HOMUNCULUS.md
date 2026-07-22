# Homunculus

**Status:** In development

Homunculus creates controlled architecture defects in real repositories so architecture-analysis tools can be tested against known ground truth.

Its current scope is intentionally narrow: add one direct dependency between two architectural areas while keeping the original checkout untouched and proving that the mutated checkout still passes its required verification.

## Current purpose

Homunculus answers a specific testing question:

> Can a tool detect a known architecture violation inserted into real, otherwise working code?

The current workflow is:

```text
Real Git repository
        ↓
Detached worktree at a known commit
        ↓
Language adapter creates one dependency edit
        ↓
Build or tests verify the repository still works
        ↓
Evidence manifest records the exact mutation
```

Generated repositories remain useful as internal smoke fixtures, but they are not the primary Homunculus testing strategy.

## Current command

```text
homunculus architecture \
  --repo PATH \
  --output PATH \
  --language go|python \
  --request FILE \
  [--base REV]
```

The request identifies:

- the source file;
- the source architecture area;
- the target dependency;
- the target architecture area; and
- any additional verification commands.

The first implementation requires explicit targets. Lexicon-backed candidate selection can be added later without changing the mutation engine.

## Language-neutral core

Homunculus itself does not edit Go or Python syntax.

A mutation adapter receives the detached repository and returns a complete plan containing:

- exact replacement contents for each edited file;
- the required pre-edit SHA-256 hash;
- the normalized relationship expected to be added; and
- verification commands.

The core engine then handles isolation, application, verification, cleanup, and evidence. Future language adapters use the same contract.

## Current adapters

### Go

The Go adapter adds a blank direct import to an existing source file. It locates the nearest owning `go.mod` and runs:

```text
go test ./...
```

from that module.

### Python

The Python adapter appends a never-called function containing the requested import. This creates a real static import relationship without importing the target during normal execution.

It performs a Python AST syntax check and then runs any tests declared by the request.

## Safety model

Homunculus currently guarantees:

- the source checkout is never modified;
- every mutation is applied to a detached Git worktree;
- the exact base commit is recorded;
- stale edit plans are rejected using pre-edit file hashes;
- edited paths cannot escape the worktree;
- verification commands run directly without a command shell;
- unexpected changed files cause failure;
- normal failed experiments are restored and removed; and
- successful experiments receive a machine-readable evidence manifest.

If the source checkout contains uncommitted work, Homunculus records that fact but mutates only the selected committed revision. It never copies or changes those uncommitted files.

Passing verification proves that the selected checks passed. It is not mathematical proof that all runtime behavior is identical. The request should therefore use the strongest practical test suite for the affected repository area.

## Evidence manifest

A successful run writes:

```text
.homunculus/architecture-mutation.json
```

The manifest records:

- mutation ID and language;
- source repository and detached worktree;
- base commit;
- whether the source checkout was dirty;
- changed files;
- before and after SHA-256 hashes;
- expected added architecture relationships; and
- verification commands and output.

## Space Rocks validation

The architecture mutation engine has been exercised against real Space Rocks code in both supported languages.

### Go experiment

Homunculus added a direct dependency from:

```text
game.physics → game.damage
```

by editing:

```text
services/game-server/internal/game/physics/vector.go
```

The full game-server `go test ./...` suite passed after the mutation.

### Python experiment

Homunculus added a direct dependency from:

```text
data-sync.model → data-sync.generators
```

by editing:

```text
tools/data_sync/data_sync/model/constants.py
```

The syntax check passed, and the data-sync test suite reported:

```text
289 passed
```

Both experiments used detached worktrees from the same recorded Space Rocks commit. The original Space Rocks checkout remained unchanged.

## Current non-goals

Homunculus does not yet introduce:

- general logic bugs;
- performance defects;
- security defects;
- partial migrations;
- documentation drift;
- arbitrary code corruption; or
- random mutations.

Those can be considered later. The current product boundary is safe architecture dependency mutation.

## Next integration

The next useful layer is Lexicon-backed target selection and verification:

1. Lexicon identifies source files and candidate target modules.
2. Homunculus applies the selected adapter plan safely.
3. Lexicon analyzes the mutated worktree.
4. The observed relationship is compared with the manifest expectation.
5. Pitlord must report the known architecture violation.

This keeps ownership clear:

- Lexicon understands source relationships;
- language mutation adapters understand syntax edits;
- Homunculus owns the controlled experiment;
- Pitlord judges whether the architecture is allowed.
