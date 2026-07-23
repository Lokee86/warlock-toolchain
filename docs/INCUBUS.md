# Incubus

Incubus is a planned deterministic runtime-adversity and edge-case exploration harness for testing live systems under hostile but reproducible conditions.

Its job is not to generate repositories or static defects. Incubus systematically torments running software with controlled pressure, disruption, malformed interactions, unusual sequencing, and failure conditions. It records what happened, preserves enough evidence to reproduce the run, and verifies how the system behaved, failed, recovered, or converged.

Cybersecurity is one application of this broader purpose, not Incubus's defining identity. Reliability, networking, distributed systems, persistence, concurrency, gameplay, and security all expose edge cases through the same underlying model: apply bounded adversity, observe the result, and compare it with declared expectations.

Space Rocks is the intended first real test target. Its multiplayer server, clients, APIs, transports, tunnels, persistence, and observability systems provide concrete requirements for the tool before it is generalized for the rest of the Warlock toolchain.

## Product definition

Incubus runs declared scenarios against one or more processes or services:

```text
scenario and seed
    ↓
start or attach to the target system
    ↓
apply scheduled adverse conditions
    ↓
observe diagnostics and externally visible behavior
    ↓
evaluate declared assertions
    ↓
emit a reproducible run manifest
```

It is best described as a deterministic adversity, edge-case exploration, or chaos harness. It may use existing load-generation, network-control, process-management, fault-injection, fuzzing, and protocol-testing facilities rather than recreating them. Incubus owns scenario coordination, repeatability, bounded exploration, evidence, and assertions.

## Initial adversity domains

Incubus scenarios may coordinate:

- network latency, loss, duplication, reordering, jitter, and partitions;
- disconnects, reconnects, and transport replacement;
- process termination, restart, crash, and delayed startup;
- slow, failed, or interrupted service dependencies;
- CPU, memory, disk, and logging pressure;
- delayed, duplicated, dropped, or burst filesystem events;
- concurrent edits and watcher storms;
- partial writes, stale reads, and interrupted persistence;
- clock, deadline, timeout, and scheduling pressure; and
- high-concurrency joins, leaves, requests, or other domain actions;
- malformed, truncated, duplicated, replayed, or semantically invalid inputs;
- unusual state-machine transitions and action sequences;
- trust-boundary, authentication, authorization, and session-handling edge cases; and
- bounded adversarial exploration of declared interfaces.

The first implementation should support only the adversity controls required by concrete Space Rocks scenarios. A universal chaos platform is not the initial goal.

## Security as an adversity profile

Security testing fits Incubus because many vulnerabilities are edge cases deliberately exercised by an adversarial actor. The security profile should use the same scenario, scope, evidence, and assertion model as every other Incubus domain rather than becoming a separate offensive architecture.

Useful security scenarios may include:

- hostile or malformed input against declared interfaces;
- authentication, authorization, and privilege-transition sequences;
- stale-session, token-replay, and message-replay behavior;
- protocol state confusion and invalid transition attempts;
- resource-exhaustion and denial-of-service resistance within explicit budgets;
- validation of containment, telemetry, rate limits, cleanup, and recovery; and
- controlled exercise of a known defect introduced by Homunculus.

Security runs must remain explicitly authorized and bounded. Scenario manifests should identify allowed targets, capabilities, resource and request limits, prohibited actions, cleanup behavior, and stop conditions. Incubus should default to local, isolated, or deliberately enrolled test environments and retain complete evidence of every action it attempted.

Incubus is not defined as a penetration-testing product, malware framework, or autonomous red-team agent. Security is one profile of controlled runtime adversity.

## Space Rocks use case

Space Rocks needs repeatable tests for failures that ordinary unit and integration tests do not represent well, including:

- packet delay, loss, duplication, reordering, and burst delivery;
- client disconnect and reconnect during an active match;
- stale snapshots and baseline or resynchronization recovery;
- WebRTC control-channel or gameplay-channel interruption;
- authoritative-server behavior while clients fall behind;
- game-server, Rails API, or player-data service restart;
- join, leave, and input storms;
- tick overruns and resource pressure;
- tunnel interruption or temporary network partitioning; and
- diagnostic continuity across failures and recovery.

A scenario could declare:

```yaml
name: reconnect-during-match
seed: 4812

setup:
  players: 12

events:
  - at: 30s
    action: add_latency
    target: player-4
    value: 80ms
  - at: 45s
    action: drop_packets
    target: player-4
    value: 15%
  - at: 60s
    action: disconnect
    target: player-4
  - at: 68s
    action: reconnect
    target: player-4
  - at: 90s
    action: restart_service
    target: player-data

assertions:
  - match_remains_authoritative
  - player_identity_is_not_duplicated
  - participant_history_is_consistent
  - client_receives_valid_baseline
  - expected_lifecycle_events_are_recorded
```

The exact schema is undecided. The important requirement is that the same scenario, seed, target build, and environment produce the same planned adversity sequence and comparable evidence.

## Determinism and evidence

Incubus cannot guarantee that every concurrent distributed system will fail in exactly the same instruction-level order. It should instead make the controlled inputs reproducible and the resulting evidence inspectable.

A run manifest should record:

- scenario version and seed;
- target builds and configuration;
- environment and relevant capabilities;
- planned and actual event timing;
- every injected condition;
- process and service lifecycle events;
- captured logs, metrics, traces, and diagnostics;
- assertion results; and
- deviations that prevented exact execution.

Where precise timing is unreliable, scenarios should prefer observable gates and state transitions over arbitrary sleeps.

## Relationship to Homunculus

Homunculus and Incubus cover different test surfaces:

```text
Homunculus
    creates or mutates controlled repository specimens

Incubus
    subjects running systems to controlled hostile conditions
```

They may be combined. Homunculus could create a repository or deployment with known structural, behavioral, or security-relevant properties, after which Incubus runs deterministic runtime-adversity scenarios against the resulting system.

For security testing, Homunculus can introduce a known source-level weakness with exact ground truth while Incubus determines whether that weakness is reachable, observable, containable, and recoverable at runtime. The pair can then benchmark analyzers, tests, observability, security controls, and review agents against the known defect and its real operational consequence.

## Relationship to Warlock

Incubus should remain independently useful outside the Warlock toolchain. When integrated with Warlock, it may:

- start, stop, or disrupt supervised component daemons;
- inject watcher and IPC failures;
- test stale-index recovery and eventual convergence;
- apply shared resource pressure;
- consume normalized health and diagnostic events; and
- verify that the daemon swarm recovers without corrupting domain state.

Warlock owns normal supervision. Incubus owns deliberate adversity during a declared test run.

## Non-goals

Incubus is not intended to become:

- a production incident-management platform;
- a permanent autonomous chaos service;
- a generic benchmark suite without failure injection;
- a repository defect generator;
- an agent orchestrator;
- an observability replacement;
- a general-purpose offensive-security framework;
- an internet target-discovery or exploitation system;
- a stealth, persistence, credential-theft, or data-exfiltration platform; or
- a reason to recreate mature operating-system and network test facilities.

## Implementation direction

The first useful Incubus version should be driven by Space Rocks scenarios rather than abstract framework design:

1. Define a small versioned scenario and run-manifest format.
2. Launch or attach to a bounded Space Rocks test environment.
3. Implement a few required network and process adversity actions using existing facilities.
4. Collect existing canonical diagnostics and observability output.
5. Evaluate explicit postconditions and recovery assertions.
6. Re-run scenarios by seed and compare evidence.
7. Generalize adapters only after a second target demonstrates a concrete shared boundary.
