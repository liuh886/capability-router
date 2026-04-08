# Capability Router

[![Agentic Engineering](https://img.shields.io/badge/Paradigm-Agentic_Engineering-blue.svg)](#)
[![Phase First](https://img.shields.io/badge/Mode-Phase_First-success.svg)](#)
[![Cross Platform](https://img.shields.io/badge/Platform-Agent_Environments-lightgrey.svg)](#)

**Capability Router** is a phase-first orchestration layer for AI agents.

It is designed for work that is bigger than one-shot skill lookup:

- multi-step
- stateful across turns
- cross-agent or delegation-friendly
- likely to pass through intake, design, execution, and evaluation

Instead of only finding a relevant skill, it determines the **current phase**, chooses the best **role lens**, attaches the best **matching skills**, and coordinates updates to shared project state files.

---

## What Makes It Different

Traditional skill discovery answers:

- "what skill might help here?"

Capability Router answers:

- "what phase is this work in?"
- "what role should drive this phase?"
- "what skills should be attached to that role?"
- "what project files must be updated?"
- "is there a conflict that must be shown to the user first?"

That is the actual gap between native skill finding and project-scale orchestration.

## The Lifecycle Model

Capability Router is built around 4 phases:

1. `Intake`
- Clarify scope
- Ask questions
- Decide whether work should enter design

2. `Design`
- Define or revise architecture, interfaces, constraints, and acceptance criteria

3. `Execution`
- Implement approved work
- Track progress
- Record implementation-side design deltas

4. `Evaluation`
- Review, audit, QA, benchmark, and verify
- Record acceptance outcomes and regressions

The router's primary job is to determine the current phase. Role and skill choice come after that.

## Shared Project State

Capability Router treats these files as long-lived project state:

- `DESIGN.md`
- `TASKS.md`
- `EVALUATE.md`

These files are not disposable outputs. They are the shared memory layer between:

- the main agent
- delegated agents
- later turns in the same project

### State File Rules

1. Never rewrite these files wholesale.
2. Prefer append-only updates.
3. Only change a conflicting field after explicit user confirmation.
4. If a file is missing, create it with the protocol header.
5. If a request conflicts with existing content, surface the conflict first.

Suggested header:

```md
> Capability Router Protocol
> This file is a long-lived project state file.
> Do not rewrite this file wholesale.
> Only append new entries or edit explicitly conflicting fields after user confirmation.
> If a request conflicts with existing content, surface the conflict first.
```

## Output Contract

Capability Router should return an execution plan, not just a recommendation list.

Example:

```json
{
  "query": "Design the routing architecture and update the task plan",
  "phase": "Design",
  "phase_confidence": 0.98,
  "recommended_role": {
    "name": "system-architect",
    "path": "~/.codex/.../system-architect.md",
    "family": "engineering",
    "confidence": 0.91,
    "why": "Best matched for the Design phase."
  },
  "recommended_skills": [
    {
      "name": "software-architecture",
      "path": "~/.codex/skills/software-architecture",
      "confidence": 0.94,
      "why": "Strong architecture fit for the current phase."
    }
  ],
  "project_state": {
    "DESIGN.md": {
      "path": "/repo/DESIGN.md",
      "exists": true,
      "protocol_header_present": true,
      "line_count": 42
    },
    "TASKS.md": {
      "path": "/repo/TASKS.md",
      "exists": true,
      "protocol_header_present": true,
      "line_count": 38
    },
    "EVALUATE.md": {
      "path": "/repo/EVALUATE.md",
      "exists": false,
      "protocol_header_present": false,
      "line_count": 0
    }
  },
  "conflicts": [],
  "file_update_plan": [
    {
      "file": "DESIGN.md",
      "action": "append",
      "section": "Decision Log",
      "reason": "Record the new design decision without rewriting prior content."
    },
    {
      "file": "TASKS.md",
      "action": "append",
      "section": "Planned Tasks",
      "reason": "Translate approved design deltas into executable tasks."
    }
  ],
  "needs_user_confirmation": false,
  "next_phase": "Execution"
}
```

## Why Use It

Use Capability Router when you need more than native skill discovery:

- you want phase-aware routing
- you maintain a large local skill library
- you use role libraries such as `agency-agents`
- you want role plus skill pairing instead of skill-only lookup
- you need a shared protocol for `DESIGN.md`, `TASKS.md`, and `EVALUATE.md`

Do not force it onto trivial tasks. Simple work may still be better served by direct execution or native skill matching.

## The Seed

This repository ships a seed prompt:

- [capability-router-seed.md](./capability-router-seed.md)

The seed is not meant to glorify bootstrap automation. Its real purpose is to transfer the router's design philosophy into another agent environment:

- phase-first reasoning
- role plus skill pairing
- shared project-state governance
- conflict-aware file maintenance

## Current Design Standard

If your router still only returns:

- detected phase
- primary role
- top skills

then it is still a first-generation recommender.

A second-generation Capability Router must also handle:

- `Intake`
- state file inspection
- conflict detection
- incremental file update planning
- next-phase handoff

That is the design standard this repository is aiming for.
