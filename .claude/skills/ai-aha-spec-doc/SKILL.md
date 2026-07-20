---
name: ai-aha-spec-doc
description: "Load specification-phase ('what') writing rules for AI ahaMatic. Use at the start of every specification ticket session to enforce spec-first, pyramid structure, and high-level writing standards. This is the spec-phase counterpart to ai-aha-design-doc."
---

```
## AI ahaMatic — Specification Session Rules

You are producing a specification ("what") artifact for the AI ahaMatic project. Apply these rules to every specification task in this project without exception.

Do not use the design-phase rules (`ai-aha-design-doc`) in this phase; this phase answers "what", never "how".

---

## Documentation Philosophy

- **Spec-first** — every document answers "what", not "how". Implementation details, technical decisions, and architectural choices belong in a separate phase.
- **Pyramid approach** — structure content from foundational to complex. Start with the broadest, most basic information and build toward specificity and complexity.
- **High-level only** — avoid granular details, code references, implementation steps, or technology-specific decisions unless the ticket explicitly requires them.
- **ahaMatic lens** — every document must reflect ahaMatic's nature as a **generic, multi-purpose software builder**, not a single-purpose platform. Never frame content around a specific domain (e.g., HRIS, CRM).

---

## Writing Rules

- No person's names — use role references only (e.g., "the project lead", "the team").
- No assumptions — if the ticket is unclear, ask before writing.
- No filler — every sentence must carry information. Remove preamble, redundancy, and padding.
- No commentary outside the deliverable — produce only what the ticket specifies.

---

## Structure Rules

- Use clear, consistent headings.
- Use tables where the ticket requires structured comparison or inventory.
- Use bullet points only for lists that are genuinely enumerable.
- Do not add sections, columns, or content beyond what the ticket specifies.

---

## Reference Rules

- `references/repos/` and `references/docs/` (the aging platform) are strictly read-only context; `references/research/` is authorized source material only where a ticket cites it.
- `docs/spec/` is the output destination for specification documents — write new files there unless the ticket specifies otherwise.
- Only read dependency docs explicitly listed in the ticket prompt.
```
