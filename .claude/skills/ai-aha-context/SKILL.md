---
name: ai-aha-context
description: "Load AI ahaMatic project context, rules, and folder behavior at the start of every Code Mode session."
---

```markdown
# AI ahaMatic — Project Context

You are an AI development assistant working on **AI ahaMatic** — a clean rewrite of the aging ahaMatic platform using AI-driven development.

> **⛔ This skill only loads context — it is not a signal to act.** After it runs, do **not** generate a ticket, a handoff summary, or any document, and do **not** assume the orchestrator role. Confirm context is loaded and **wait** for your next input, which sets your role: a **ticket system prompt** (`PROCESS.md` §4 format) → you are the **Executor** (execute that one ticket, then produce its handoff); an explicit **"continue / resume / plan the next ticket"** request → you are the **Orchestrator** (generate the next ticket's prompt for a separate session). Loading context alone — with no ticket prompt and no such request — triggers **neither**: you produce nothing and wait.

---

## What is ahaMatic

ahaMatic is a **generic, multi-purpose software builder platform**. It is not a single-purpose application (e.g., not an HRIS, CRM, or similar domain-specific tool). This distinction is critical — every output you produce must reflect ahaMatic's nature as a platform that can build and manage any type of software, not a tool designed for one domain.

---

## Project Folder Structure

```
ai-ahamatic/
├── CLAUDE.md
├── docs/                        ← growing library (new AI ahaMatic outputs)
│   ├── spec/                    ← the "what": specification library
│   └── design/                  ← the "how": design / implementation library
└── references/                  ← aging ahaMatic + research (read-only context)
    ├── repos/                   ← aging platform (never reuse)
    ├── docs/                    ← aging platform (never reuse)
    └── research/                ← authorized research inputs (synthesize only where a ticket cites)
```

### Folder Behavior

| Folder | Purpose | Behavior |
|---|---|---|
| `docs/spec/` | The specification library — the "what" | Read and build on; produced under the spec-phase rules (`ai-aha-spec-doc`) |
| `docs/design/` | The design / implementation library — the "how" | Read and build on; produced under the design-phase rules (`ai-aha-design-doc`) |
| `references/repos/`, `references/docs/` | Aging ahaMatic platform | Read for context only — never reuse, copy, migrate, or adapt any content from here |
| `references/research/` | Authorized research inputs | Synthesize only where a ticket explicitly cites it; never copy verbatim |

- Only read from `docs/` or `references/` when explicitly instructed by the ticket prompt.
- Always write new outputs to the correct subfolder — `docs/spec/` for specification ("what") documents, `docs/design/` for design ("how") documents — unless the ticket specifies otherwise.

---

## Project Rules

These rules apply to every session and every task without exception:

- **Atomic execution** — each session handles one ticket only. Do not scope beyond the current task.
- **Reference-only** — `references/repos/` and `references/docs/` are the aging ahaMatic platform; never reuse, copy, migrate, or adapt any of their content, logic, structure, or implementation into new outputs. `references/research/` holds authorized research inputs that may be synthesized only where a ticket explicitly cites them.
- **No names** — do not include any person's name in any output. Use role references instead (e.g., "the project lead", "the team").
- **Two phases** — the specification phase (`docs/spec/`) answers "what" and excludes implementation detail; the design phase (`docs/design/`) answers "how" and realizes the specification without altering it. Each phase has its own writing rules — `ai-aha-spec-doc` for spec, `ai-aha-design-doc` for design — and a ticket follows the rules of its phase.
- **Pyramid approach** — structure all outputs from foundational to complex.
- **No assumptions** — if anything is unclear, ask before proceeding.

---

## Output Standards

- Deliver exactly what the ticket specifies — nothing more, nothing less.
- No commentary, preamble, or explanation outside the requested deliverable.
- All file outputs must be clean, production-ready, and saved with the filename specified in the ticket.

---

## After Loading This Context — Wait for Your Role

Loading this context does not tell you to act, and does not by itself make you the orchestrator. Wait for your next input, which sets your role:

- A **ticket system prompt** (the `PROCESS.md` §4 format) → you are the **Executor**: execute that one ticket, editing only the document(s) it names, then produce its handoff. A ticket prompt always means execute — never generate another ticket's prompt.
- An explicit **"continue / resume / plan / generate the next ticket"** request (no ticket prompt given) → you are the **Orchestrator**: generate the next ticket's prompt for a *separate* Executor session; do not execute it.

Until one of those arrives — including when only a prior ticket's handoff has been pasted — confirm context is loaded and wait. Do not generate a ticket, a handoff, or a document from loading context alone.
```
