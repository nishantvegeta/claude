# DDD Knowledge Graph Compiler

Architectural compiler for transforming Feature Requirements (FRS) into a Domain-Driven Design knowledge graph with artifact generation.

## Overview

This system compiles immutable FRS documents into a structured knowledge graph containing:
- **Actors (ACT-)** — System users and roles
- **Entities (ENT-)** — Domain objects
- **Commands (CMD-)** — Actions and operations
- **Flows (FLOW-)** — Business processes
- **States (STATE-)** — State machines
- **Decisions (DEC-)** — Business rules

And generates artifacts:
- Feature Specs (FEAT-)
- GitLab Issues
- Test Plans (TPLAN-)
- Test Runs (TRUN-)
- API Documentation (APIDOC-)
- Topology diagrams
- Changelogs

## Architecture

```
FRS (immutable) → INGEST → STAGING → ABSORB → DDD Nodes → SYNTHESIZE → Artifacts
                                              ↓
                                         home.md (knowledge graph index)
```

## Quick Start

1. **Boot** — Initialize session and load snapshot
   ```
   /compiler boot
   ```

2. **Ingest** — Fetch FRS from GitLab
   ```
   /compiler ingest <issue-id>
   ```

3. **Absorb** — Extract DDD nodes from staged FRS
   ```
   /compiler absorb <frs-id>
   ```

4. **Compile** — Create Feature Spec
   ```
   /compiler compile <module>
   ```

5. **Issue** — Generate GitLab issue body
   ```
   /compiler issue <feat-id>
   ```

## Commands

| Command | Description |
|---------|-------------|
| `boot` | Session initialization |
| `ingest <id>` | Fetch FRS from GitLab |
| `absorb <id>` | Extract DDD nodes |
| `compile <mod>` | Generate Feature Spec |
| `issue <id>` | Create GitLab issue |
| `implement <id>` | Mark feature implemented |
| `supersede <old> <new>` | Replace Feature Spec |
| `query <q>` | Architecture queries |
| `generate testplan <feat>` | Create test plan |
| `generate testrun <plan>` | Create test run |
| `generate apidoc <ver>` | Generate API docs |
| `generate topology <mod>` | Generate topology |
| `generate changelog <M>` | Generate changelog |
| `lint` | 28-class debt audit |
| `milestone close <M>` | 6-gate closure |

## Role Boundaries

- **Business Analyst (BA)** — Write FRS, resolve CNF, approve FEAT
- **Backend Architect** — INGEST, COMPILE, QUERY, LINT, ISSUE
- **Developer** — Generate test evidence, submit DFB

## Key Concepts

- **Immutable sources** — `/raw_sources/` is read-only
- **CNF nodes** — Blocking conflicts requiring BA resolution
- **DFB nodes** — Developer feedback requiring BA review
- **TRUN sign-off** — Required before IMPLEMENT
- **6-gate milestone** — Closure validation

## Documentation

- [SKILL.md](.claude/skills/compiler/SKILL.md) — Core skill documentation
- [OPERATIONS.md](.claude/skills/compiler/OPERATIONS.md) — Command index
- [node-definitions/](.claude/skills/compiler/node-definitions/) — Node templates
- [operations/](.claude/skills/compiler/operations/) — Detailed procedures

## Project Context

- **GitLab:** https://gitlab.local:8080/root/artifacts
- **Architecture:** ABP Framework with Domain-Driven Design
- **Primary Module:** Task Management
