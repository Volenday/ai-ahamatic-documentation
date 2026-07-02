```markdown
# AI ahaMatic — Project Context

You are an AI development assistant working on **AI ahaMatic** — a clean rewrite of the aging ahaMatic platform using AI-driven development.

---

## What is ahaMatic

ahaMatic is a **generic, multi-purpose software builder platform**. It is not a single-purpose application (e.g., not an HRIS, CRM, or similar domain-specific tool). This distinction is critical — every output you produce must reflect ahaMatic's nature as a platform that can build and manage any type of software, not a tool designed for one domain.

---

## Project Folder Structure
```

ai-ahamatic/
├── CLAUDE.md
├── docs/ ← growing library (new AI ahaMatic outputs)
└── reference/ ← current ahaMatic (strictly read-only context)
└── repos/
└── docs/

```

### Folder Behavior

| Folder | Purpose | Behavior |
|---|---|---|
| `docs/` | New AI ahaMatic outputs | Read and build on — this is the growing library |
| `reference/` | Aging ahaMatic repos and docs | Read for context only — never reuse, copy, migrate, or adapt any content from here |

- Only read from `docs/` or `reference/` when explicitly instructed by the ticket prompt.
- Always write new outputs to `docs/` unless the ticket specifies otherwise.

---

## Project Rules

These rules apply to every session and every task without exception:

- **Atomic execution** — each session handles one ticket only. Do not scope beyond the current task.
- **Reference-only** — everything inside `reference/` represents the old ahaMatic platform. Do not reuse, copy, migrate, or adapt any existing content, logic, structure, or implementation details into new outputs.
- **No names** — do not include any person's name in any output. Use role references instead (e.g., "the project lead", "the team").
- **Spec-first** — documentation focuses on "what", not "how". Implementation details belong in a separate phase.
- **Pyramid approach** — structure all outputs from foundational to complex.
- **No assumptions** — if anything is unclear, ask before proceeding.

---

## Output Standards

- Deliver exactly what the ticket specifies — nothing more, nothing less.
- No commentary, preamble, or explanation outside the requested deliverable.
- All file outputs must be clean, production-ready, and saved with the filename specified in the ticket.
```
