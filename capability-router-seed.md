---
name: capability-router-seed
description: Build a phase-first Capability Router that determines the current project phase, recommends the best role and matching skills, and incrementally maintains DESIGN.md, TASKS.md, and EVALUATE.md.
---

# Capability Router Seed v2

You are an AI Agent. Your task is to build a **Capability Router** for your own environment.

This router is **not** just a better skill finder. It is a **phase-first orchestration layer** for multi-step work. It must:

- detect the current project phase
- recommend the best role lens for that phase
- recommend the smallest useful set of matching skills
- inspect and maintain shared project state files
- surface conflicts before downstream execution

The router should outperform native skill discovery on complex work by adding **phase selection**, **role + skill pairing**, and **project-state governance**.

## Core Design Philosophy

The router exists for tasks that are:

- multi-step
- stateful across turns
- cross-agent or delegation-friendly
- likely to touch design, execution, and evaluation over time

The router is **not mandatory for every prompt**. For trivial or single-shot tasks, native skill discovery or direct execution may still be better.

## Lifecycle Model

The router MUST be phase-first.

Supported phases:

1. `Intake`
Purpose:
- Clarify user intent
- Narrow scope
- Resolve uncertainty
- Decide whether work should move into design

2. `Design`
Purpose:
- Define or revise architecture, constraints, interfaces, decisions, and acceptance criteria

3. `Execution`
Purpose:
- Implement approved work
- Update task progress
- Record implementation-specific design deltas

4. `Evaluation`
Purpose:
- Review, audit, QA, benchmark, or verify work
- Record acceptance outcomes and follow-up actions

## Shared Project State Files

The router must treat these files as long-lived project state:

- `DESIGN.md`
- `TASKS.md`
- `EVALUATE.md`

These are **state files**, not phase-local outputs.

Rules:

1. Never rewrite these files wholesale.
2. Prefer append-only updates.
3. Only edit a specific conflicting field when the user has explicitly confirmed the change.
4. If a file does not exist, create it with the protocol header below.
5. If the current request conflicts with existing file content, surface the conflict before changing anything.

Required protocol header:

```md
> Capability Router Protocol
> This file is a long-lived project state file.
> Do not rewrite this file wholesale.
> Only append new entries or edit explicitly conflicting fields after user confirmation.
> If a request conflicts with existing content, surface the conflict first.
```

## Routing Model

The router must choose:

1. the current `phase`
2. the best `recommended_role` for that phase
3. the best 1-3 `recommended_skills`
4. the `file_update_plan`
5. whether `needs_user_confirmation`
6. the `next_phase`

The primary object is the **phase**, not the role.

The role is an execution lens.

The skills are execution attachments for the current phase.

## Scoring Principles

Do not hardcode your entire philosophy into fixed numeric weights. Small weights are fine, but the important thing is the structure:

- phase affinity matters more than generic keyword overlap
- direct skills should beat wrappers and meta-skills
- architecture tasks should strongly prefer architecture roles and skills
- evaluation tasks should strongly prefer QA, review, audit, and verification roles and skills
- broad wrapper skills and self-referential router skills should be penalized
- noisy domains such as sales, marketing, or brand should be penalized unless the query clearly asks for them

The tokenizer should support:

- English / code tokens via regex
- CJK tokens in a way that does not destroy recall

## Metadata Requirements

When indexing roles and skills, extract more than plain keywords.

Role metadata should include:

- `kind`
- `scope`
- `phase_affinity`

Skill metadata should include:

- `kind`
- `scope`
- `phase_affinity`
- `generic_wrapper`
- `activation_kind`

The router must use these metadata fields in scoring.

## Required Output Schema

The router MUST return JSON shaped like this:

```json
{
  "query": "...",
  "phase": "Design",
  "phase_confidence": 0.88,
  "recommended_role": {
    "name": "system-architect",
    "path": "...",
    "family": "engineering",
    "confidence": 0.91,
    "why": "Best matched for the Design phase."
  },
  "recommended_skills": [
    {
      "name": "software-architecture",
      "path": "...",
      "confidence": 0.94,
      "why": "Strong architecture fit for the current phase."
    }
  ],
  "project_state": {
    "DESIGN.md": {
      "path": "...",
      "exists": true,
      "protocol_header_present": true,
      "line_count": 42
    },
    "TASKS.md": {
      "path": "...",
      "exists": true,
      "protocol_header_present": true,
      "line_count": 38
    },
    "EVALUATE.md": {
      "path": "...",
      "exists": false,
      "protocol_header_present": false,
      "line_count": 0
    }
  },
  "conflicts": [
    {
      "file": "DESIGN.md",
      "type": "soft_conflict",
      "field": "existing_constraints",
      "summary": "The request changes a previously approved constraint."
    }
  ],
  "file_update_plan": [
    {
      "file": "DESIGN.md",
      "action": "append",
      "section": "Decision Log",
      "reason": "Record the new design decision without rewriting prior content."
    }
  ],
  "needs_user_confirmation": true,
  "next_phase": "Design"
}
```

## Discovery and Integration

Discover only what is actually useful in the current environment.

- Index the local role sources you truly use
- Index the local skill sources you truly use
- Do not assume every possible tool root is meaningful
- Prefer maintainable local integration over universal self-installation theater

The seed may create:

- the router script
- role and skill indexers
- metadata files
- a local skill wrapper
- tests

But the goal is the router design, not the bootstrap spectacle.

## Implementation Requirements

Build:

- `capability_router.py`
- role indexer
- skill indexer
- tests that cover:
  - phase detection
  - role and skill metadata extraction
  - state file creation plan
  - blocking rewrite conflict detection
  - soft conflict detection
  - wrapper/meta-skill de-prioritization

## Final Standard

The router is successful if it is:

- better than native skill discovery on multi-phase work
- explicit about why it chose a phase, role, and skill set
- safe around existing project state files
- useful for both the main agent and delegated agents

Implementation may begin now.
