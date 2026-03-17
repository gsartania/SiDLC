# SiDLC Framework

> **Silvia AI Software Development Life Cycle** — A multi-agent framework for building software with AI, collaboratively and iteratively.

[![Version](https://img.shields.io/badge/version-2.0-blue.svg)]()
[![License](https://img.shields.io/badge/license-MIT-green.svg)]()
[![Agents](https://img.shields.io/badge/agents-6-orange.svg)]()

---

## What Is SiDLC?

SiDLC is a **structured, phased framework** that orchestrates multiple specialized AI agents to build software from a rough idea to a tested, deployable product. It is designed for use with advanced LLMs (Claude, Gemini, GPT-4) operating as an orchestrator — particularly within the OpenClaw environment.

**Ideal for building:**
- 🔌 MCP (Model Context Protocol) servers
- ⚡ Micro-apps and CLI tools
- 🔄 Background services and scheduled tasks

---

## Core Principles

| Principle | Description |
|-----------|------------|
| 🧑‍💼 **Human-in-the-Loop** | The human is the Product Owner. Key artifacts require explicit approval before the pipeline advances. |
| 🤖 **Agent Specialization** | Each phase is handled by a dedicated agent with a narrow scope — reducing hallucination and improving quality. |
| 📄 **Artifact-Driven** | Every phase produces a concrete, verifiable deliverable. No phase starts until the previous one is validated. |

---

## The Pipeline

```
 ┌─────────────┐
 │  Human Idea  │
 └──────┬───────┘
        ▼
 Phase 0 ─── Intake Agent ────────► input/PRD.md           ⛔ HALT: Human Approves
        ▼
 Phase 1 ─── Architect Agent ─────► output/technical-design.md  ⛔ HALT: Human Approves
        ▼
 Phase 2 ─── Backend Agent ───────► Server Skeleton + DB
        ▼
 Phase 3 ─── Logic Agent ─────────► Completed output/src/ + output/tests/ ⛔ HALT: Human Reviews
        ▼
 Phase 4 ─── Integration Agent ───► output/SKILL.md + configs
        ▼
 Phase 5 ─── QA Agent ────────────► output/test-report.md         ⛔ HALT: Human Launches
```

### Phase Details

| Phase | Agent | Key Output | Human Approval? |
|-------|-------|-----------|:-:|
| 0 — Intake | Intake Agent | `input/PRD.md` | ✅ |
| 1 — Design | Architect Agent | `output/technical-design.md` | ✅ |
| 2 — Infrastructure | Backend Agent | Server code, DB, health check | — |
| 3 — Logic | Logic Agent | Business logic, tests, `output/LOGIC.md` | ✅ |
| 4 — Integration | Integration Agent | `output/SKILL.md`, `output/CHANGELOG.md` | ✅ |
| 5 — QA | QA Agent | `output/test-report.md` | ✅ |

---

## Repository Structure

```
SiDLC/
├── SiDLCFramework.md          # The master framework specification
├── ORCHESTRATOR.md            # System prompt to turn an LLM into the project manager
├── CONVENTIONS.md             # Shared coding standards for all agents
├── input/
│   └── PRD.template.md        # Reusable PRD starter template
├── output/                    # Generated artifacts go here (initially contains templates only)
│   └── context.template.json  # Shared context template (copied → context.json at project start)
│   # The following are generated at runtime by agents:
│   # ├── technical-design.md  # (Architect Agent output — Phase 1)
│   # ├── LOGIC.md             # (Logic Agent output — Phase 3)
│   # ├── SKILL.md             # (Integration Agent output — Phase 4)
│   # └── src/                 # (Backend + Logic Agent output — Phases 2–3)
└── Skills/
    ├── intake/SKILL.md        # Phase 0 — Requirements gathering
    ├── architect/SKILL.md     # Phase 1 — Technical design
    ├── backend/SKILL.md       # Phase 2 — Infrastructure setup
    ├── logic/SKILL.md         # Phase 3 — Business logic
    ├── integration/SKILL.md   # Phase 4 — Skill wiring & deployment
    └── qa/SKILL.md            # Phase 5 — Quality assurance
```

---

## Quick Start

### 1. Start with a PRD

Copy the template and fill it in (or let the Intake Agent help you):

```bash
cp input/PRD.template.md input/PRD.md
```

### 2. Run the Framework

Instruct your AI by providing it the [`ORCHESTRATOR.md`](ORCHESTRATOR.md) file. This gives the AI its system prompt to act as the project manager.

Then, issue the simple command:

> *"Start Phase 0"*

The AI Orchestrator will:
1. Check prerequisites (e.g., does `output/technical-design.md` exist for Phase 2?)
2. Read the specific specialist prompt from `Skills/<agent>/SKILL.md` for that phase.
3. Automatically manage the shared `output/context.json` file.
4. When finished, show you a summary of what was built and halt for your approval.

### 3. Approve and Continue

After reviewing the summary and the generated artifacts, you either approve and say **"Yes, start the next phase"**, or you provide feedback for the agent to fix before moving on.

---

## Error Recovery

If the QA Agent finds bugs, the framework automatically routes fixes to the right agent:

| Failure Type | Routed To |
|---|---|
| Business logic bug | Logic Agent |
| Infrastructure / DB bug | Backend Agent |
| Design flaw | Architect Agent → requires human re-approval |
| App won't start | 🚨 Escalated to human |

> **Max retries:** 2 per phase before escalating to the human.

---

## Conventions

All agents follow shared standards defined in [`CONVENTIONS.md`](CONVENTIONS.md):

- **Languages:** TypeScript (default) or Python
- **Libraries:** `better-sqlite3`, `zod`, `vitest`, `@modelcontextprotocol/sdk`
- **Code style:** No `any`, named exports, structured errors, explicit return types
- **Testing:** Unit tests required for all service functions
- **Naming:** `kebab-case` files, `PascalCase` classes, `snake_case` DB tables and MCP tools

---

## Designed For

| Environment | Why |
|------------|-----|
| **OpenClaw** | Native integration — agents are spawned as OpenClaw skills |
| **Claude / Gemini / GPT-4** | SKILL files are LLM-optimized prompts |
| **Human developers** | Clear artifacts, checkpoints, and conventions make the process auditable |

---

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/your-improvement`
3. Follow the commit conventions in `CONVENTIONS.md`
4. Submit a pull request with a clear description

---

## License

This project is open source under the [MIT License](LICENSE).

---

<p align="center">
  <em>Built for AI agents. Controlled by humans.</em>
</p>
