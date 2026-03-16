# Silvia AI Software Development Life Cycle (SiDLC) Framework

> **Version:** 2.0
> **Last Updated:** 2026-03-16

## Purpose
This document defines the SiDLC (Silvia Software Development Life Cycle), a multi-agent, collaborative framework designed to build software applications (particularly MCP servers, micro-apps, and background services) iteratively. It is optimized for consumption by advanced LLMs (Claude, Gemini, GPT-4) operating as an orchestrator or within an OpenClaw environment.

## Core Philosophy
1. **Human-in-the-Loop (HITL):** The human acts as the Product Owner and final approval authority.
2. **Agent Specialization:** Tasks are broken down and delegated to specialized sub-agents with narrow scopes to reduce hallucination and improve code quality.
3. **Artifact-Driven:** Each phase produces a concrete, verifiable artifact (e.g., Schema, Code, SKILL.md) before moving to the next.

---

## Artifact Flow

```
[Human Idea]
     │
     ▼
Phase 0: input/PRD.md  ───────────────────────────────► HALT: Human Approves PRD
     │
     ▼
Phase 1: output/technical-design.md  ──────────────► HALT: Human Approves Design
     │
     ▼
Phase 2: Baseline Server Code (runs, connects to DB)
     │
     ▼
Phase 3: Completed output/src/ (all PRD requirements met)  ► HALT: Human Reviews Logic
     │
     ▼
Phase 4: SKILL.md + deployment configs
     │
     ▼
Phase 5: output/test-report.md + Final Approval  ──────────► HALT: Human Launches
```

---

## Framework Phases

### Phase 0: Requirements Intake
- **Actor:** Intake Agent
- **Input:** Human's raw idea or rough description.
- **Tasks:**
  1. Interview the human using a structured question set.
  2. Populate `input/PRD.template.md` collaboratively.
  3. Flag ambiguities, scope creep, and external API dependencies.
- **Output:** `input/PRD.md` (human-approved)
- **⛔ HALT:** Human must explicitly approve `input/PRD.md` before proceeding.

### Phase 1: Technical Design & Schema
- **Actor:** Architect Agent
- **Input:** `input/PRD.md` (approved), `CONVENTIONS.md`, human constraints.
- **Tasks:**
  1. Define database schema (e.g., SQLite tables).
  2. Design API or MCP Tool Interfaces (JSON schemas for inputs/outputs).
  3. Define an **error state catalog** — all errors each tool can return.
  4. Map out the internal architecture and data flow.
- **Output:** `output/technical-design.md`
- **⛔ HALT:** Human must approve `output/technical-design.md` before proceeding.

### Phase 2: Core Infrastructure
- **Actor:** Backend Agent
- **Input:** `output/technical-design.md`, `CONVENTIONS.md`
- **Tasks:**
  1. Initialize the project repository (e.g., `output/package.json`, `output/tsconfig.json`).
  2. Create `output/.env.example` with all required environment variables documented.
  3. Implement the database connector and basic CRUD operations.
  4. Construct the server entry point (e.g., MCP server instance).
  5. Implement a `ping` / `get_status` health check tool.
- **Output:** Functional baseline server code (runs without crashing, connects to DB, health check passes).
- **Self-Verification:** The Backend Agent must start the server and confirm the health check responds before handoff.

### Phase 3: Business Logic & Intelligence
- **Actor:** Logic Agent
- **Input:** `input/PRD.md`, `CONVENTIONS.md`, Phase 2 Codebase.
- **Tasks:**
  1. Implement specific business rules (e.g., scheduling logic, filtering).
  2. Implement AI parsing algorithms or advanced data transformations.
  3. Wire the business logic into the API/MCP interfaces created in Phase 2.
  4. Write unit tests for all service functions.
  5. Handle all error states defined in `output/technical-design.md`.
- **Output:** Completed `output/src/` directory + `output/tests/` + `output/LOGIC.md` (design rationale).
- **Self-Verification:** The Logic Agent must run all tests and confirm they pass before handoff.
- **⛔ HALT:** Human reviews the completed logic and signs off before integration.

### Phase 4: Integration & Skill Wiring
- **Actor:** Integration Agent
- **Input:** Completed Codebase, OpenClaw environment constraints.
- **Tasks:**
  1. Write the `SKILL.md` file instructing the primary AI assistant how to use the new software.
  2. Register the service/MCP server within the orchestrator environment.
  3. Configure any required cron jobs, heartbeat integrations, or environment variables.
  4. Verify the MCP server or API is reachable end-to-end.
  5. Create or update `output/CHANGELOG.md`.
- **Output:** `output/SKILL.md`, deployment scripts/configs, `output/CHANGELOG.md`.
- **Self-Verification:** The Integration Agent must verify end-to-end reachability before handoff.

### Phase 5: Quality Assurance & Launch
- **Actor:** QA Agent
- **Input:** Running software, `SKILL.md`, `input/PRD.md`, `output/technical-design.md`.
- **Tasks:**
  1. Test the health check tool first.
  2. Execute end-to-end user stories defined in the PRD.
  3. Verify data persistence and error handling (including all error states from the catalog).
  4. Test edge cases: invalid inputs, missing files, empty states.
  5. Rate all bugs found by severity: `P0 (blocker) / P1 (major) / P2 (minor)`.
  6. Present final `output/test-report.md` to the human.
- **Output:** `output/test-report.md`, final deployment approval.
- **⛔ HALT:** Human reviews the test report and approves the launch.

