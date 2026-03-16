# SiDLC Shared Conventions

> **All agents must read this file before beginning any phase.**
> These standards apply across all projects built with the SiDLC Framework.

---

## 1. Preferred Runtimes

| Language | Minimum Version | Use When |
|----------|-----------------|----------|
| TypeScript (Node.js) | Node.js 20+, TS 5+ | MCP servers, APIs, CLI tools |
| Python | 3.11+ | Data-heavy apps, scripts, ML pipelines |

Default to **TypeScript** unless the PRD specifies Python or the project is data/ML-heavy.

---

## 2. Preferred Libraries

### TypeScript
| Domain | Library |
|--------|---------|
| Database | `better-sqlite3` |
| Input validation | `zod` |
| MCP server | `@modelcontextprotocol/sdk` |
| HTTP server | `express` or `fastify` |
| Testing | `vitest` |
| Env vars | `dotenv` |
| Date/time | `date-fns` |

### Python
| Domain | Library |
|--------|---------|
| Database | `sqlite3` (stdlib) or `sqlalchemy` |
| Input validation | `pydantic` |
| Testing | `pytest` |
| Env vars | `python-dotenv` |
| Date/time | `datetime` (stdlib) |

---

## 3. Project Structure

```
project-root/
├── output/              # All generated artifacts and code go here!
│   ├── src/
│   │   ├── index.ts         # Server entry point
│   │   ├── db.ts            # Database connector
│   │   ├── tools/           # One file per MCP tool or API route
│   │   ├── services/        # Business logic (pure functions, no I/O)
│   │   └── utils/           # Shared utility functions
│   ├── tests/
│   │   └── *.test.ts        # Mirrors output/src/ structure
│   ├── scripts/             # Setup, migration, seed scripts
│   ├── .env.example         # Required env vars (no secrets)
│   ├── package.json
│   ├── tsconfig.json
│   └── SKILL.md             # Agent usage instructions (Integration Agent output)
├── .env                 # Ignored in version control
└── input/               # Sourced from the human/Intake Agent
    └── PRD.md
```

---

## 4. Code Style Rules

### TypeScript
- **No `any`** — use `unknown` and type-narrow properly
- **Named exports preferred** over default exports
- **Interface over type alias** for object shapes
- **Async/await** over raw Promises
- Functions must have explicit return types when non-trivial
- All database operations must be wrapped in try/catch

### Python
- Type hints required on all function signatures
- Use `dataclass` or `pydantic` models for structured data
- No bare `except:` — always catch specific exceptions

---

## 5. Error Handling Standards

- **Never throw untyped errors.** Always throw structured errors with a `code` and `message`.
- **Log errors with context:** include the function name, input params (sanitized), and timestamp.
- **All API/MCP tools must return a typed error response**, never crash the server.
- Define all possible error states in `output/technical-design.md` before implementing.

**TypeScript pattern:**
```ts
export class AppError extends Error {
  constructor(public code: string, message: string) {
    super(message);
    this.name = 'AppError';
  }
}
```

---

## 6. Testing Standards

- Every **service function** must have at least one unit test.
- Tests must cover: happy path, invalid input, edge cases (empty, null, out-of-range).
- Test files live in `output/tests/`, mirroring `output/src/` structure.
- Run tests with: `npx vitest run` (TS) or `pytest` (Python).

---

## 7. Environment Variables

- All required env vars must be documented in `.env.example`.
- Never hard-code secrets or file paths. Use `process.env` or `dotenv`.
- The Backend Agent is responsible for creating `.env.example`.

---

## 8. File & Symbol Naming

| Type | Convention | Example |
|------|-----------|---------|
| Files | `kebab-case` | `task-service.ts` |
| Classes | `PascalCase` | `TaskService` |
| Functions/vars | `camelCase` | `getTaskById` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRY_COUNT` |
| DB tables | `snake_case` | `task_items` |
| MCP tools | `snake_case` | `create_task` |

---

## 9. Version Control (if applicable)

Commit message format: `type(scope): short description`

| Type | When to use |
|------|------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `chore` | Setup, config, tooling |
| `docs` | Documentation only |
| `test` | Tests only |
| `refactor` | Code change, no behavior change |

Example: `feat(tasks): add recurring task scheduling logic`
