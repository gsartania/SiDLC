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
Phase 1: technical-design.md  ─────────────────────► HALT: Human Approves Design
     │
     ▼
Phase 2: Baseline Server Code (runs, connects to DB)
     │
     ▼
Phase 3: Completed src/ (all PRD requirements met)  ► HALT: Human Reviews Logic
     │
     ▼
Phase 4: SKILL.md + deployment configs
     │
     ▼
Phase 5: test-report.md + Final Approval  ──────────► HALT: Human Launches
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
- **Output:** `technical-design.md`
- **⛔ HALT:** Human must approve `technical-design.md` before proceeding.

### Phase 2: Core Infrastructure
- **Actor:** Backend Agent
- **Input:** `technical-design.md`, `CONVENTIONS.md`
- **Tasks:**
  1. Initialize the project repository (e.g., `package.json`, `tsconfig.json`).
  2. Create `.env.example` with all required environment variables documented.
  3. Implement the database connector and basic CRUD operations.
  4. Construct the server entry point (e.g., MCP server instance).
  5. Implement a `ping` / `get_status` health check tool.
- **Output:** Functional baseline server code (runs without crashing, connects to DB, health check passes).

### Phase 3: Business Logic & Intelligence
- **Actor:** Logic Agent
- **Input:** `input/PRD.md`, `CONVENTIONS.md`, Phase 2 Codebase.
- **Tasks:**
  1. Implement specific business rules (e.g., scheduling logic, filtering).
  2. Implement AI parsing algorithms or advanced data transformations.
  3. Wire the business logic into the API/MCP interfaces created in Phase 2.
  4. Write unit tests for all service functions.
  5. Handle all error states defined in `technical-design.md`.
- **Output:** Completed `src/` directory + `tests/` + `LOGIC.md` (design rationale).
- **⛔ HALT:** Human reviews the completed logic and signs off before integration.

### Phase 4: Integration & Skill Wiring
- **Actor:** Integration Agent
- **Input:** Completed Codebase, OpenClaw environment constraints.
- **Tasks:**
  1. Write the `SKILL.md` file instructing the primary AI assistant how to use the new software.
  2. Register the service/MCP server within the orchestrator environment.
  3. Configure any required cron jobs, heartbeat integrations, or environment variables.
  4. Verify the MCP server or API is reachable end-to-end.
  5. Create or update `CHANGELOG.md`.
- **Output:** `SKILL.md`, deployment scripts/configs, `CHANGELOG.md`.

### Phase 5: Quality Assurance & Launch
- **Actor:** QA Agent
- **Input:** Running software, `SKILL.md`, `input/PRD.md`, `technical-design.md`.
- **Tasks:**
  1. Test the health check tool first.
  2. Execute end-to-end user stories defined in the PRD.
  3. Verify data persistence and error handling (including all error states from the catalog).
  4. Test edge cases: invalid inputs, missing files, empty states.
  5. Rate all bugs found by severity: `P0 (blocker) / P1 (major) / P2 (minor)`.
  6. Present final `test-report.md` to the human.
- **Output:** `test-report.md`, final deployment approval.
- **⛔ HALT:** Human reviews the test report and approves the launch.

---

## Execution Instructions for AI Orchestrator

When instructed to execute the SiDLC Framework:
1. Spawn the **Intake Agent** (Phase 0) to produce an approved `input/PRD.md`.
2. Spawn the **Architect Agent** (Phase 1). Halt and require human approval of `technical-design.md`.
3. Spawn the **Backend Agent** (Phase 2).
4. Spawn the **Logic Agent** (Phase 3). Halt and require human review of logic output.
5. Spawn the **Integration Agent** (Phase 4).
6. Spawn the **QA Agent** (Phase 5). Halt and require human launch approval.
7. Do not proceed to a subsequent phase until the Output artifact of the current phase is validated.

---

## Error Recovery Loop

If the **QA Agent rejects** the build, follow this protocol:

| Failure Type | Action |
|---|---|
| Logic/business rule bug | Re-spawn **Logic Agent** with QA report as input |
| Infrastructure/DB bug | Re-spawn **Backend Agent** with QA report as input |
| Design flaw (wrong schema/interface) | Re-spawn **Architect Agent**; require human re-approval of `technical-design.md` |
| Application won't start | **Escalate to human immediately** — do not attempt to auto-fix |

> **Max retry limit:** 2 loops per phase. If a bug persists after 2 attempts, escalate to the human and halt.

---

## Required Reading Per Agent

All agents must read `CONVENTIONS.md` before beginning their phase. This file defines shared coding standards, preferred libraries, naming conventions, and error handling patterns.