---

## Orchestrator Commands & Execution

The SiDLC Framework is designed to be executed via simple commands from the human to the orchestrator (e.g., "Start Phase 0"). 

When the human issues a **`Start Phase N`** command, the orchestrator must follow this exact sequence:

1. **Check Prerequisites:** Verify the required input artifacts for that phase exist.
2. **Execute:** Spawn the specific agent persona and execute the tasks in its `SKILL.md`.
3. **Summarize & Halt:** When the agent finishes, display a summary of what was accomplished, present the key output artifact(s), and ask the human: *"Phase N is complete. Shall I proceed to the next phase?"*

### Phase-Specific Prerequisites

| Command | Prerequisite Checks Before Running |
|---------|------------------------------------|
| **`Start Phase 0`** | None. (Checks if `input/PRD.template.md` exists). |
| **`Start Phase 1`** | `input/PRD.md` must exist and be human-approved. |
| **`Start Phase 2`** | `output/technical-design.md` must exist and be human-approved. |
| **`Start Phase 3`** | `output/src/` directory and basic server skeleton must exist. |
| **`Start Phase 4`** | `output/src/` directory and `output/tests/` directory must be populated. `output/LOGIC.md` must exist. |
| **`Start Phase 5`** | `output/SKILL.md` must exist. Application must be runnable. |

If a prerequisite fails, the orchestrator must **abort** and tell the human which previous phase needs to be completed first.

---

## Error Recovery Loop

If the **QA Agent rejects** the build, follow this protocol:

| Failure Type | Action |
|---|---|
| Logic/business rule bug | Re-spawn **Logic Agent** with QA report as input |
| Infrastructure/DB bug | Re-spawn **Backend Agent** with QA report as input |
| Design flaw (wrong schema/interface) | Re-spawn **Architect Agent**; require human re-approval of `output/technical-design.md` |
| Application won't start | **Escalate to human immediately** — do not attempt to auto-fix |

> **Max retry limit:** 2 loops per phase. If a bug persists after 2 attempts, escalate to the human and halt.

### Intermediate Recovery

Error recovery is not limited to Phase 5. If **any agent** produces output that fails self-verification (e.g., server won't start, tests fail), the orchestrator should:
1. Re-attempt the current phase once with the error output as context.
2. If the second attempt fails, escalate to the human before proceeding.

---

## Agent Budget & Escalation

To prevent agents from looping indefinitely on difficult sub-tasks:

- **Attempt limit:** If an agent has made **5 or more failed attempts** at a single sub-task (e.g., fixing a compilation error, resolving a test failure), it must **halt and escalate to the human** with a clear summary of what was tried and what failed.
- **Scope guard:** If an agent discovers that completing its phase requires changes outside its defined scope (e.g., the Logic Agent needs to change the database schema), it must **not make those changes**. Instead, document the issue and escalate to the orchestrator for re-routing.

---

## Required Reading Per Agent

All agents must read `CONVENTIONS.md` before beginning their phase. This file defines shared coding standards, preferred libraries, naming conventions, and error handling patterns.

---

## Agent Context Passing

A shared context file (`output/context.json`) enables downstream agents to understand decisions, assumptions, and flags raised by upstream agents. This creates a persistent memory across the pipeline.

### How It Works

1. At the start of a project, the orchestrator copies `output/context.template.json` → `output/context.json`.
2. Each agent **reads** the full `output/context.json` before starting its phase to understand prior decisions.
3. Each agent **appends** to its own phase section upon completion — recording decisions made, assumptions taken, and any flags for downstream agents.
4. Agents must **never modify** another agent's phase section.

### What Each Agent Records

Each phase section contains:

| Field | Type | Description |
|-------|------|-------------|
| `status` | string | `"pending"`, `"in_progress"`, `"completed"`, or `"failed"` |
| `completed_at` | string \| null | ISO 8601 timestamp when the phase finished |
| `decisions` | array of strings | Key choices made (e.g., `"Used SQLite WAL mode for concurrent reads"`) |
| `assumptions` | array of strings | Things assumed without explicit confirmation (e.g., `"Default timezone is UTC"`) |
| `flags` | array of strings | Warnings or notes for downstream agents (e.g., `"The tasks table has no soft-delete — QA should test permanent deletion"`) |
| `notes` | string | Free-form context for the next agent |

### Example

```json
{
  "project_name": "SilviaTasks",
  "created_at": "2026-03-16T10:00:00Z",
  "last_updated": "2026-03-16T14:30:00Z",
  "phases": {
    "phase_0_intake": {
      "status": "completed",
      "completed_at": "2026-03-16T10:15:00Z",
      "decisions": [
        "Target user is a single power-user, not a team",
        "Offline-first, no cloud sync in v1"
      ],
      "assumptions": [
        "User is on macOS"
      ],
      "flags": [
        "The user mentioned 'recurring tasks' but this was moved to Out of Scope"
      ],
      "notes": "PRD approved without changes on first pass."
    },
    "phase_1_architect": {
      "status": "completed",
      "completed_at": "2026-03-16T11:00:00Z",
      "decisions": [
        "Used SQLite with WAL mode for concurrent reads",
        "Designed 3 MCP tools: create_task, list_tasks, complete_task"
      ],
      "assumptions": [
        "Max 10,000 tasks per database — no pagination needed in v1"
      ],
      "flags": [
        "The tasks table uses hard deletes — QA should verify no orphaned references"
      ],
      "notes": ""
    }
  }
}
```

