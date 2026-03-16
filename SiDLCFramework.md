# Silvia AI Software Development Life Cycle (SiDLC) Framework

## Purpose
This document defines the SiDLC (Silvia Software Development Life Cycle), a multi-agent, collaborative framework designed to build software applications (particularly MCP servers, micro-apps, and background services) iteratively. It is optimized for consumption by advanced LLMs (Claude, Gemini, GPT-4) operating as an orchestrator or within an OpenClaw environment.

## Core Philosophy
1. **Human-in-the-Loop (HITL):** The human acts as the Product Owner and final approval authority.
2. **Agent Specialization:** Tasks are broken down and delegated to specialized sub-agents with narrow scopes to reduce hallucination and improve code quality.
3. **Artifact-Driven:** Each phase produces a concrete, verifiable artifact (e.g., Schema, Code, SKILL.md) before moving to the next.

## Framework Phases

### Phase 1: Technical Design & Schema
- **Actor:** Architect Agent
- **Input:** `PRD.md` (Product Requirements Document), human constraints.
- **Tasks:**
  1. Define database schema (e.g., SQLite tables).
  2. Design API or MCP Tool Interfaces (JSON schemas for inputs/outputs).
  3. Map out the internal architecture and data flow.
- **Output:** `technical-design.md`

### Phase 2: Core Infrastructure
- **Actor:** Backend Agent
- **Input:** `technical-design.md`
- **Tasks:**
  1. Initialize the project repository (e.g., `package.json`, `tsconfig.json`).
  2. Implement the database connector and basic CRUD operations.
  3. Construct the server entry point (e.g., MCP server instance).
- **Output:** Functional baseline server code (runs without crashing, connects to DB).

### Phase 3: Business Logic & Intelligence
- **Actor:** Logic Agent
- **Input:** `PRD.md`, Phase 2 Codebase.
- **Tasks:**
  1. Implement specific business rules (e.g., scheduling logic, filtering).
  2. Implement AI parsing algorithms or advanced data transformations.
  3. Wire the business logic into the API/MCP interfaces created in Phase 2.
- **Output:** Completed source code (`src/` directory) fulfilling all PRD functional requirements.

### Phase 4: Integration & Skill Wiring
- **Actor:** Integration Agent
- **Input:** Completed Codebase, OpenClaw environment constraints.
- **Tasks:**
  1. Write the `SKILL.md` file instructing the primary AI assistant how to use the new software.
  2. Register the service/MCP server within the orchestrator environment.
  3. Configure any required cron jobs, heartbeat integrations, or environment variables.
- **Output:** `SKILL.md`, deployment scripts/configs.

### Phase 5: Quality Assurance & Launch
- **Actor:** QA Agent
- **Input:** Running software, `SKILL.md`, `PRD.md`.
- **Tasks:**
  1. Execute end-to-end user stories defined in the PRD.
  2. Verify data persistence and error handling.
  3. Present final build to the human for review.
- **Output:** Test logs, final deployment approval.

## Execution Instructions for AI Orchestrator
When instructed to execute the SiDLC Framework:
1. Ensure a `PRD.md` exists and is approved by the user.
2. Spawn the **Architect Agent** (via sub-agent or self-prompting with the Architect SKILL) to complete Phase 1.
3. Halt and require human approval of the `technical-design.md`.
4. Proceed sequentially through Phases 2-5, spawning the respective specialized agent for each phase.
5. Do not proceed to a subsequent phase until the Output artifact of the current phase is validated.
