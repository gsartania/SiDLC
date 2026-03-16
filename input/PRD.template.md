# Product Requirements Document (PRD)

> **Project:** [Project Name]
> **Version:** 1.0
> **Date:** [YYYY-MM-DD]
> **Author:** [Human / Intake Agent]
> **Status:** Draft | Approved

---

## 1. Problem Statement

> Describe the core problem being solved. Who experiences this pain? Why does it matter?

[Describe the problem here]

**Target Users:**
- [User type 1 — e.g., "A busy professional who tracks tasks manually"]
- [User type 2]

---

## 2. Goals

> What must this software accomplish to be considered a success?

- [ ] Goal 1 (e.g., "Users can create and manage tasks via natural language")
- [ ] Goal 2
- [ ] Goal 3

## 2.1 Non-Goals

> Explicitly list what this project will NOT do to prevent scope creep.

- [Non-goal 1 — e.g., "Will not support multi-user collaboration in v1"]
- [Non-goal 2]

---

## 3. User Stories

> Use the format: **As a [user], I want to [action], so that [benefit].**
> Each story must have acceptance criteria.

### Story 1 — [Short Title]
**As a** [user type], **I want to** [do something], **so that** [I benefit].

**Acceptance Criteria:**
- [ ] Given [context], when [action], then [expected result]
- [ ] Given [context], when [action], then [expected result]

### Story 2 — [Short Title]
**As a** [user type], **I want to** [do something], **so that** [I benefit].

**Acceptance Criteria:**
- [ ] Given [context], when [action], then [expected result]

### Story 3 — [Short Title]
**As a** [user type], **I want to** [do something], **so that** [I benefit].

**Acceptance Criteria:**
- [ ] Given [context], when [action], then [expected result]

---

## 4. Functional Requirements

> List what the system must do. Reference user stories where applicable.

| ID | Requirement | Priority | Story Ref |
|----|-------------|----------|-----------|
| FR-01 | [Description] | Must Have | Story 1 |
| FR-02 | [Description] | Should Have | Story 2 |
| FR-03 | [Description] | Nice to Have | — |

---

## 5. Technical Constraints

> Hard rules the Architect Agent must respect.

- **Runtime:** [e.g., Node.js 20+ / Python 3.11+]
- **Storage:** [e.g., SQLite (local-first), no cloud dependency]
- **Interface:** [e.g., MCP server / REST API / CLI]
- **Platform:** [e.g., macOS, cross-platform, server-only]
- **Authentication:** [e.g., None / API key / OAuth]
- **External APIs:** [e.g., None / OpenAI API / Google Calendar API]
- **Performance:** [e.g., All tool calls must respond in <500ms]
- **Offline-first:** [Yes / No]

---

## 6. Success Metrics

> How do we know the software is working well?

- [Metric 1 — e.g., "100% of user stories pass QA validation"]
- [Metric 2 — e.g., "All tool calls respond in under 500ms"]
- [Metric 3]

---

## 7. Out of Scope (v1)

> Features explicitly deferred to a future version.

- [Feature 1]
- [Feature 2]

---

## 8. Open Questions

> Unresolved decisions that must be answered before or during Phase 1.

| # | Question | Owner | Status |
|---|----------|-------|--------|
| 1 | [Question] | Human / Architect | Open |
| 2 | [Question] | Human | Open |

---

## 9. Approval

> This PRD must be approved by the human Product Owner before the Architect Agent begins Phase 1.

- [ ] **Human Product Owner Approved** — Date: ___________
